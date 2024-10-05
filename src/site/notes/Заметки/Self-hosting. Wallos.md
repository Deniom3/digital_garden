---
{"dg-publish":true,"permalink":"/zametki/self-hosting-wallos/","created":"2024-07-01","updated":"2024-09-24T23:27:51+03:00"}
---

Интересное приложение по отслеживанию расходов и отслеживанию подписок

Репозиторий: [GitHub - ellite/Wallos: Wallos: Open-Source Personal Subscription Tracker](https://github.com/ellite/Wallos)
> [!note]
> Особой пользы от программы нет, фактически позволяет учитывать только подписки без возможности занесения какой либо другой программы. Имеет смысл только если большое количество подписок у всей семьи.

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/wallos/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
version: '3.0'

services:
  wallos:
    container_name: wallos
    image: bellamy/wallos:latest
    ports:
      - "8282:80/tcp"
    environment:
      TZ: 'Europe/Moscow'
    # Volumes store your data between container upgrades
    volumes:
      - './db:/var/www/html/db'
      - '.logos:/var/www/html/images/uploads/logos'
    restart: unless-stopped
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Отслеживание финансов и подписок

> [!todo]-
> - [x] Попробовать развернуть у себя Wallos ⏬ ➕ 2024-07-07 ✅ 2024-07-15
