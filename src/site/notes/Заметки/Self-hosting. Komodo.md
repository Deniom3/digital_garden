---
{"dg-publish":true,"permalink":"/zametki/self-hosting-komodo/","tags":[""],"created":"2025-11-20 00:37","updated":"2025-11-27T00:12:24+03:00"}
---

Универсальный интерфейс управления docker и docker compose с гибкой возможностью управления и хранения исходных файлов, а так же возможностями автоматизации работы с контейнерами.

Репозиторий: https://github.com/moghtech/komodo

![Self-hosting. Komodo.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Komodo.png)
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/komodo/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
###################################
# 🦎 KOMODO COMPOSE - FERRETDB 🦎 #
###################################

## This compose file will deploy:
##   1. Postgres + FerretDB Mongo adapter (https://www.ferretdb.com)
##   2. Komodo Core
##   3. Komodo Periphery

services:
  postgres:
    # 🚨 Pin to a specific version. Updates can be breaking.
    # https://github.com/FerretDB/documentdb/pkgs/container/postgres-documentdb
    image: ghcr.io/ferretdb/postgres-documentdb
    labels:
      komodo.skip: # Prevent Komodo from stopping with StopAllContainers
    restart: unless-stopped
    # ports:
    #   - 5432:5432
    volumes:
      - postgres-data:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: ${KOMODO_DB_USERNAME}
      POSTGRES_PASSWORD: ${KOMODO_DB_PASSWORD}
      POSTGRES_DB: postgres

  ferretdb:
    # 🚨 Pin to a specific version. Updates can be breaking.
    # https://github.com/FerretDB/FerretDB/pkgs/container/ferretdb
    image: ghcr.io/ferretdb/ferretdb
    labels:
      komodo.skip: # Prevent Komodo from stopping with StopAllContainers
    restart: unless-stopped
    depends_on:
      - postgres
    # ports:
    #   - 27017:27017
    volumes:
      - ferretdb-state:/state
    environment:
      FERRETDB_POSTGRESQL_URL: postgres://${KOMODO_DB_USERNAME}:${KOMODO_DB_PASSWORD}@postgres:5432/postgres
  
  core:
    image: ghcr.io/moghtech/komodo-core:${COMPOSE_KOMODO_IMAGE_TAG:-latest}
    container_name: komodo-core
    labels:
      komodo.skip: # Prevent Komodo from stopping with StopAllContainers
    restart: unless-stopped
    depends_on:
      - ferretdb
    ports:
      - 9120:9120
    env_file: .env
    environment:
      KOMODO_DATABASE_ADDRESS: ferretdb:27017
      KOMODO_DATABASE_USERNAME: ${KOMODO_DB_USERNAME}
      KOMODO_DATABASE_PASSWORD: ${KOMODO_DB_PASSWORD}
    volumes:
      ## Store dated backups of the database - https://komo.do/docs/setup/backup
      - ${COMPOSE_KOMODO_BACKUPS_PATH}:/backups
      ## Store sync files on server
      # - /path/to/syncs:/syncs
      ## Optionally mount a custom core.config.toml
      # - /path/to/core.config.toml:/config/config.toml
    ## Allows for systemd Periphery connection at 
    ## "https://host.docker.internal:8120"
    # extra_hosts:
    #   - host.docker.internal:host-gateway

  ## Deploy Periphery container using this block,
  ## or deploy the Periphery binary with systemd using 
  ## https://github.com/moghtech/komodo/tree/main/scripts
  periphery:
    image: ghcr.io/moghtech/komodo-periphery:${COMPOSE_KOMODO_IMAGE_TAG:-latest}
    labels:
      komodo.skip: # Prevent Komodo from stopping with StopAllContainers
    restart: unless-stopped
    user: "1000:990"
    env_file: .env
    volumes:
      ## Mount external docker socket
      - /var/run/docker.sock:/var/run/docker.sock
      ## Allow Periphery to see processes outside of container
      - /proc:/proc
      ## Specify the Periphery agent root directory.
      ## Must be the same inside and outside the container,
      ## or docker will get confused. See https://github.com/moghtech/komodo/discussions/180.
      ## Default: /etc/komodo.
      - /home/deniom/docker:/home/deniom/docker
      - /home/deniom/docker/komodo/ssl:/etc/komodo/ssl

volumes:
  # Postgres
  postgres-data:
  # FerretDB
  ferretdb-state:
```




</div></div>



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/komodo-env/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
####################################
# 🦎 KOMODO COMPOSE - VARIABLES 🦎 #
####################################

## These compose variables can be used with all Komodo deployment options.
## Pass these variables to the compose up command using `--env-file komodo/compose.env`.
## Additionally, they are passed to both Komodo Core and Komodo Periphery with `env_file: ./compose.env`,
## so you can pass any additional environment variables to Core / Periphery directly in this file as well.

## Stick to a specific version, or use `latest`
COMPOSE_KOMODO_IMAGE_TAG=latest
## Store dated database backups on the host - https://komo.do/docs/setup/backup
COMPOSE_KOMODO_BACKUPS_PATH=/etc/komodo/backups

## DB credentials
KOMODO_DB_USERNAME=admin
KOMODO_DB_PASSWORD=admin

## Configure a secure passkey to authenticate between Core / Periphery.
KOMODO_PASSKEY=<key>

## Set your time zone for schedules
## https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
TZ=Europe/Moscow

#=-------------------------=#
#= Komodo Core Environment =#
#=-------------------------=#

## Full variable list + descriptions are available here:
## 🦎 https://github.com/moghtech/komodo/blob/main/config/core.config.toml 🦎

## Note. Secret variables also support `${VARIABLE}_FILE` syntax to pass docker compose secrets.
## Docs: https://docs.docker.com/compose/how-tos/use-secrets/#examples

## Used for Oauth / Webhook url suggestion / Caddy reverse proxy.
KOMODO_HOST=https://komodo.domein.ru
## Displayed in the browser tab.
KOMODO_TITLE=Komodo
## Create a server matching this address as the "first server".
## Use `https://host.docker.internal:8120` when using systemd-managed Periphery.
KOMODO_FIRST_SERVER=https://periphery:8120
## Give the first server a custom name.
KOMODO_FIRST_SERVER_NAME=Local
## Make all buttons just double-click, rather than the full confirmation dialog.
KOMODO_DISABLE_CONFIRM_DIALOG=false

## Rate Komodo polls your servers for
## status / container status / system stats / alerting.
## Options: 1-sec, 5-sec, 15-sec, 1-min, 5-min, 15-min
## Default: 15-sec
KOMODO_MONITORING_INTERVAL="15-sec"
## Interval at which to poll Resources for any updates / automated actions.
## Options: 15-min, 1-hr, 2-hr, 6-hr, 12-hr, 1-day
## Default: 1-hr
KOMODO_RESOURCE_POLL_INTERVAL="1-hr"

## Used to auth incoming webhooks. Alt: KOMODO_WEBHOOK_SECRET_FILE
KOMODO_WEBHOOK_SECRET=a_random_secret
## Used to generate jwt. Alt: KOMODO_JWT_SECRET_FILE
KOMODO_JWT_SECRET=a_random_jwt_secret
## Time to live for jwt tokens.
## Options: 1-hr, 12-hr, 1-day, 3-day, 1-wk, 2-wk
KOMODO_JWT_TTL="1-day"

## Enable login with username + password.
KOMODO_LOCAL_AUTH=true
## Set the initial admin username created upon first launch.
## Comment out to disable initial user creation,
## and create first user using signup button.
#KOMODO_INIT_ADMIN_USERNAME=admin
## Set the initial admin password
KOMODO_INIT_ADMIN_PASSWORD=changeme
## Disable new user signups.
KOMODO_DISABLE_USER_REGISTRATION=false
## All new logins are auto enabled
KOMODO_ENABLE_NEW_USERS=false
## Disable non-admins from creating new resources.
KOMODO_DISABLE_NON_ADMIN_CREATE=false
## Allows all users to have Read level access to all resources.
KOMODO_TRANSPARENT_MODE=false

## Prettier logging with empty lines between logs
KOMODO_LOGGING_PRETTY=false
## More human readable logging of startup config (multi-line)
KOMODO_PRETTY_STARTUP_CONFIG=false

## OIDC Login
KOMODO_OIDC_ENABLED=true
## Must reachable from Komodo Core container
KOMODO_OIDC_PROVIDER=https://authentik.domen.ru/application/o/komodo/
## Change the host to one reachable be reachable by users (optional if it is the same as above).
## DO NOT include the `path` part of the URL.
# KOMODO_OIDC_REDIRECT_HOST=https://oidc.provider.external
## Your OIDC client id
KOMODO_OIDC_CLIENT_ID=<authentik_id> # Alt: KOMODO_OIDC_CLIENT_ID_FILE
## Your OIDC client secret.
## If your provider supports PKCE flow, this can be ommitted.
KOMODO_OIDC_CLIENT_SECRET=<authentik_secret> # Alt: KOMODO_OIDC_CLIENT_SECRET_FILE
## Make usernames the full email.
## Note. This does not work for all OIDC providers.
# KOMODO_OIDC_USE_FULL_EMAIL=true
## Add additional trusted audiences for token claims verification.
## Supports comma separated list, and passing with _FILE (for compose secrets).
# KOMODO_OIDC_ADDITIONAL_AUDIENCES=abc,123 # Alt: KOMODO_OIDC_ADDITIONAL_AUDIENCES_FILE

## Github Oauth
KOMODO_GITHUB_OAUTH_ENABLED=false
# KOMODO_GITHUB_OAUTH_ID= # Alt: KOMODO_GITHUB_OAUTH_ID_FILE
# KOMODO_GITHUB_OAUTH_SECRET= # Alt: KOMODO_GITHUB_OAUTH_SECRET_FILE

## Google Oauth
KOMODO_GOOGLE_OAUTH_ENABLED=false
# KOMODO_GOOGLE_OAUTH_ID= # Alt: KOMODO_GOOGLE_OAUTH_ID_FILE
# KOMODO_GOOGLE_OAUTH_SECRET= # Alt: KOMODO_GOOGLE_OAUTH_SECRET_FILE

## Aws - Used to launch Builder instances.
KOMODO_AWS_ACCESS_KEY_ID= # Alt: KOMODO_AWS_ACCESS_KEY_ID_FILE
KOMODO_AWS_SECRET_ACCESS_KEY= # Alt: KOMODO_AWS_SECRET_ACCESS_KEY_FILE

#=------------------------------=#
#= Komodo Periphery Environment =#
#=------------------------------=#

## Full variable list + descriptions are available here:
## 🦎 https://github.com/moghtech/komodo/blob/main/config/periphery.config.toml 🦎

## Specify the root directory used by Periphery agent.
PERIPHERY_ROOT_DIRECTORY=/etc/komodo

## Periphery passkeys must include KOMODO_PASSKEY to authenticate.
PERIPHERY_PASSKEYS=${KOMODO_PASSKEY}

## Specify whether to disable the terminals feature
## and disallow remote shell access (inside the Periphery container).
PERIPHERY_DISABLE_TERMINALS=false

## Enable SSL using self signed certificates.
## Connect to Periphery at https://address:8120.
PERIPHERY_SSL_ENABLED=true

## If the disk size is overreporting, can use one of these to 
## whitelist / blacklist the disks to filter them, whichever is easier.
## Accepts comma separated list of paths.
## Usually whitelisting just /etc/hostname gives correct size.
PERIPHERY_INCLUDE_DISK_MOUNTS=/etc/hostname
# PERIPHERY_EXCLUDE_DISK_MOUNTS=/snap,/etc/repos

## Prettier logging with empty lines between logs
PERIPHERY_LOGGING_PRETTY=false
## More human readable logging of startup config (multi-line)
PERIPHERY_PRETTY_STARTUP_CONFIG=false
```

Основные параметры:
	`KOMODO_PASSKEY` - пароль авторизации для komodo agent
	`KOMODO_OIDC_PROVIDER` - ссылка на провайдера авторизации
	`KOMODO_OIDC_CLIENT_ID` - О2A клиент id
	`KOMODO_OIDC_CLIENT_SECRET` - клиент секрет

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Komodo\|Self-hosting. Komodo]]

> [!description]- Примечание
> Примечание::  Параметры env для komodo


</div></div>

## Подключение дополнительных серверов


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/komodo-agent/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
####################################
# 🦎 KOMODO COMPOSE - PERIPHERY 🦎 #
####################################

services:
  periphery:
    image: ghcr.io/moghtech/komodo-periphery:${COMPOSE_KOMODO_IMAGE_TAG:-latest}
    container_name: komodo-agent-${SERVER_NAME}
    # user добавляем для создания файлов под конкретным пользователем
    user: "${UID}:${DOCKER_GID}"
    labels:
      komodo.skip: # Prevent Komodo from stopping with StopAllContainers
    restart: unless-stopped
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /proc:/proc
      # Точка монтирования к папке с docker compose, путь должен полностью соответвствать в контейнере и хосте
      - ${PERIPHERY_DATA_DIR}:${PERIPHERY_DATA_DIR}
      # Папка хранения сертификата ssl создать от пользователя user, необходимо если указывать user
      - ${PERIPHERY_SSL_DIR}:/etc/komodo/ssl
    ports:
       - 8120:8120

```


</div></div>



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/komodo-agent-env/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
# Версия образа Komodo Periphery
COMPOSE_KOMODO_IMAGE_TAG=latest

# Имя сервера (любой понятный тебе идентификатор)
SERVER_NAME=monitoring

# первый UID это пользовательй - определить командой id
# второй DOCKER_GID группы docker - определить командой getent group docker
UID=1000
DOCKER_GID=999

# Каталог с данными docker-compose
PERIPHERY_DATA_DIR=/home/deniom/docker

# SSL-папка для Periphery
PERIPHERY_SSL_DIR=/home/deniom/docker/komodo-agent/ssl

# Корневой каталог перефирии (может быть изменён)
PERIPHERY_ROOT_DIRECTORY=/etc/komodo

# Ключ подключения из свойства KOMODO_PASSKEY komodo server
PERIPHERY_PASSKEYS=<ключ>

# Включить SSL
PERIPHERY_SSL_ENABLED=true
# Разрешить терминалы/доступ к shell
PERIPHERY_DISABLE_TERMINALS=false
# Какие точки монтирования учитывать при подсчёте дисков
PERIPHERY_INCLUDE_DISK_MOUNTS=/etc/hostname

```

Параметры:
	`SERVER_NAME` - имя сервера в среде komodo
	`UID` - id пользователя от чьего имени будут создаваться файлы
	`DOCKER_GID` - id группы docker для запуска под ограниченными правами
	`PERIPHERY_DATA_DIR` - основная папка хранения docker compose файлов
	`PERIPHERY_SSL_DIR` - папка хранения самовыпущенных сертификатов ssl, необходимо создавать под пользователем из UID до первого запуска контейнера, иначе будет ошибка.
	`PERIPHERY_PASSKEYS` - ключ авторизации между сервером и агентом, смотри параметр `KOMODO_PASSKEY`

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Komodo\|Self-hosting. Komodo]]

> [!description]- Примечание
> Примечание::  Параметры env для komodo agent


</div></div>


Полезное:
- [[Заметки/Добавление серверов в komodo\|Добавление серверов в komodo]]
- [[Заметки/Управление docker compose в komodo\|Управление docker compose в komodo]]
- [[Заметки/Уведомления из komodo в telegram\|Уведомления из komodo в telegram]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Docker compose оркестратор
