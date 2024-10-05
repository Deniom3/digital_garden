---
{"dg-publish":true,"dg-path":"Docker compose/Wallos.md","permalink":"/docker-compose/wallos/","updated":"2024-10-06T01:05:42+03:00"}
---


```yaml
version: '3.0'

services:
  wallos:
    container_name: wallos
    image: bellamy/wallos:latest
    ports:
      - "8282:80/tcp"
    environment:
      TZ: 'Europe/Moscow'
    # Volumes store your data between container upgrades
    volumes:
      - './db:/var/www/html/db'
      - '.logos:/var/www/html/images/uploads/logos'
    restart: unless-stopped
```

