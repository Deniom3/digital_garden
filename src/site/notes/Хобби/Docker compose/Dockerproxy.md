---
{"dg-publish":true,"dg-path":"Docker compose/Dockerproxy.md","permalink":"/docker-compose/dockerproxy/","updated":"2024-10-10T00:34:31+03:00"}
---


```yaml
services:
  dockerproxy:
    image: ghcr.io/tecnativa/docker-socket-proxy:latest
    container_name: dockerproxy
    environment:
      - INFO=1 # Для работы Crowdsek
      - CONTAINERS=1
      - SERVICES=1
      - TASKS=1
      - POST=0 # Disallow any POST operations (effectively read-only)
      - NETWORKS=1 # Alloy
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - 2375:2375
    restart: unless-stopped
networks: {}

```
