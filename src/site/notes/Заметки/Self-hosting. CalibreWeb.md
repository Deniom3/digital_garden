---
{"dg-publish":true,"permalink":"/zametki/self-hosting-calibre-web/","created":"2024-07-04 01:26","updated":"2024-10-09T19:53:55+03:00"}
---

Позволяет организовать личную библиотеку электронных книг с возможностью поделиться и разделения доступа по тегам.

Репозиторий: [GitHub - janeczku/calibre-web: :books: Web app for browsing, reading and downloading eBooks stored in a Calibre database](https://github.com/janeczku/calibre-web)
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





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
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Электронная библиотека книг