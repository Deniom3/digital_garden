---
{"dg-publish":true,"dg-path":"Docker compose/Alloy.md","permalink":"/docker-compose/alloy/","tags":[""],"updated":"2024-10-13T01:00:13+03:00"}
---


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
