---
{"dg-publish":true,"permalink":"/zametki/self-hosting-plex/","created":"2024-09-02 01:11","updated":"2025-06-10T02:09:00+03:00"}
---

Удобная и красивая программа для просмотра видео и отслеживания пожеланий. Имеет клиенты под любую платформу и поставляется бесплатно для серверов и компьютеров. Для мобильных устройств и телевизоров необходимо покупать подписку или единоразово купить доступ к приложению.

> [!hint]
> На 4pda в теме [4pda.to/forum/index.php?showtopic=225897&st=2020](https://4pda.to/forum/index.php?showtopic=225897&st=2020) можно скачать "взломанные" версии

Имеется возможность просмотра фильмов с удаленного сервера и шаринга для других пользователей даже без белого ip.

> [!important]
> При развертывании в локальном режиме выполняется авторизация на удаленных серверах plex.

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


Репозиторий сервера в докере: [GitHub - plexinc/pms-docker: Plex Media Server Docker repo, for all your PMS docker needs.](https://github.com/plexinc/pms-docker)

Основной сайт: [Stream Movies & Find Shows On The Best Streaming Services](https://www.plex.tv/)

> [!note]
> Проксирование через traefik или caddy необходимо выполнять по https на порт 32400 иначе видео будет идти не на прямую а через удаленные сервера plex.

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Media\|Сервер Media]]

> [!description]- Примечание
> Примечание:: Организация и просмотр медиафайлов
