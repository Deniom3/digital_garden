---
{"dg-publish":true,"permalink":"/zametki/monitoring-udalennogo-servera-v-grafana/","created":"2024-10-13 20:34","updated":"2024-10-31T15:47:30+03:00"}
---

Для организации мониторинга используется стек программ [[Заметки/Self-hosting. Nodeexpoeter\|Nodeexporter]] + [[Заметки/Self-hosting. Cadvisor\|Cadvisor]] + [[Заметки/Self-hosting. Alloy\|Alloy]]. А так же [[Заметки/Self-hosting. Uptime Kuma\|Uptime Kuma]] для внешнего мониторинга и уведомлений в случае потери связи с основным сервером. Сбор данных выполняет [[Заметки/Self-hosting. Prometheus\|Prometheus]] и [[Заметки/Self-hosting. Grafana Loki\|Loki]]. 

Для объединения и защиты стека базовой аутентификацией используется [[Заметки/Self-hosting. Caddy\|Caddy]]. Для связи с основным сервером мониторинга [[Заметки/Self-hosting. Prometheus\|Prometheus]] и [[Заметки/Self-hosting. Grafana Loki\|Loki]] можно использовать как прямую публикацию портов в интернете так и использование vpn сети. 

> [!bug]
> В текущем виде dockerproxy без based auth так как не удалось полноценно подключить alloy с базовой аутентификацией. Надо или доработать конфигурацию или поменять подход и на сервере удаленном запустить alloy а на локальном опубликовать в открытом виде api loki.

## Настройка удаленного сервера

В предложенной реализации Alloy подключается к удаленному [[Заметки/Self-hosting. Dockerproxy\|Dockerproxy]] для сбора логов, если это не требуется необходимо удалить публикацию порта 2375 в caddy, полное удаление из стека не рекомендуется так как используется для [[Заметки/Self-hosting. Uptime Kuma\|Uptime Kuma]].
### Пример docker compose

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/remote-server-monitoring/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  # Мониторинг хоста
  nodeexporter:
    image: prom/node-exporter:latest
    container_name: nodeexporter
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - --path.procfs=/host/proc
      - --path.rootfs=/rootfs
      - --path.sysfs=/host/sys
      - --collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($|/)
    restart: unless-stopped
    expose:
      - 9100
    networks:
      monitor-net:
        ipv4_address: 10.2.1.6
    labels:
      org.label-schema.group: monitoring
  # Мониторинг контейнеров
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    privileged: true
    devices:
      - /dev/kmsg:/dev/kmsg
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /cgroup:/cgroup:ro
    restart: unless-stopped
    expose:
      - 8080
    networks:
      monitor-net:
        ipv4_address: 10.2.1.2
    labels:
      org.label-schema.group: monitoring
  # Для Alloy и Uptime Kuma
  dockerproxy:
    image: ghcr.io/tecnativa/docker-socket-proxy:latest
    container_name: dockerproxy
    environment:
      - INFO=1
      - CONTAINERS=1 
      - SERVICES=1 
      - TASKS=1 
      - POST=0
      - NETWORKS=1 
    expose:
      - 2375
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped
    networks:
      monitor-net:
        ipv4_address: 10.2.1.3
    labels:
      org.label-schema.group: monitoring
  # Отслеживания доступности сервисов
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
      - ./uptime-kuma:/app/data
      - /etc/localtime:/etc/localtime:ro
    expose:
      - 3001
    restart: unless-stopped
    networks:
      monitor-net:
        ipv4_address: 10.2.1.4
    labels:
      org.label-schema.group: monitoring
  # Прокси
  caddy:
    image: caddy:latest
    container_name: caddy
    ports:
      - "3001:3001" # uptime-kuma
      - "8090:8090" # cadvisor
      - "9100:9100" # nodeexporter
      - "2375:2375" # dockerproxy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./Caddyfile:/etc/caddy/Caddyfile 
    environment:
      - ADMIN_USER=${ADMIN_USER:-admin}
      - ADMIN_PASSWORD_HASH=${ADMIN_PASSWORD_HASH:-$2a$14$1l.IozJx7xQRVmlkEQ32OeEEfP5mRxTpbDTCTcXRqn19gXD8YK1pO}
    restart: unless-stopped
    labels:
      org.label-schema.group: monitoring
    networks:
      monitor-net:
      private_network:
        ipv4_address: 10.2.0.130

networks:
  monitor-net:
    name: monitoring
    ipam:
      driver: default
      config:
        - subnet: 10.2.1.0/24
  private_network:
    name: dwg_private_network
    external: true
```


</div></div>


### Конфигурация Caddy

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/remote-server-monitoring-caddyfile/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
:3001 {
    reverse_proxy 10.2.1.4:3001  
}

:8090 {
    basicauth {
        {$ADMIN_USER} {$ADMIN_PASSWORD_HASH}
    }    
    reverse_proxy 10.2.1.2:8080
}

:2375 {
    reverse_proxy 10.2.1.3:2375
}

:9100 {
    basicauth {
        {$ADMIN_USER} {$ADMIN_PASSWORD_HASH}
    }
    reverse_proxy 10.2.1.6:9100
}

```

---
> [!urls]- Упоминания:

> [!description]- Примечание
> Примечание::  Параметры проксирования стека мониторинга


</div></div>


> [!note]
> По умолчанию если не задать параметры в `.env` для авторизации используются `admin:admin`

Для получения шифрованного пароля можно воспользоваться встроенным шифрованием caddy:

```shell
docker exec caddy caddy hash-password --plaintext <Password>
```

> [!attention] Важно
> При использовании в докере необходимо экранировать все символы `$` заменой на `$$` 

### Настройка Uptime Kuma

Для работы uptime kuma с docker необходимо настроить подключение к хосту для этого необходимо перейти в `Настройки` -> `Хосты Docker` -> `Настроить Docker Host`
![Мониторинг удаленного сервера в Grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%83%D0%B4%D0%B0%D0%BB%D0%B5%D0%BD%D0%BD%D0%BE%D0%B3%D0%BE%20%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0%20%D0%B2%20Grafana.png)
### Настройка VPN подключения

Для доступа к данным мониторинга необходимо подключить к VPN сети только Caddy путем добавления второй сети.

```
...
	networks:
      monitor-net:
      private_network:
        ipv4_address: 10.2.0.130

networks:
  monitor-net:
...
  private_network:
    name: private_network
    external: true
```

Все сервисы мониторинга будут доступны в vpn сети по адресу 10.2.0.130. 

> [!note]
> Стек тестировался в связке с wg на базе стека [dwg](https://github.com/DigneZzZ/dwg)

Для доступа между сетями docker при использовании ufw необходимо выполнить команды:

```sh
# Включение ufw для docker
sudo ufw default allow routed
sudo ufw allow in on docker0
sudo ufw allow out on docker0

# Открытия доступа между сетями
sudo ufw allow from 10.2.0.0/24 to 10.2.1.0/24
sudo ufw allow from 10.2.1.0/24 to 10.2.0.0/24
```
## Настройка основного сервера мониторинга

Для работы мониторинга [[Заметки/Self-hosting. Prometheus\|Prometheus]] и [[Заметки/Self-hosting. Alloy\|Alloy]] с сервера мониторинга должны иметь доступ к портам Caddy в vpn сети или до самого сервера если опубликовано в интернете.
### Пример конфигурации Prometheus для подключения мониторинга:
```yml
scrape_configs:
  - job_name: 'nodeexporter'
    scrape_interval: 5s
    basic_auth:
      username: admin
      password: admin
    static_configs:
      - targets: [ 10.2.0.130:9100]

  - job_name: 'cadvisor'
    scrape_interval: 5s
    basic_auth:
      username: admin
      password: admin
    static_configs:
      - targets: [ 10.2.0.130:8090]
```
### Настройки для Alloy

```
loki.relabel "remote" {
  forward_to = []

  rule {
    source_labels = ["__meta_docker_container_name"]
    regex         = "/(.*)"
    target_label  = "container_name"
  }

  rule {
    source_labels = ["__meta_docker_container_name"]
    regex         = "/(loki|alloy|dockerproxy|nodeexporter|cadvisor)"
    action        = "drop"
  }
}

discovery.docker "remote" {
  host = "tcp://10.2.0.130:2375"

}

loki.source.docker "remote" {
  host = "tcp://10.2.0.130:2375"
  targets    = discovery.docker.remote.targets
  labels     = {"app" = "docker", host = "remote" }
  
  relabel_rules = loki.relabel.remote.rules
  forward_to = [loki.write.local.receiver]
}

loki.write "local" {
    endpoint {
        url = "http://loki:3100/loki/api/v1/push"
    }
    external_labels = { alloy_host = "gateway" }
}
```

Про настройки подробно расписано в разделе [[Заметки/Сбор логов docker контейнеров для Grafana#Фильтрация и обработка логов\|фильтрация логов]].

## Настройка Grafana

За основу панели использована уже существующая панель [[Заметки/Self-hosting. Cadvisor#Мониторинг в Grafana\|панель]].

Шаблон панели:
![[Docker Containers and Logs.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Grafana\|Grafana]]