---
{"dg-publish":true,"dg-path":"Docker compose/Netbird Relay.md","permalink":"/docker-compose/netbird-relay/","tags":[""],"updated":"2025-10-27T02:19:41+03:00"}
---


```yaml
services:
  relay:
    image: netbirdio/relay:latest
    restart: unless-stopped
    container_name: netbird-relay
    environment:
    - NB_LOG_LEVEL=info
    - NB_LISTEN_ADDRESS=:33080
    - NB_EXPOSED_ADDRESS=domein.ru:33080
    # todo: change to a secure secret
    - NB_AUTH_SECRET=<secret>
    ports:
      - 33080:33080
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"
```
