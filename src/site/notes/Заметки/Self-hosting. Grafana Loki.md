---
{"dg-publish":true,"permalink":"/zametki/self-hosting-grafana-loki/","created":"2024-09-14 02:48","updated":"2024-10-09T19:53:47+03:00"}
---

Позволяет выполнять сбор данных логов для дальнейшего анализа с различных систем.

Официальный сайт: [Grafana Loki OSS | Log aggregation system](https://grafana.com/oss/loki/)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/loki/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  loki:
    image: grafana/loki:latest
    container_name: loki
    ports:
      - 3100:3100
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./loki-config.yml:/etc/loki/loki-config.yaml
      - loki_data:/tmp/loki/
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring

volumes:
  loki_data: {}
networks:
  monitoring:
    name: monitoring
    external: true
```


</div></div>


Файл конфигурации: [[Хобби/Конфиги/Loki\|Loki]]

Полезные ссылки:
- [Site Unreachable](https://blog.lrvt.de/log-visualization-with-grafana-loki-promtail/)

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Статьи/Настраиваем дашборд Grafana для точек доступа Keenetic\|Настраиваем дашборд Grafana для точек доступа Keenetic]]

> [!description]- Примечание
> Примечание:: Сбор и обработка логов

> [!todo]-
- [-] Настроить Loki для сборов логов Crowdsec ➕ 2024-10-09 ❌ 2024-10-09
- [x] Настроить Loki для сборов логов Keenetik ✅ 2024-10-08
- [/] Настроить Loki для сборов логов docker ➕ 2024-09-14
- [x] Изучить настройки Loki для сборов логов ➕ 2024-09-14 ✅ 2024-10-09
- [<] Настроить Loki для сборов логов OpenWRT ➕ 2024-09-14
- [<] Настроить Loki для сборов proxmox ➕ 2024-09-14