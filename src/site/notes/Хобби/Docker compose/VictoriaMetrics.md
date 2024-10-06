---
{"dg-publish":true,"dg-path":"Docker compose/VictoriaMetrics.md","permalink":"/docker-compose/victoria-metrics/","tags":[""],"updated":"2024-10-06T22:59:23+03:00"}
---


```yaml
services:
  victoria-metrics:
    image: victoriametrics/victoria-metrics
    container_name: victoria-metrics
    volumes:
      - ./victoria-metrics:/victoria-metrics-data
    ports:
      - 8428:8428
```
