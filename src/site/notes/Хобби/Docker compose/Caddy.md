---
{"dg-publish":true,"dg-path":"Docker compose/Caddy.md","permalink":"/docker-compose/caddy/","updated":"2024-10-06T00:57:55+03:00"}
---


```yml
services:
  caddy:
    image: ghcr.io/deniom3/caddy-cloudflare-transform:latest
    container_name: caddy
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
    ports:
      - 80:80
      - 443:443 
    environment:
      ACME_AGREE: true
      CLOUDFLARE_API_TOKEN: <CLOUDFLARE_API_TOKEN>
      CLOUDFLARE_EMAIL: <CLOUDFLARE_EMAIL>
      DOMAIN: deniom.ru
    volumes:
      - ./data:/data
      - ./config:/config
      - ./logs:/logs
      - ./Caddyfile:/etc/caddy/Caddyfile  
```

> [!note]-
> Подобно разобрано в [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|статье]]
> 
> Настройки проксирования указываются в [[Хобби/Конфиги/Caddyfile\|файле]]
