---
{"dg-publish":true,"permalink":"/zametki/uvedomleniya-iz-komodo-v-telegram/","created":"2025-11-27 00:11","updated":"2025-11-27T00:12:41+03:00"}
---

### Создание службы уведомлений

Перейти в `Settings` -> `Alerts` 
![Self-hosting. Komodo Telegram Alerter-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Komodo%20Telegram%20Alerter-1.png)

Заполнить `Endpoint` в режиме `Custom` указав ссылку на отправку уведомления в сервис. Все необходимые параметры для отправки передаются как параметры post запроса к сервису.

`http://<alerter-ip>:3000/alert?token=[[TELEGRAM_TOKEN]]&chat_id=[[TELEGRAM_CHAT_ID]]`
### Указать параметры запроса в секретах komodo
![Self-hosting. Komodo Telegram Alerter.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Komodo%20Telegram%20Alerter.png)

Параметры:
- `TELEGRAM_TOKEN` - токен телеграмм бота
- `TELEGRAM_CHAT_ID` - id чата куда отправлять уведомление
- `TELEGRAM_THREADS_ID` - id под темы супер группы, может быть упущен

### Дополнительные параметры

Можно указывать какие типы уведомлений будут обрабатываться а так же устанавливать для каждого стека отдельно использовать или не использовать фильтры уведомлений.
![Self-hosting. Komodo Telegram Alerter-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Komodo%20Telegram%20Alerter-2.png)


---
> [!urls]- Упоминания:
> - 