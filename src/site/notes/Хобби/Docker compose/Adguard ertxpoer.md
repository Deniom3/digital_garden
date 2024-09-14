---
{"dg-publish":true,"dg-path":"Docker compose/Adguard ertxpoer.md","permalink":"/docker-compose/adguard-ertxpoer/","updated":"2024-09-14T23:47:59+03:00"}
---


```yaml
services:
  adguard-ertxpoer:
    container_name: adguard-ertxpoer
    environment:
      - ADGUARD_SERVERS=http://192.168.0.1:8080
      - ADGUARD_USERNAMES=none
      - ADGUARD_PASSWORDS=none
    ports:
      - 9618:9618
    image: ghcr.io/henrywhitaker3/adguard-exporter:latest
```
