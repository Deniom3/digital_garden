---
{"dg-publish":true,"dg-path":"Docker compose/Komodo Agent.md","permalink":"/docker-compose/komodo-agent/","tags":[""],"updated":"2025-11-26T23:52:13+03:00"}
---


```yaml
####################################
# 🦎 KOMODO COMPOSE - PERIPHERY 🦎 #
####################################

services:
  periphery:
    image: ghcr.io/moghtech/komodo-periphery:${COMPOSE_KOMODO_IMAGE_TAG:-latest}
    container_name: komodo-agent-${SERVER_NAME}
    # user добавляем для создания файлов под конкретным пользователем
    user: "${UID}:${DOCKER_GID}"
    labels:
      komodo.skip: # Prevent Komodo from stopping with StopAllContainers
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /proc:/proc
      # Точка монтирования к папке с docker compose, путь должен полностью соответвствать в контейнере и хосте
      - ${PERIPHERY_DATA_DIR}:${PERIPHERY_DATA_DIR}
      # Папка хранения сертификата ssl создать от пользователя user, необходимо если указывать user
      - ${PERIPHERY_SSL_DIR}:/etc/komodo/ssl
    ports:
       - 8120:8120

```
