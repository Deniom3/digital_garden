---
{"dg-publish":true,"dg-path":"Docker compose/SmtpToTelegram.md","permalink":"/docker-compose/smtp-to-telegram/","updated":"2024-09-24T23:33:56+03:00"}
---

Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---

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