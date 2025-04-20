---
{"dg-publish":true,"dg-path":"Docker compose/Remnawave.md","permalink":"/docker-compose/remnawave/","tags":[""],"updated":"2025-04-20T04:24:14+03:00"}
---

```yaml
services:
    remnawave-db:
        image: postgres:17
        container_name: 'remnawave-db'
        hostname: remnawave-db
        restart: always
        env_file:
            - .env
        environment:
            - POSTGRES_USER=${POSTGRES_USER}
            - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
            - POSTGRES_DB=${POSTGRES_DB}
            - TZ=UTC
        ports:
            - 6767:5432
        volumes:
            - remnawave-db-data:/var/lib/postgresql/data
        networks:
            - remnawave-network
        healthcheck:
            test: ['CMD-SHELL', 'pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}']
            interval: 3s
            timeout: 10s
            retries: 3

    remnawave:
        image: remnawave/backend:dev
        container_name: 'remnawave'
        hostname: remnawave
        restart: always
        ports:
            - 3030:3000
            - 3001:3001
        env_file:
            - .env
        networks:
            - remnawave-network
        depends_on:
            remnawave-db:
                condition: service_healthy
            remnawave-redis:
                condition: service_healthy

    remnawave-subscription-page:
        image: remnawave/subscription-page:latest
        container_name: remnawave-subscription-page
        hostname: remnawave-subscription-page
        restart: always
        environment:
            - REMNAWAVE_PLAIN_DOMAIN=remnawave:3000
            - REQUEST_REMNAWAVE_SCHEME=http
            - SUBSCRIPTION_PAGE_PORT=3010
            - CUSTOM_SUB_PREFIX=sub
            - META_TITLE="Страница подписки"
            - META_DESCRIPTION="Ваша персональная страница подписки на VPN"
        ports:
            - 3010:3010
        networks:
            - remnawave-network

    remnawave-redis:
        image: valkey/valkey:8.0.2-alpine
        container_name: remnawave-redis
        hostname: remnawave-redis
        restart: always
        networks:
            - remnawave-network
        volumes:
            - remnawave-redis-data:/data
        healthcheck:
            test: ['CMD', 'valkey-cli', 'ping']
            interval: 3s
            timeout: 10s
            retries: 3

networks:
    remnawave-network:
        name: remnawave-network
        driver: bridge
        external: false

volumes:
    remnawave-db-data:
        driver: local
        external: false
        name: remnawave-db-data
    remnawave-redis-data:
        driver: local
        external: false
        name: remnawave-redis-data
```
