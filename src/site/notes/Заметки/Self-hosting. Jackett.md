---
{"dg-publish":true,"permalink":"/zametki/self-hosting-jackett/","created":"2024-09-02 01:20","updated":"2024-10-09T19:53:42+03:00"}
---

Универсальный индексатор торрент трекеров для поиска файлов по нескольким сайтам размещения торрентов.

Репозиторий: [GitHub - Jackett/Jackett: API Support for your favorite torrent trackers](https://github.com/Jackett/Jackett)

Докер файл развертывания 

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/jackett/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  jackett:
    image: lscr.io/linuxserver/jackett:latest
    container_name: jackett
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Moscow
      - AUTO_UPDATE=true #optional
    networks:
      - multimedia
    volumes:
      - /docker_config/jackett:/config
    ports:
      - 9117:9117
    restart: unless-stopped
```



</div></div>


> [!bug]
> Ведет себя очень не стабильно, не удобный интерфейс и во многом уступающий функционал. Отказался от него в пользу [[Заметки/Self-hosting. Prowlarr\|Prowlarr]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!description]- Примечание
> Примечание:: Индексатор торрент трекеров
