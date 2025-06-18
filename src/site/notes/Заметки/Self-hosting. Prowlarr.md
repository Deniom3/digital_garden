---
{"dg-publish":true,"permalink":"/zametki/self-hosting-prowlarr/","created":"2024-07-13 23:56","updated":"2025-06-10T02:09:36+03:00"}
---

Универсальный индексатор торрентов для поиска сразу по нескольким сайтам размещения торрентов. Реализован в рамках системы arr по аналогии с [[Заметки/Self-hosting. Sonarr\|Sonarr]] и [[Заметки/Self-hosting. Radarr\|Radarr]], основное преимущество перед [[Заметки/Self-hosting. Jackett\|jackett]] в том что автоматически интегрируется в arr приложения через api а так же имеет более удобный и функциональный интерфейс. Есть возможность поиска и запуска загрузки торрентов на прямую из индексатора.

Развернут в рамках стека 

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
services:
  Sonarr:
    image: ghcr.io/hotio/sonarr
    container_name: sonarr
    volumes:
      - /docker_config/sonarr:/config
      - /mnt/media:/data
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Europe/Moscow
    networks:
      - multimedia
    ports:
      - 8989:8989
    restart: unless-stopped
    
  Radarr:
    image: ghcr.io/hotio/radarr
    container_name: radarr
    volumes:
       - /docker_config/radarr:/config
       - /mnt/media:/data
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Europe/Moscow
    networks:
      - multimedia
    ports:
      - 7878:7878
    restart: unless-stopped

  Prowlarr:
    container_name: prowlarr
    image: ghcr.io/hotio/prowlarr
    ports:
      - 9696:9696
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Europe/Moscow
    volumes:
      - /docker_config/prowlarr:/config
    networks:
      - multimedia
    restart: unless-stopped

  Flaresolverr:
    image: ghcr.io/flaresolverr/flaresolverr:latest
    restart: unless-stopped
    environment:
      - LOG_LEVEL=info
      - TZ=Europe/Moscow
    ports:
      - 8191:8191
    networks:
      - multimedia
    container_name: flaresolverr
    
  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Moscow
      - WEBUI_PORT=8080
    volumes:
      - /docker_config/qbittorrent:/config
      - /mnt/media/torrents:/data/torrents
    networks:
      - multimedia
    ports:
      - 8080:8080
      - 6881:6881
      - 6881:6881/udp
    restart: unless-stopped
      
  Plex:
    image: lscr.io/linuxserver/plex:latest
    container_name: plex
    network_mode: host
    volumes:
      - /docker_config/plex:/config
      - /mnt/media:/data/media

    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
      - TZ=Europe/Moscow
    restart: unless-stopped

networks:
 multimedia:
   enable_ipv6: true
   ipam:
     config:
       - subnet: 2001:db8::/64
```


</div></div>


Репозиторий: [GitHub - Prowlarr/Prowlarr](https://github.com/Prowlarr/Prowlarr)

Документация по настройке: [Prowlarr | Servarr Wiki](https://wiki.servarr.com/prowlarr)

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Media\|Сервер Media]]
> - [[Заметки/Self-hosting. FlareSolverr\|Self-hosting. FlareSolverr]]

> [!description]- Примечание
> Примечание:: Индексатор торрент трекеров

> [!todo]-
> - [x] Необходимо попробовать заменить Jacket в связке Media ➕ 2024-07-14 ✅ 2024-08-30