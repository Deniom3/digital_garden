---
{"dg-publish":true,"dg-path":"Docker compose/WUD.md","permalink":"/docker-compose/wud/","tags":[""],"updated":"2025-05-07T18:11:16+03:00"}
---


```yaml
services:
  whatsupdocker:
    image: getwud/wud
    container_name: wud
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - 3006:3000
```
