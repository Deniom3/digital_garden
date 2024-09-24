---
{"dg-publish":true,"permalink":"/zametki/self-hosting-calibre-web/","created":"2024-07-04 01:26","updated":"2024-09-24T22:47:02+03:00"}
---

Позволяет организовать личную библиотеку электронных книг с возможностью поделиться и разделения доступа по тегам.

Репозиторий: [GitHub - janeczku/calibre-web: :books: Web app for browsing, reading and downloading eBooks stored in a Calibre database](https://github.com/janeczku/calibre-web)
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">




Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
```yml
name: calibre-web
services:
  calibre-web:
    image: linuxserver/calibre-web:latest
    container_name: calibre-web
    restart: unless-stopped
    environment:
      - DOCKER_MODS=ghcr.io/linuxserver/mods:universal-calibre
      - PGID=1000
      - PUID=1000
      - TZ=Europe/Moscow
    ports:
      - 8083:8083
    volumes:
      - ./config:/config
      - ./Books:/books
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Электронная библиотека книг