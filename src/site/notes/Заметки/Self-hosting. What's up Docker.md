---
{"dg-publish":true,"permalink":"/zametki/self-hosting-what-s-up-docker/","created":"2024-07-19 00:44","updated":"2025-05-07T19:23:21+03:00"}
---

Спорное решение по отслеживанию обновлений. Может быть применимо для случаев когда обновления выполняются в ручном режиме что бы отслеживать выход новых версий. В моей системе польза является сомнительной но в перспективе позволяет удобно отслеживать удаленные сервера и те программы которые не обновляются в автоматическом режиме.

Если необходимо просто автоматически обновлять все контейнеры не задумываясь проще использовать [[Заметки/Self-hosting. Watchtower\|Watchtower]] .

Для работы в виде клиент серверного сервиса используется [[Заметки/Self-hosting. Dockerproxy\|Dockerproxy]] , а для организации уведомлений в телеграмм [[Заметки/Self-hosting. Форк SmtpToTelegram\|Self-hosting. Форк SmtpToTelegram]].

> [!warning] Использование
> Пока использую в тестовом режиме, есть много нареканий к работе.

Репозиторий: [GitHub - fmartinou/whats-up-docker: What's up Docker ( aka WUD ) gets you notified when a new version of your Docker Container is available.](https://github.com/fmartinou/whats-up-docker)

Документация: [WUD](https://getwud.github.io/wud/#/)
### Пример docker файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/wud/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  whatsupdocker:
    image: getwud/wud
    container_name: wud
    restart: unless-stopped
    env_file:
      - .env
    ports:
      - 3006:3000
```


</div></div>


### Настройка переменных запуска

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/wud/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# WUD

</div>




```shell
# Общие настройки
TZ=Europe/Moscow
WUD_PUBLIC_URL=https://wud.domein.ru

# Настройки авторизации докер хаб
WUD_REGISTRY_HUB_PUBLIC_LOGIN=<login>
WUD_REGISTRY_HUB_PUBLIC_TOKEN=<token>

# Настройки авторизации через authentik
WUD_AUTH_OIDC_AUTHENTIK_CLIENTID=<id>
WUD_AUTH_OIDC_AUTHENTIK_CLIENTSECRET=<secret>
WUD_AUTH_OIDC_AUTHENTIK_DISCOVERY=https://authentik.domein.ru/application/o/wud/.well-known/openid-configuration
WUD_AUTH_OIDC_AUTHENTIK_REDIRECT=true

# Настройки обновления
WUD_TRIGGER_DOCKER_Update_AUTO=false
WUD_TRIGGER_DOCKER_Update_PRUNE=true

# Настройки отправки SMTP
WUD_TRIGGER_SMTP_SmtpToTelegram_HOST=192.168.0.132
WUD_TRIGGER_SMTP_SmtpToTelegram_PORT=2525
WUD_TRIGGER_SMTP_SmtpToTelegram_FROM=wud@domein.ru
WUD_TRIGGER_SMTP_SmtpToTelegram_TO=admin@domein.ru
# Настройки сообщений
#WUD_TRIGGER_SMTP_SmtpToTelegram_MODE=batch
WUD_TRIGGER_SMTP_SmtpToTelegram_ONCE=false
WUD_TRIGGER_SMTP_SmtpToTelegram_BATCHTITLE='Доступно обновление ${containers.length} контейнеров на сервере ${watcher}'
WUD_TRIGGER_SMTP_SmtpToTelegram_SIMPLEBODY='Контейнер "${container.name}" может быть обновлен с версии ${container.updateKind.kind} ${container.updateKind.localValue} до ${container.updateKind.kind} ${container.updateKind.remoteValue}${container.result && container.result.link ? "\\n" + container.result.link : ""}'
WUD_TRIGGER_SMTP_SmtpToTelegram_SIMPLETITLE='Новая версия обнаружена для контейнера ${container.name} на сервере ${watcher}'
# Gateway
WUD_WATCHER_Gateway_HOST=192.168.0.131
WUD_WATCHER_Gateway_CRON=0 6 * * *
# Main
WUD_WATCHER_Main_HOST=192.168.0.132
WUD_WATCHER_Main_CRON=0 6 * * *
# Nextcloud
WUD_WATCHER_Nextcloud_HOST=192.168.0.133
WUD_WATCHER_Nextcloud_CRON=0 6 * * *
# Media
WUD_WATCHER_Media_HOST=192.168.0.134
WUD_WATCHER_Media_CRON=0 6 * * *
# Monitoring
WUD_WATCHER_Monitoring_HOST=192.168.0.135
WUD_WATCHER_Monitoring_CRON=0 6 * * *
# Immich
WUD_WATCHER_Immich_HOST=192.168.0.136
WUD_WATCHER_Immich_CRON=0 6 * * *
```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. What's up Docker\|Self-hosting. What's up Docker]]

> [!description]- Примечание
> Примечание::  


</div></div>


### Настройка докер прокси для внешних хостов

Подключение внешних хостов: [WUD](https://getwud.github.io/wud/#/configuration/watchers/)

Для мониторинга удаленного хоста необходимо в [[Хобби/Docker compose/Dockerproxy\|Dockerproxy]] внести изменения дав разрешения на действия:
```
INFO=1 # Для работы Crowdsek
CONTAINERS=1 
SERVICES=1 
TASKS=1 
VERSION=1 # wud
IMAGES=1 # wud
POST=0 # Disallow any POST operations (effectively read-only)
NETWORKS=1 # Alloy
```

> [!attention] Важно
> Приведенная настройка предназначена для МОНИТОРИНГА обновлений и не позволяет вносить изменения и обновлять контейнеры.

Если есть необходимость обновлять через WUD необходимо так же изменить параметр `POST=1` и в докер контейнере изменить права на сокет:
```
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
```

> [!danger] Важно
> Если есть необходимость полноценного управления необходимо обязательно подключать tls авторизацию по ключам иначе любой сможет по открытому api оперировать докер контейнерами.

### Уведомления

Настройка уведомлений: [WUD](https://getwud.github.io/wud/#/configuration/triggers/)

> [!bug] Ошибка при работе с телеграмм
> Уведомления в телеграмм не поддерживают темы и суппер группы. Так же было замечено ряд проблем при отправки уведомлений. Лучше использовать smtp в связке с [[Заметки/Self-hosting. Форк SmtpToTelegram\|Self-hosting. Форк SmtpToTelegram]].

### Авторизация

Есть поддержка внешней авторизации. Подробнее про подключение внешней авторизации: [Integrate with What's Up Docker \| authentik](https://docs.goauthentik.io/integrations/services/whats-up-docker/)

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Отслеживание состояний docker контейнеров

