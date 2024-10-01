---
{"dg-publish":true,"permalink":"/zametki/self-hosting-keenetic-monitoring/","created":"2024-10-01 12:17","updated":"2024-10-01T20:41:05+03:00"}
---

Программа выполняет сбор данных о роутере [[Заметки/Роутеры Keenetic\|Keenetic]] предоставляемых на Rest API и преобразует их в формат Influx v2

Репозиторий: https://github.com/vitaliy-sk/keenetic-grafana-monitoring

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">




Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
```yaml
version: '3.7'
services:
  keenetic-monitoring:
    image: techh/keenetic-grafana-monitoring:latest
    container_name: keenetic-monitoring
    environment:
      - TZ=Europe/Moscow
    volumes:
      - /docker/keentic-monitoring/config.ini:/home/config/config.ini:ro
      # Optionally you can override metrics
      #- ./config/metrics.json:/home/config/metrics.json:ro
    restart: always
```


</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Статьи/Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа\|Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа]]

> [!info]-
> Примечание:: Сбор данных мониторинга с роутера Keenetik
