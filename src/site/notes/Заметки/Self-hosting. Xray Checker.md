---
{"dg-publish":true,"permalink":"/zametki/self-hosting-xray-checker/","tags":[""],"created":"2025-04-20 22:14","updated":"2025-06-10T02:17:15+03:00"}
---

Xray Checker — это инструмент для мониторинга доступности прокси-серверов с поддержкой протоколов VLESS, VMess, Trojan и Shadowsocks. Он автоматически проверяет подключения через Xray Core и предоставляет показатели для Prometheus, а также конечные точки API для интеграции с системами мониторинга.

Репозиторий: https://github.com/kutovoys/xray-checker

Документация: [Features \| Xray Checker](https://xray-checker.kutovoy.dev/intro/features/)
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/xray-checker/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




```yaml
services:
  xray-checker:
    image: kutovoys/xray-checker
    environment:
      - SUBSCRIPTION_URL=<ссылка на подписку>
      - PROXY_CHECK_METHOD=status
      - PROXY_STATUS_CHECK_URL=http://cp.cloudflare.com/generate_204
    ports:
      - 2112:2112
networks: {}
```


</div></div>


Подкючение к prometheus:
```
  - job_name: "xray-checker"
    metrics_path: "/metrics"
    static_configs:
      - targets: ["monitoring.local:2112"]
    scrape_interval: 1m
```

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]

> [!description]- Примечание
> Примечание:: Мониторинг доступности xray подключений
