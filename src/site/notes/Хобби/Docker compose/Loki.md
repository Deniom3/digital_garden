---
{"dg-publish":true,"dg-path":"Docker compose/Loki.md","permalink":"/docker-compose/loki/","tags":[""],"updated":"2024-10-09T00:53:22+03:00"}
---


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
