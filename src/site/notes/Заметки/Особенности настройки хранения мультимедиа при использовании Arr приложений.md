---
{"dg-publish":true,"permalink":"/zametki/osobennosti-nastrojki-hraneniya-multimedia-pri-ispolzovanii-arr-prilozhenij/","created":"2024-09-02 01:39","updated":"2024-09-03T16:37:55+03:00"}
---

Для корректной загрузки файлов с помощью программ стека Arr ([[Заметки/Self-hosting. Sonarr\|Sonarr]], [[Заметки/Self-hosting. Radarr\|Radarr]]) установленных в докере необходимо правильно выполнить настройку путей монтирования. Если этого не выполнить после каждой загрузки будет выполняться не перемещение в целевую папку а копирование что приведет к быстрому забиванию диска.

Пример докер файла в целом [[Хобби/Docker compose/Multimedia\|Multimedia]]

Подробно разобрано в статье [[Статьи/Рекомендация по настройки Sonnar Raddar в докере\|Рекомендация по настройки Sonnar Raddar в докере]]

---
Моя установка имеет следующие параметры настроек.

Структура папок:

```
/mnt/media/
├── torrents
└── media
    ├── movies
    └── shows

```

При этом точки монтирования обязательно должны быть такими что бы папки загрузки и хранения были одной файловой системой:

```
  qbittorrent:
    volumes:
      - /mnt/media/torrents:/data/torrents
  Radarr:
       - /mnt/media:/data
  Sonarr:
      - /mnt/media:/data
  Plex:
      - /mnt/media:/data/media
```

> [!danger] Важно
> Обязательно в контейнеры Arr необходимо выполнять монтирование корнего каталога в котором размещены и папка загрузки и папка хранения медиа иначе будет выполняться копирование.

Так же для всех контейнеров необходимо задать права на создаваемые файлы через переменные запуска:

```
    environment:
      - PUID=1000
      - PGID=1000
      - UMASK=002
```

> [!danger] Важно
>  Необходимо создать еще одного пользователя кроме root под которым будут создаваться все файлы в системе.

После запуска контейнеров необходимо выполнить настройку внутри приложений по инструкции ниже:

Примеры настроек в приложениях: [[Статьи/Примеры настройки хранения файлов для Sonarr Radarr\|Примеры настройки хранения файлов для Sonarr Radarr]]

---
> [!urls]- Упоминания:
> - 