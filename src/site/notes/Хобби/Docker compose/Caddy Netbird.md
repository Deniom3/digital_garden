---
{"dg-publish":true,"dg-path":"Docker compose/Caddy Netbird.md","permalink":"/docker-compose/caddy-netbird/","tags":[""],"updated":"2025-05-14T00:49:04+03:00"}
---


```yaml
services:
  caddy:
    image: caddy:latest
    container_name: caddy
    restart: unless-stopped
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - ./logs:/var/log/caddy
      - caddy_data_panel:/data
      - caddy_config_panel:/config
    env_file:
      - .env
    network_mode: "host"

volumes:
  caddy_data_panel:
  caddy_config_panel:
```

env
```
SELF_STEAL_DOMAIN=netberd.domein.ru
```