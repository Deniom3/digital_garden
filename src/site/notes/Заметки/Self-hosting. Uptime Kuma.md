---
{"dg-publish":true,"permalink":"/zametki/self-hosting-uptime-kuma/","created":"2024-09-08 02:40","updated":"2024-10-09T19:53:11+03:00"}
---

Система мониторинга сервисов на основании запросов позволяет организовать наблюдение и уведомления по статусу сервисов докер и веб сервисов.

Репозиторий: [GitHub - louislam/uptime-kuma: A fancy self-hosted monitoring tool](https://github.com/louislam/uptime-kuma)
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
services:
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
      - ./data:/app/data
      - /etc/localtime:/etc/localtime:ro
    ports:
      - 3001:3001
    restart: unless-stopped
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]

> [!description]- Примечание
> Примечание:: Мониторинг запущенных сервисов и сайтов
