---
{"dg-publish":true,"dg-path":"Docker compose/Monitoring.md","permalink":"/docker-compose/monitoring/","updated":"2024-09-11T01:07:57+03:00"}
---

Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---

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
      - 8080:8080
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
    driver: bridge

```

> [!note]
> Метка используется для отделения стека мониторинга в панелях мониторинга
> ```
>     labels:
>       org.label-schema.group: monitoring
> ```


