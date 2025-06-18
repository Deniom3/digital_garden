---
{"dg-publish":true,"permalink":"/zametki/self-hosting-influx-db/","created":"2024-07-03 23:19","updated":"2025-06-10T01:55:54+03:00"}
---

Система управления базами данных с открытым кодом на основе [[Заметки/База данных временных рядов\|базы данных временных рядов]]. Имеет удобный интерфейс для работы который позволяет в нем выполнять запросы и просматривать результаты, гибкую настройку хранения, возможность построения дашборда. Так же имеет конструктор для настройки [[Заметки/Self-hosting. Telegraf\|Telegraf]] с возможностью их хранения и передачи по сети.

Начиная с версии 2 использует язык запросов flux.

Официальный сайт: [InfluxDB Time Series Data Platform | InfluxData](https://www.influxdata.com/)

### Пример docker compose файла:
Для запуска стека мониторинга

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/monitoring/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  influxdb:
    image: influxdb:latest
    container_name: influxdb
    restart: always
    ports:
      - 8086:8086
    volumes:
      - ./influxdb/data:/var/lib/influxdb2
      - ./influxdb/config:/etc/influxdb2
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  grafana:
    image: grafana/grafana:latest
    container_name: grafana-server
    restart: always
    depends_on:
      - influxdb
    links:
      - influxdb
    ports:
      - 3000:3000
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/provisioning/datasources:/etc/grafana/provisioning/datasources
    environment:
      - GF_SERVER_ROOT_URL=${GF_SERVER_ROOT_URL}
      - GF_AUTH_GENERIC_OAUTH_ENABLED=false
      - GF_AUTH_GENERIC_OAUTH_NAME=${GF_AUTH_GENERIC_OAUTH_NAME}
      - GF_AUTH_GENERIC_OAUTH_CLIENT_ID=${GF_AUTH_GENERIC_OAUTH_CLIENT_ID}
      - GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET=${GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET}
      - GF_AUTH_GENERIC_OAUTH_SCOPES=${GF_AUTH_GENERIC_OAUTH_SCOPES}
      - GF_AUTH_GENERIC_OAUTH_AUTH_URL=${GF_AUTH_GENERIC_OAUTH_AUTH_URL}
      - GF_AUTH_GENERIC_OAUTH_TOKEN_URL=${GF_AUTH_GENERIC_OAUTH_TOKEN_URL}
      - GF_AUTH_GENERIC_OAUTH_API_URL=${GF_AUTH_GENERIC_OAUTH_API_URL}
      - GF_AUTH_SIGNOUT_REDIRECT_URL=${GF_AUTH_SIGNOUT_REDIRECT_URL}
      - GF_AUTH_OAUTH_AUTO_LOGIN=false
      - GF_AUTH_GENERIC_OAUTH_ROLE_ATTRIBUTE_PATH="contains(groups, 'Grafana
        Admins') && 'Admin' || contains(groups, 'Grafana Editors') && 'Editor'
        || 'Viewer'"
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    ports:
      - 9090:9090
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
      - --web.console.libraries=/etc/prometheus/console_libraries
      - --web.console.templates=/etc/prometheus/consoles
      - --storage.tsdb.retention.time=15d
      - --storage.tsdb.retention.size=10GB
      - --web.enable-lifecycle
    restart: unless-stopped
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  nodeexporter:
    image: prom/node-exporter:latest
    container_name: nodeexporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - --path.procfs=/host/proc
      - --path.rootfs=/rootfs
      - --path.sysfs=/host/sys
      - --collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($|/)
    restart: unless-stopped
    ports:
      - 9100:9100
    expose:
      - 9100
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    privileged: true
    devices:
      - /dev/kmsg:/dev/kmsg
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
      - /cgroup:/cgroup:ro #doesn't work on MacOS only for Linux
    restart: unless-stopped
    ports:
      - 8090:8080
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  pushgateway:
    image: prom/pushgateway:latest
    container_name: pushgateway
    restart: unless-stopped
    ports:
      - 9091:9091
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
volumes:
  grafana_data: {}
  prometheus_data: {}
networks:
  monitoring:
    name: monitoring
    driver: bridge
```

> [!note]
> Метка используется для отделения стека мониторинга в панелях мониторинга
> ```
>     labels:
>       org.label-schema.group: monitoring
> ```




</div></div>
 

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
>- [[Статьи/Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana\|Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana]]
>- [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]]
>- [[Статьи/Мониторинг роутеров openwrt в influxdb v2 и grafana\|Мониторинг роутеров openwrt в influxdb v2 и grafana]]
>- [[Статьи/Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа\|Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа]]

> [!description]- Примечание
> Примечание:: База данных временных рядов для хранения мониторинга

