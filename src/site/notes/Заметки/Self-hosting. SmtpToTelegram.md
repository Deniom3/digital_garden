---
{"dg-publish":true,"permalink":"/zametki/self-hosting-smtp-to-telegram/","created":"2024-09-01 00:56","updated":"2024-10-09T19:53:20+03:00"}
---

Для пересылки писем полученных по smtp в телеграмм группу можно использовать программу smtp to telegram. Это простое приложение которое позволяет легко переслать любое письмо в телеграмм группу. Предназначено только для внутреннего использования внутри локальной сети.

Репозиторий: [GitHub - KostyaEsmukov/smtp\_to\_telegram: A small program that listens for SMTP and sends all incoming Email messages to Telegram](https://github.com/KostyaEsmukov/smtp_to_telegram)

> [!fail]
> Основная версия не обновляется и не развивается. В данной версии не поддерживается отправка сообщений в каналы групп.

Развертывание форка с отправкой в каналы групп описано в [[Заметки/Self-hosting. Форк SmtpToTelegram\|Self-hosting. Форк SmtpToTelegram]]
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/smtp-to-telegram/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
version: '3'
services:
 SmtpToTelegram:
    container_name: SmtpToTelegram
    image: cobra1978/smtp_to_telegram:latest
    restart: unless-stopped
    environment:
      - ST_TELEGRAM_CHAT_IDS=<ChatID>
      - ST_TELEGRAM_BOT_TOKEN=<BotToken>
      - ST_TELEGRAM_MESSAGE_TEMPLATE={subject}\n\n{body}\n\n{attachments_details}
    ports:
      - 2525:2525
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!description]- Примечание
> Примечание:: Пересылка писем в телеграмм
