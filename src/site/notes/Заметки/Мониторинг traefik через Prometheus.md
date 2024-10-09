---
{"dg-publish":true,"permalink":"/zametki/monitoring-traefik-cherez-prometheus/","created":"2024-09-11 01:12","updated":"2024-09-14T23:52:34+03:00"}
---

[[Заметки/Self-hosting. Traefik\|Traefik]] поддерживает из коробки публикацию метрик в формате prometheus. В метриках доступна основная информация по работе прокси.

Документация по настройке: [Traefik Prometheus Documentation - Traefik](https://doc.traefik.io/traefik/observability/metrics/prometheus/)
## Настройка Traefik

Необходимо внести изменения в статическую конфигурацию `traefik.yml`

> [!note]
> Порт публикации может быть любой свободный, в данном случае 8082

```yaml
#Добавления точки публикации данных
entryPoints:
  metrics:
    address: ":8082"
    
#Включение публикации метрик
metrics:
  prometheus:
    entryPoint: metrics
```

Пример статической конфигурации:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/traefik/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Traefik статическая конфигурация

</div>




```yml
api:
  dashboard: true
  debug: true
entryPoints:
  http:
    address: ":80"
    http:
      middlewares:
        - crowdsec@file
      redirections:
        entryPoint:
          to: https
          scheme: https
  https:
    address: ":443"
    http:
      middlewares:
        - crowdsec@file
  metrics:
    address: ":8082"

serversTransport:
  insecureSkipVerify: true
providers:
  file:
    directory: /config
    watch: true
certificatesResolvers:
  cloudflare:
    acme:
      email: <email>@gmail.com
      storage: acme.json
      dnsChallenge:
        provider: cloudflare
        resolvers:
          - "1.1.1.1:53"
          - "1.0.0.1:53"
metrics:
  prometheus:
    entryPoint: metrics
log:
  level: "INFO"
  filePath: "/var/log/traefik/traefik.log"
accessLog:
  filePath: "/var/log/traefik/access.log"

experimental:
  plugins:
    crowdsec-bouncer-traefik-plugin:
      moduleName: "github.com/maxlerebourg/crowdsec-bouncer-traefik-plugin"
      version: "v1.3.4-beta1"
```


</div></div>


Внести изменения в докер файл добавив проброс порта публикации метрик из контейнера docker.

```yaml
...
    ports:
      - 8082:8082
...
```

Измененный файл docker compose для запуска 


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/traefik/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Traefik

</div>




Обязательно надо настроить файлы конфигурации [[Хобби/Конфиги/Traefik\|Traefik]]

```yaml
services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
      - 8082:8082
    environment:
      - CF_API_EMAIL=<CF_API_EMAIL>
      - CF_DNS_API_TOKEN=<CF_DNS_API_TOKEN>
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./traefik.yml:/traefik.yml:ro
      - ./acme.json:/acme.json
      - ./logs:/var/log/traefik
      - ./dynamic:/config:ro

networks:
  proxy:
    name: proxy
    external: true
```



</div></div>


## Настройка Prometheus

Добавить в конфигурационный файл [[Заметки/Self-hosting. Prometheus\|Prometheus]] новую задачу сбора данных:
```yaml
  - job_name: 'traefik'
    scrape_interval: 30s
    scheme: http
    metrics_path: '/metrics'
    static_configs:
      - targets: ['192.168.0.131:8082']:9100"]
```

Параметры являются не обязательными:
```
    scheme: http
    metrics_path: '/metrics'
```

## Мониторинг в Grafana

![Мониторинг traefik через Prometheus.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20traefik%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20Prometheus.png)

Можно использовать следующий дашборд для [[Заметки/Self-hosting. Grafana\|Grafana]]:
![[GrafanaTraefik.json]]

Описание дашборда на официальном сайте: [Traefik | Grafana Labs](https://grafana.com/grafana/dashboards/4475-traefik/)

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Traefik\|Traefik]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Заметки/Self-hosting. Prometheus\|Prometheus]]

> [!todo]-
> - [x] Изучить вопрос настройки ➕ 2024-09-11 ⏳ 2024-09-11 ✅ 2024-09-12
