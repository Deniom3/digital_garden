---
{"dg-publish":true,"dg-path":"Docker compose/SmtpToTelegram.md","permalink":"/docker-compose/smtp-to-telegram/","updated":"2024-10-06T01:02:38+03:00"}
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