---
{"dg-publish":true,"permalink":"/zametki/self-hosting-periphery/","tags":[""],"created":"2025-11-26 02:03","updated":"2025-11-26T02:05:00+03:00"}
---

Агент взаимодействия с удаленным сервером для [[Заметки/Self-hosting. Komodo\|Komodo]]

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/komodo-agent/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





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


</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]

> [!description]- Примечание
> Примечание:: Агент взаимодействия для komodo
