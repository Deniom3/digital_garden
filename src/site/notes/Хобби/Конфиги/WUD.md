---
{"dg-publish":true,"dg-path":"Конфиги/WUD.md","permalink":"/konfigi/wud/","updated":"2025-05-07T18:14:56+03:00"}
---


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
