---
{"dg-publish":true,"permalink":"/zametki/self-hosting-nextcloud/","created":"2024-07-03 23:30","updated":"2025-06-10T02:08:00+03:00"}
---

Комплекс докер программ для организации частного облака аналога Google документы имеет огромное количество плагинов и дополнительного софта от организации библиотеки фотографий до звонков и т.д.

Имеет WebDav клиент для работы с облаком который позволяет синхронизировать файлы на устройстве с облаком. Скачать можно тут: [Download and install Nextcloud](https://nextcloud.com/install/)
Единственный адекватный вариант запуска из всех мною опробованных это NextloudAIO. Устанавливается как комплекс docker контейнеров с отдельным интерфейсом администрирования и обновления. 

Репозиторий проекта: [GitHub - nextcloud/all-in-one: 📦 The official Nextcloud installation method. Provides easy deployment and maintenance with most features included in this one Nextcloud instance.](https://github.com/nextcloud/all-in-one)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/nextloud-aio/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  nextcloud-aio-mastercontainer:
    image: nextcloud/all-in-one:latest
    init: true
    restart: always
    container_name: nextcloud-aio-mastercontainer # This line is not allowed to be changed as otherwise AIO will not work correctly
    volumes:
      - nextcloud_aio_mastercontainer:/mnt/docker-aio-config # This line is not allowed to be changed as otherwise the built-in backup solution will not work
      - /var/run/docker.sock:/var/run/docker.sock:ro # May be changed on macOS, Windows or docker rootless. See the applicable documentation. If adjusting, don't forget to also set 'WATCHTOWER_DOCKER_SOCKET_PATH'!
    ports:
      #- 80:80
      - 8080:8080
      #- 8443:8443
    environment: # Is needed when using any of the options below
      - APACHE_PORT=11000 # Is needed when running behind a web server or reverse proxy (like Apache, Nginx, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      - APACHE_IP_BINDING=0.0.0.0 # Should be set when running behind a web server or reverse proxy (like Apache, Nginx, Cloudflare Tunnel and else) that is running on the same host. See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      - NEXTCLOUD_DATADIR=/mnt/nextcloud # Allows to set the host directory for Nextcloud's datadir. ⚠️⚠️⚠️ Warning: do not set or adjust this value after the initial Nextcloud installation is done! See https://github.com/nextcloud/all-in-one#how-to-change-the-default-location-of-nextclouds-datadir
      #- NEXTCLOUD_MOUNT=/mnt/ # Allows the Nextcloud container to access the chosen directory on the host. See https://github.com/nextcloud/all-in-one#how-to-allow-the-nextcloud-container-to-access-directories-on-the-host
      - NEXTCLOUD_UPLOAD_LIMIT=10G # Can be adjusted if you need more. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-upload-limit-for-nextcloud
      - NEXTCLOUD_MAX_TIME=3600 # Can be adjusted if you need more. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-max-execution-time-for-nextcloud
      - NEXTCLOUD_MEMORY_LIMIT=1024M # Can be adjusted if you need more. See https://github.com/nextcloud/all-in-one#how-to-adjust-the-php-memory-limit-for-nextcloud
      - NEXTCLOUD_STARTUP_APPS=twofactor_totp notes # Allows to modify the Nextcloud apps that are installed on starting AIO the first time. See https://github.com/nextcloud/all-in-one#how-to-change-the-nextcloud-apps-that-are-installed-on-the-first-startup
      - SKIP_DOMAIN_VALIDATION=true
      
volumes:
  nextcloud_aio_mastercontainer:
    name: nextcloud_aio_mastercontainer # This line is not allowed to be changed as otherwise the built-in backup solution will not work
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Nextcloud\|Сервер Nextcloud]]
> [[Статьи/Развертываем облачное хранилище NextCloud AIO на Windows в Docker для дома\|Развертываем облачное хранилище NextCloud AIO на Windows в Docker для дома]]

> [!description]- Примечание
> Примечание:: Персональное облако файлов