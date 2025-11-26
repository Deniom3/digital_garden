---
{"dg-publish":true,"permalink":"/zametki/self-hosting-komodo-telegram-alerter/","tags":[""],"created":"2025-11-26 23:54","updated":"2025-11-27T00:12:18+03:00"}
---

Легковесный сервис Telegram-уведомлений для системы мониторинга [[Заметки/Self-hosting. Komodo\|Komodo]]

> [!info] Примечание
> Это доработанный мною форк оригинального [проекта](https://github.com/SashaBusinaro/komodo-telegram-alerter) выполненные с использованием ИИ для разработки.

Репозиторий: https://github.com/Deniom3/komodo-telegram-alerter
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/komodo-telegram-alerter/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  komodo-telegram-alerter:
    container_name: komodo-telegram-alerter
    image: deniom3/komodo-telegram-alerter:latest
    restart: unless-stopped
    environment:
      - MESSAGE_LOG=true
    ports:
      - '3090:3000'
```


</div></div>


Можно переопределить шаблоны сообщений путем монтирования `templates.json` в докер образ при запуске.

Подробно про настройку в [[Заметки/Уведомления из komodo в telegram\|Уведомления из komodo в telegram]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Уведомления в телеграмм из komodo
