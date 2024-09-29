---
{"dg-publish":true,"permalink":"/stati/rekomendacziya-po-nastrojki-sonnar-raddar-v-dokere/","created":"2024-09-02-01-53-37","updated":"2024-09-29T16:21:28+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]
> [!urls]- Заметки по статье
> - 

---

Примечание

Я не собираюсь объяснять, как установить и запустить docker, мы только объясним, какую структуру папок мы рекомендуем.

Пути, упомянутые ниже, относятся к внутренним путям (или `Container Path`) контейнеров!

Внешние пути (или `Host Path`) зависят от того, куда вы подключили свой общий ресурс или свои диски.

Например, `/<path_to_data>/data` или даже `/data`.

## Структура папок[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#folder-structure "Постоянная ссылка")

Предупреждение

На самом деле не имеет значения, какой путь вы используете для своих медиа и данных приложения,

единственное, чего вам следует избегать, это `/home`.

Ожидается, что пользовательские папки в `/home` будут иметь некоторые ограничительные разрешения.

Это может привести к путанице с разрешениями, поэтому лучше просто полностью избегать этого.

В этом примере мы собираемся использовать общий ресурс с именем `data`.

В `data` папке есть подпапки для `torrents` и `usenet`, и в каждой из них есть подпапки для `tv`, `movie`, `books` и `music` загрузок, чтобы все было аккуратно. У `media` папки есть красиво названные `TV`, `Movies`, `Books` и `Music` вложенные папки, это ваша библиотека и то, что вы передадите в Plex, Emby или JellyFin.

*В этих примерах я специально использую нижний регистр для всех папок, поскольку Linux чувствителен к регистру.*

```
data
├── torrents
│   ├── books
│   ├── movies
│   ├── music
│   └── tv
├── usenet
│   ├── incomplete
│   └── complete
│       ├── books
│       ├── movies
│       ├── music
│       └── tv
└── media
    ├── books
    ├── movies
    ├── music
    └── tv

```

### Неправильное предложение пути[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#bad-path-suggestion "Постоянная ссылка")

Настройка пути по умолчанию, предлагаемая некоторыми разработчиками docker, которая побуждает людей использовать монтирования типа `/movies`, `/tv`, `/books` или `/downloads`, очень неоптимальна и делает их похожими на две или три файловые системы, даже если это не так (*из-за того, как работают тома Docker*). Это самый простой способ начать работу. Несмотря на простоту использования, у него есть существенный недостаток. В основном теряется возможность создания жестких ссылок или мгновенного перемещения, что приводит к более медленному и интенсивному вводу-выводу при использовании копирования + удаления.

### Разбивка структуры папок[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#breakdown-of-the-folder-structure "Постоянная ссылка")

#### Торрент-клиенты[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#torrent-clients "Постоянная ссылка")

qBittorrent, Потоп, ruTorrent

Причина, по которой мы используем `/data/torrents` для торрент-клиента, заключается в том, что ему нужен доступ только к торрент-файлам. В настройках программного обеспечения для торрентов вам нужно будет перенастроить пути, и вы сможете сортировать файлы по подпапкам, таким как `/data/torrents/{tv|movies|music}`.

```
data
└── torrents
    ├── books
    ├── movies
    ├── music
    └── tv

```

`Container Path:` => `/data/torrents/`

`Host Path:` => `/<path_to_data>/data/torrents/`

#### Клиенты Usenet[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#usenet-clients "Постоянная ссылка")

NZBGet или SABnzbd

Причина, по которой мы используем `/data/usenet` для клиента Usenet, заключается в том, что ему нужен доступ только к файлам Usenet. В настройках программного обеспечения Usenet вам нужно будет перенастроить пути, и вы сможете выполнять сортировку по вложенным папкам, таким как `/data/usenet/complete/{tv|movies|music}`.

```
data
└── usenet
    ├── incomplete
    └── complete
        ├── books
        ├── movies
        ├── music
        └── tv

```

`Container Path:` => `/data/usenet/`

`Host Path:` => `/<path_to_data>/data/usenet/`

#### The Starr Apps[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#the-starr-apps "Permanent link")

Sonarr, Radarr, Readarr and Lidarr

Sonarr, Radarr, Readarr and Lidarr gets access to everything using `/data` because the download folder(s) and media folder will look like and be one file system. Hardlinks will work and moves will be atomic, instead of copy + delete.

```
data
├── torrents
│   ├── books
│   ├── movies
│   ├── music
│   └── tv
├── usenet
│   ├── incomplete
│   └── complete
│       ├── books
│       ├── movies
│       ├── music
│       └── tv
└── media
    ├── books
    ├── movies
    ├── music
    └── tv

```

`Container Path:` => `/data`

`Host Path:` => `/<path_to_data>/data/`

#### Media Server[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#media-server "Permanent link")

Plex, Emby, JellyFin and Bazarr

Plex, Emby, JellyFin and Bazarr only needs access to your media library using `/data/media`, which can have any number of sub folders like Movies, Kids Movies, TV, Documentary TV and/or Music as sub folders.

```
data
└── media
    ├── movies
    ├── music
    ├── books
    └── tv

```

`Container Path:` => `/data/media`

`Host Path:` => `/<path_to_data>/data/media/`

---

**Don't forget to look at the [Examples](https://trash-guides.info/Hardlinks/Examples/) how to set up the paths inside the applications.**

### Permissions[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#permissions "Permanent link")

Recursively chown user and group and Recursively chmod to 775/664

```
sudo chown -R $USER:$USER /data
sudo chmod -R a=,a+rX,u+w,g+w /data

```

## Docker-compose Example[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#docker-compose-example "Permanent link")

This is a docker-compose example based on a default Ubuntu install.

The storage location used for the host is the same as in the container to make it easier to understand in this case `/data`.

The appdata (`/config`) will be stored on the host in the `/docker/appdata/{appname}`

docker-compose - \[Click to show/hide\]

```
version: "3.2"
services:
  radarr:
    container_name: radarr
    image: ghcr.io/hotio/radarr:latest
    restart: unless-stopped
    logging:
      driver: json-file
    ports:
      - 7878:7878
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /docker/appdata/radarr:/config
      - /data:/data
  sonarr:
    container_name: sonarr
    image: ghcr.io/hotio/sonarr:latest
    restart: unless-stopped
    logging:
      driver: json-file
    ports:
      - 8989:8989
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /docker/appdata/sonarr:/config
      - /data:/data
  bazarr:
    container_name: bazarr
    image: ghcr.io/hotio/bazarr:latest
    restart: unless-stopped
    logging:
      driver: json-file
    ports:
      - 6767:6767
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /docker/appdata/bazarr:/config
      - /data/media:/data/media
  sabnzbd:
    container_name: sabnzbd
    image: ghcr.io/hotio/sabnzbd:latest
    restart: unless-stopped
    logging:
      driver: json-file
    ports:
      - 8080:8080
      - 9090:9090
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /docker/appdata/sabnzbd:/config
      - /data/usenet:/data/usenet:rw

```

### Docker-Compose Commands[](https://trash-guides.info/Hardlinks/How-to-setup-for/Docker/#docker-compose-commands "Permanent link")

docker-compose commands - \[Click to show/hide\]

-   `sudo docker-compose up -d` (This Docker-compose command helps builds the image, then creates and starts Docker containers. The containers are from the services specified in the compose file. If the containers are already running and you run docker-compose up, it recreates the container.)
-   `sudo docker-compose pull` (Pulls an image associated with a service defined in a docker-compose.yml)
-   `sudo docker-compose down` (The Docker-compose down command also stops Docker containers like the stop command does. But it goes the extra mile. Docker-compose down, doesn’t just stop the containers, it also removes them.)
-   `sudo docker system prune -a --volumes --force` (Remove all unused containers, networks, images (both dangling and unreferenced), and optionally, volumes.)

---

Questions or Suggestions?

If you have questions or suggestions, click the button below to join our Discord server.

[Click For Support](https://trash-guides.info/discord)

[![[492590071455940612.idunno\|492590071455940612.idunno]]](https://trash-guides.info/discord)
