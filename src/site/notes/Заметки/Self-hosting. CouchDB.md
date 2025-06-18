---
{"dg-publish":true,"permalink":"/zametki/self-hosting-couch-db/","created":"2024-09-16 00:40","updated":"2025-06-10T01:51:33+03:00"}
---

Легковесная и простая база данных с веб интерфейсом. Используется для хранения чанков заметов обсидиан при организации синхронизации в дополнении [[Заметки/Дополнение Obsidian. Self-hosted LiveSync\|Дополнение Obsidian. Self-hosted LiveSync]]

Пример настройки docker compose:


<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
services:
  couchdb-obsidian-livesync:
    container_name: obsidian-livesync #shortened name
    image: couchdb:latest
    environment:
      - PUID=99
      - PGID=100
      - UMASK=0022
      - TZ=Europe/Moscow
      - COUCHDB_USER=$COUCHDB_USER
      - COUCHDB_PASSWORD=$COUCHDB_PASSWORD
    volumes:
      - ./data:/opt/couchdb/data
      - ./config/etc/local.d:/opt/couchdb/etc/local.d
    ports:
      - 5984:5984
    restart: unless-stopped
```



</div></div>


Репозиторий: [GitHub - apache/couchdb: Seamless multi-master syncing database with an intuitive HTTP/JSON API, designed for reliability](https://github.com/apache/couchdb)

Документация: [Overview — Apache CouchDB® 3.3 Documentation](https://docs.couchdb.org/en/stable/)

## Закрытие доступа через [[Заметки/Self-hosting. Authentik\|Authentik]]:

Для закрытия доступа к базе данных с помощью Authentik необходимо создать стандартную настройку проксирования и добавить в исключения конкретную базу.

Добавить запись в поле `Неаутентифицированные пути` для провайдера couchdb:
```
^/.*<database_name>/.*
```

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Легковесная база данных. Для синхронизации заметок Obsidian
