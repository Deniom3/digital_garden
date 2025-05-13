---
{"dg-publish":true,"permalink":"/zametki/self-hosting-remnawave/","created":"2025-04-20 04:18","updated":"2025-05-14T00:08:19+03:00"}
---

Мощный инструмент для управления прокси-серверами, созданный на основе [[Заметки/Ядро прокси Xray-core\|Xray-core]], с упором на простоту и удобство использования.

Репозиторий: https://github.com/remnawave/panel

Документация: [Remnawave Documentation](https://remna.st/)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/remnawave/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




```yaml
services:
    remnawave-db:
        image: postgres:17
        container_name: 'remnawave-db'
        hostname: remnawave-db
        restart: always
        env_file:
            - .env
        environment:
            - POSTGRES_USER=${POSTGRES_USER}
            - POSTGRES_PASSWORD=${POSTGRES_PASSWORD}
            - POSTGRES_DB=${POSTGRES_DB}
            - TZ=UTC
        ports:
            - 6767:5432
        volumes:
            - remnawave-db-data:/var/lib/postgresql/data
        networks:
            - remnawave-network
        healthcheck:
            test: ['CMD-SHELL', 'pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}']
            interval: 3s
            timeout: 10s
            retries: 3

    remnawave:
        image: remnawave/backend:dev
        container_name: 'remnawave'
        hostname: remnawave
        restart: always
        ports:
            - 3030:3000
            - 3001:3001
        env_file:
            - .env
        networks:
            - remnawave-network
        depends_on:
            remnawave-db:
                condition: service_healthy
            remnawave-redis:
                condition: service_healthy

    remnawave-subscription-page:
        image: remnawave/subscription-page:latest
        container_name: remnawave-subscription-page
        hostname: remnawave-subscription-page
        restart: always
        environment:
            - REMNAWAVE_PLAIN_DOMAIN=remnawave:3000
            - REQUEST_REMNAWAVE_SCHEME=http
            - SUBSCRIPTION_PAGE_PORT=3010
            - CUSTOM_SUB_PREFIX=sub
            - META_TITLE="Страница подписки"
            - META_DESCRIPTION="Ваша персональная страница подписки на VPN"
        ports:
            - 3010:3010
        networks:
            - remnawave-network

    remnawave-redis:
        image: valkey/valkey:8.0.2-alpine
        container_name: remnawave-redis
        hostname: remnawave-redis
        restart: always
        networks:
            - remnawave-network
        volumes:
            - remnawave-redis-data:/data
        healthcheck:
            test: ['CMD', 'valkey-cli', 'ping']
            interval: 3s
            timeout: 10s
            retries: 3

networks:
    remnawave-network:
        name: remnawave-network
        driver: bridge
        external: false

volumes:
    remnawave-db-data:
        driver: local
        external: false
        name: remnawave-db-data
    remnawave-redis-data:
        driver: local
        external: false
        name: remnawave-redis-data
```


</div></div>


### Настройка переменных запуска

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/peremennye-zapuska-remnaweve/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
### APP ###
APP_PORT=3000
METRICS_PORT=3001


### API ###
# Possible values: max (start instances on all cores), number (start instances on number of cores), -1 (start instances on all cores - 1)
# !!! Do not set this value more that physical cores count in your machine !!!
API_INSTANCES=1

### DATABASE ###
# FORMAT: postgresql://{user}:{password}@{host}:{port}/{database}
DATABASE_URL="postgresql://postgres:postgres@remnawave-db:5432/postgres"

### REDIS ###
REDIS_HOST=remnawave-redis
REDIS_PORT=6379

### JWT ###
### CHANGE DEFAULT VALUES ###
JWT_AUTH_SECRET=<secret>
JWT_API_TOKENS_SECRET=<secret>

### TELEGRAM ###
IS_TELEGRAM_NOTIFICATIONS_ENABLED=true
TELEGRAM_BOT_TOKEN=<secret>
TELEGRAM_NOTIFY_USERS_CHAT_ID=<secret>
TELEGRAM_NOTIFY_NODES_CHAT_ID=<secret>
TELEGRAM_NOTIFY_USERS_THREAD_ID=<secret>
TELEGRAM_NOTIFY_NODES_THREAD_ID=<secret>

### FRONT_END ###
FRONT_END_DOMAIN=panel.<domain>.ru

### SUBSCRIPTION PUBLIC DOMAIN ###
### RAW DOMAIN, WITHOUT HTTP/HTTPS, DO NOT PLACE / to end of domain ###
### Used in "profile-web-page-url" response header ###
SUB_PUBLIC_DOMAIN=sub.<domain>.ru/sub

### SWAGGER ###
SWAGGER_PATH=/docs
SCALAR_PATH=/scalar
IS_DOCS_ENABLED=true

### PROMETHEUS ###
### Metrics are available at /metrics
METRICS_USER=user
METRICS_PASS=password

### WEBHOOK ###
WEBHOOK_ENABLED=false
### Only https:// is allowed
WEBHOOK_URL=https://webhook.site/1234567890
### This secret is used to sign the webhook payload, must be exact 64 characters. Only a-z, 0-9, A-Z are allowed.
WEBHOOK_SECRET_HEADER=<secret>

### Database ###
### For Postgres Docker container ###
# NOT USED BY THE APP ITSELF
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
POSTGRES_DB=postgres

```

Для генерации новых секретных ключей воспользоваться командой:
```
openssl rand -hex 64
```

Параметры телеграмм для работы с супер группами доступны с версии 1.5.6 (dev) и являются не обязательными:
```
NODES_NOTIFY_THREAD_ID
TELEGRAM_ADMIN_THREAD_ID
```

> [!note] Postgres
> Параметры можно не менять так как контейнеры не выходят за пределы сети докер. 

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Remnawave\|Self-hosting. Remnawave]]

> [!description]- Примечание
> Примечание::  Настройки переменных Remnaweve


</div></div>


### Настройка проксирования и защиты authenik

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/traefik-remnaweve/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Для панели применяется в общем виде стандартный шаблон с защитой через authentik:
```yaml
http:
  routers:
    remnawave:
      rule: "Host(`panel.<domain>.ru`)"
      entrypoints:
        - http   
      middlewares:
        - remnawave-https-redirect
        - authentik
      service: remnawave
          
    remnawave-secure:
      rule: "Host(`panel.<domain>.ru`)"
      entrypoints:
        - https
      middlewares:
        - authentik
      tls: true
      service: remnawave

  middlewares:
    remnawave-https-redirect:
      redirectScheme:
        scheme: https

  services:
    remnawave:
      loadBalancer:
        servers:
          - url: "http://192.168.0.132:3030"

```

Для страницы подписки защита authentik не используется для свободного доступа пользователей:
```yaml
http:
  routers:
    sub-remnawave:
      rule: "Host(`sub.<domain>.ru`)"
      entrypoints:
        - http   
      middlewares:
        - sub-remnawave-https-redirect
      service: sub-remnawave
          
    sub-remnawave-secure:
      rule: "Host(`sub.<domain>.ru`)"
      entrypoints:
        - https
      tls: true
      service: sub-remnawave

  middlewares:
    sub-remnawave-https-redirect:
      redirectScheme:
        scheme: https

  services:
    sub-remnawave:
      loadBalancer:
        servers:
          - url: "http://192.168.0.132:3010"
```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Traefik\|Self-hosting. Traefik]]
> [[Заметки/Self-hosting. Remnawave\|Remnawave]]

> [!description]- Примечание
> Примечание::  Настройка проксирования для Remnaweve


</div></div>


### Настройки authentik

Если необходимо использовать страницу подписки для пользователей которые не авторизованы в authentik необходимо использовать два под домена. Один для панели второй для страницы подписки.

Для защиты панели через authentik можно применять стандартные настройки защиты через прокси traefik (см подробнее [[Заметки/Настройка защиты конкретного домена через authentik traefik\|Настройка защиты конкретного домена через authentik traefik]]). 

Для доступа к api необходимо добавить исключения путей. Перейти в настройки `Authentik > Provider > Advanced protocol settings > Unauthenticated Paths` и внести следующие значений:

```
^/api/.*
```

При такой настройке api будет исключен из защиты authentik как и страница подписки, а страница панели закрыта.
### Общие настройки панели:
1. Заполнить файл конфигурации для xray [[Хобби/Конфиги/Пример конфигурации xray\|Пример конфигурации xray]].
2.  [[Заметки/Self-hosting. Remnawave#Развертывание ноды\|Подключить новую ноду]]
3. Создать новый хост. Хост - это точка подключения пользователя, то что он видит в выборе в приложении. В поле адрес использовать домен а в поле порта порт из конфигурации инбаунда.
4. Создание пользователя. Для пользователя указываются инбаунды а не хосты, на выбор будут доступны все хосты свяханные с указанным инбаундом.

### Развертывание ноды:

Общий порядок установки описан в [документации](https://remna.st/installation/node/quick-start)  так же можно воспользоваться готовым скриптом:

```
sudo bash -c "$(curl -sL https://github.com/DigneZzZ/remnawave-scripts/raw/main/remnanode.sh)" @ install
```

Из репозитория: [GitHub - DigneZzZ/remnawave-scripts](https://github.com/DigneZzZ/remnawave-scripts)

> [!attention] Важно
> Подключение ноды к панели выполнять рекомендуется по ip адресу

При подключении ноды выбираются инбаунды указанные в файле конфигурации. Весь файл конфигурации передается на ноду, но работают только выбранные.

Для подключения ноды к панели используется ключ шифрования который можно скопировать при создании новой ноды в панели.

Про маскировку: [[Заметки/Маскировка reality под свой сайт\|Маскировка reality под свой сайт]]

### Страница подписки

Вывод страницы подписки осуществляется отдельным контейнером `remnawave/subscription-page` настройки выполняются в панели. Можно изменить шаблон, предлагаемые приложения и сообщения.

Документация: [Remnawave Documentation](https://remna.st/subscription-templating/installation)

### Мониторинг

Панель имеет метрики [[Заметки/Self-hosting. Prometheus\|Prometheus]] для сбора данных мониторинга доступны по умолчанию по адресу `remnawave:3001/metrics`

Пример подключения метрик для prometheus:
```
  - job_name: 'remnawave'
    scrape_interval: 5s
    metrics_path: /metrics
    static_configs:
      - targets: ['panel:3001']
    basic_auth:
      username: username
      password: password
```

Панель для графаны: <в процессе настройки>

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Панель управления VPN
