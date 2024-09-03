---
{"dg-publish":true,"permalink":"/zametki/programma-avtomaticheskogo-obnovleniya-docker-watchtower/","created":"2024-07-11 21:10"}
---

Программа для автоматического обновления docker контейнеров, выполняет отслеживание выхода обновлений и выполняет обновление с уведомлениями например в телеграмм.

Для исключения контейнера из автоматического обновления в исключаемый контейнер необходимо добавить:
```yaml
    labels:
      - "com.centurylinklabs.watchtower.enable=false"
```

Подробная документация: [Watchtower](https://containrrr.dev/watchtower/) 

Docker compose для запуска [[Хобби/Docker compose/Watchtower\|Watchtower]]

Настройка уведомлений в телеграмм: [[Заметки/Отправка сообщения из Watchrower в тему супергруппы телеграмм\|Отправка сообщения из Watchrower в тему супергруппы телеграмм]]

---
> [!urls]- Упоминания:
> - 