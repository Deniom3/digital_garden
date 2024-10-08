---
{"dg-publish":true,"dg-path":"Docker compose/Alloy.md","permalink":"/docker-compose/alloy/","tags":[""],"updated":"2024-10-09T01:12:52+03:00"}
---


```yaml
  alloy:
    image: grafana/alloy:latest
    container_name: alloy
    volumes:
      - ./config.alloy:/etc/alloy/config.alloy
      - /var/run/docker.sock:/var/run/docker.sock:ro
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

networks:
  monitoring:
    name: monitoring
    external: true
```
