---
{"dg-publish":true,"dg-path":"Docker compose/Komodo Telegram Alerter.md","permalink":"/docker-compose/komodo-telegram-alerter/","tags":[""]}
---


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
