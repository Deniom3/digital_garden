---
{"dg-publish":true,"dg-path":"Docker compose/Portainer Agent.md","permalink":"/docker-compose/portainer-agent/","tags":[""]}
---


```yaml
services:
  agent:
    ports:
      - 9001:9001
    container_name: portainer_agent
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
      - /:/host
    image: portainer/agent:latest
```
