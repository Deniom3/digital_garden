---
{"dg-publish":true,"dg-path":"Docker compose/Mosquitto.md","permalink":"/docker-compose/mosquitto/","updated":"2024-10-06T01:00:44+03:00"}
---


```yaml
services:
  mosquitto:
    container_name: mosquitto
    image: eclipse-mosquitto:1.5.11
    restart: unless-stopped
    privileged: true
    volumes:
      - ./config:/mosquitto/config
      - ./data:/mosquitto/data
      - ./log:/mosquitto/log
      - ./certs:/mosquitto/certs
    environment:
      - TZ=Europe/Moscow
    network_mode: host
    logging:
      driver: none

```
