---
{"dg-publish":true,"dg-path":"Docker compose/Dockerproxy.md","permalink":"/docker-compose/dockerproxy/","updated":"2024-10-06T00:58:41+03:00"}
---


```yaml
services:
  dockerproxy:
    image: ghcr.io/tecnativa/docker-socket-proxy:latest
    container_name: dockerproxy
    environment:
	  - INFO=1 # Для работы Crowdsek
      - CONTAINERS=1 # Allow access to viewing containers
      - SERVICES=1 # Allow access to viewing services (necessary when using Docker Swarm)
      - TASKS=1 # Allow access to viewing tasks (necessary when using Docker Swarm)
      - POST=0 # Disallow any POST operations (effectively read-only)
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro # Mounted as read-only
    ports:
      - 2375:2375
    restart: unless-stopped
networks: {}

```
