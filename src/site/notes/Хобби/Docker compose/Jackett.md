---
{"dg-publish":true,"dg-path":"Docker compose/Jackett.md","permalink":"/docker-compose/jackett/","updated":"2024-10-06T00:59:52+03:00"}
---


```yaml
services:
  jackett:
    image: lscr.io/linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Moscow
      - AUTO_UPDATE=true #optional
    networks:
      - multimedia
    volumes:
      - /docker_config/jackett:/config
    ports:
      - 9117:9117
    restart: unless-stopped
```

