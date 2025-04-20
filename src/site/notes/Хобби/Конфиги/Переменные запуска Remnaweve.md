---
{"dg-publish":true,"dg-path":"Конфиги/Переменные запуска Remnaweve.md","permalink":"/konfigi/peremennye-zapuska-remnaweve/","tags":[""],"updated":"2025-04-20T22:20:13+03:00"}
---


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
IS_TELEGRAM_ENABLED=true
TELEGRAM_BOT_TOKEN=<secret>
TELEGRAM_ADMIN_ID=<secret>
NODES_NOTIFY_CHAT_ID=<secret>
NODES_NOTIFY_THREAD_ID=<secret>
TELEGRAM_ADMIN_THREAD_ID=<secret>

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
