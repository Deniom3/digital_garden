---
{"dg-publish":true,"permalink":"/zametki/self-hosting-dockerproxy/","created":"2024-10-01 12:01","updated":"2024-10-01T20:39:13+03:00"}
---

Позволяет получить удаленный доступ к данным docker для управления и получения сведений о контейнерах.

Репозиторий: https://github.com/Tecnativa/docker-socket-proxy

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/dockerproxy/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




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


</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]
> - [[Хобби/Домашняя лаборатория/Сервер Media\|Сервер Media]]
> - [[Хобби/Домашняя лаборатория/Сервер Immich\|Сервер Immich]]
> - [[Хобби/Домашняя лаборатория/Сервер Nextcloud\|Сервер Nextcloud]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]
> - [[Хобби/Домашняя лаборатория/Сервер Armbian\|Сервер Armbian]]

> [!info]-
> Примечание:: Удаленное подключение к сокету docker
