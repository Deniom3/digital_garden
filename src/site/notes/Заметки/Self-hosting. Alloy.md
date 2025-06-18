---
{"dg-publish":true,"permalink":"/zametki/self-hosting-alloy/","created":"2024-10-09 01:10","updated":"2025-06-10T01:44:24+03:00"}
---

Программа из стека [[Заметки/Self-hosting. Grafana\|Grafana]] предназначена для сбора и обработки логов с разных источников и передачи их в Loki.

Репозиторий: https://github.com/grafana/alloy
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/alloy/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  alloy:
    image: grafana/alloy:latest
    container_name: alloy
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./config.alloy:/etc/alloy/config.alloy
    ports:
      - 12345:12345
      - 1514:1514/tcp
      - 1514:1514/udp
    command: run --server.http.listen-addr=0.0.0.0:12345
      --storage.path=/var/lib/alloy/data /etc/alloy/config.alloy
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
    restart: unless-stopped

networks:
  monitoring:
    name: monitoring
    external: true
```


</div></div>


Настройка:
- [[Заметки/Сбор логов с роутеров Keenetic и OpenWrt для Grafana\|Сбор логов с роутеров Keenetic и OpenWrt для Grafana]]
- [[Заметки/Сбор логов linux и proxmox для Grafana\|Сбор логов linux и proxmox для Grafana]]
- [[Заметки/Сбор логов docker контейнеров для Grafana\|Сбор логов docker контейнеров для Grafana]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
 > - [[Заметки/Self-hosting. Grafana Loki\|Self-hosting. Grafana Loki]]

> [!description]- Примечание
> Примечание:: Сбор и обработка данных логов
