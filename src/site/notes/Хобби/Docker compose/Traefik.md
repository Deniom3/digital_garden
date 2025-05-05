---
{"dg-publish":true,"dg-path":"Docker compose/Traefik.md","permalink":"/docker-compose/traefik/","updated":"2025-05-05T18:23:28+03:00"}
---


Обязательно надо настроить файлы конфигурации [[Хобби/Конфиги/Traefik\|Traefik]]

```yaml
services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: always
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
      - 8082:8082
      - 22:22
    environment:
      - CF_API_EMAIL=$CF_API_EMAIL
      - CF_DNS_API_TOKEN=$CF_DNS_API_TOKEN
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./traefik.yml:/traefik.yml:ro
      - ./acme.json:/acme.json
      - ./logs:/var/log/traefik
      - ./dynamic:/config:ro
networks:
  proxy:
    name: proxy
    external: true

```

