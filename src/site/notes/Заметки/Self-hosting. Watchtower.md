---
{"dg-publish":true,"permalink":"/zametki/self-hosting-watchtower/","created":"2024-07-11 21:10","updated":"2024-10-01T20:15:50+03:00"}
---

Программа для автоматического обновления docker контейнеров, выполняет отслеживание выхода обновлений и выполняет обновление с уведомлениями например в телеграмм.

> [!note]
> Для исключения контейнера из автоматического обновления в исключаемый контейнер необходимо добавить:
> ```yaml
>     labels:
>       - "com.centurylinklabs.watchtower.enable=false"
> ```
> 


Подробная документация: [Watchtower](https://containrrr.dev/watchtower/) 

Настройка уведомлений в телеграмм: [[Заметки/Отправка сообщения из Watchrower в тему супергруппы телеграмм\|Отправка сообщения из Watchrower в тему супергруппы телеграмм]]
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Watchtower

</div>



Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---

```yaml
version: "3"
services:
  watchtower:
    image: containrrr/watchtower:latest
    container_name: watchtower
    environment:
      - WATCHTOWER_NOTIFICATIONS_HOSTNAME=<ServerName>
      - WATCHTOWER_NOTIFICATIONS=shoutrrr
      - WATCHTOWER_NOTIFICATION_URL=<TelegrammToken>
      - TZ=Europe/Moscow
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    restart: unless-stopped
    command: --rolling-restart --cleanup --schedule "0 0 3 * * *"
```

`<ServerName\>` - имя сервера которые будет использоваться в уведомления
`<TelegrammToken\>` - токен телеграмм бота
`"0 0 3 * * *"` - проверка выполняется каждый день в три часа ночи

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]
> - [[Хобби/Домашняя лаборатория/Сервер Media\|Сервер Media]]
> - [[Хобби/Домашняя лаборатория/Сервер Immich\|Сервер Immich]]
> - [[Хобби/Домашняя лаборатория/Сервер Nextcloud\|Сервер Nextcloud]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]

> [!info]-
> Примечание:: Автоматическое отслеживание и установка обновлений Docker
