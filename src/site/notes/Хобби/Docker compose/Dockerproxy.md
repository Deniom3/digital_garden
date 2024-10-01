---
{"dg-publish":true,"dg-path":"Docker compose/Dockerproxy.md","permalink":"/docker-compose/dockerproxy/","tags":[""],"updated":"2024-10-01T12:04:13+03:00"}
---

Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---

```yaml
services:
  dockerproxy:
    image: ghcr.io/tecnativa/docker-socket-proxy:latest
    container_name: dockerproxy
    environment:
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
