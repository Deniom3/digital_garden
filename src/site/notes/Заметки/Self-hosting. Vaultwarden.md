---
{"dg-publish":true,"permalink":"/zametki/self-hosting-vaultwarden/","created":"2024-07-03 19:56","updated":"2024-10-01T20:37:31+03:00"}
---

Удобный Self-hosting менеджер паролей, не официальный форк менеджера паролей Bitwarden для личной установки. Совместим со всеми версиями Bitwarden приложения. 

Для работы обязательно требуется использовать https соединений через реверс прокси.

Для быстрого получения обновлений через пуш уведомления необходимо дополнительно настроить [[Статьи/Включение push-уведомлений мобильного клиента Vaultwarden\|Включение push-уведомлений мобильного клиента Vaultwarden]]

Репозиторий проекта: [GitHub - dani-garcia/vaultwarden: Unofficial Bitwarden compatible server written in Rust, formerly known as bitwarden\_rs](https://github.com/dani-garcia/vaultwarden)

Сейчас идет бета тест нового приложения [About the Beta Program - User Research / Beta Programs - Bitwarden Community Forums](https://community.bitwarden.com/t/about-the-beta-program/39185/57)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/vaultwarden/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    network_mode: bridge
    environment:
      - TZ=Europe/Moscow
      - ADMIN_TOKEN=${VAULTWARDEN_ADMIN_TOKEN}
      - PUSH_ENABLED=true
      - PUSH_IDENTITY_URI=https://identity.bitwarden.eu
      - PUSH_INSTALLATION_ID=<PUSH_INSTALLATION_ID>
      - PUSH_INSTALLATION_KEY=<PUSH_INSTALLATION_KEY>
      - PUSH_RELAY_URI=https://push.bitwarden.eu
    volumes:
      - ./data:/data
    ports:
      - 10380:80
    restart: unless-stopped
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]
> - [[Статьи/Настройка локального менеджера паролей Vaultwarden\|Настройка локального менеджера паролей Vaultwarden]]

> [!info]-
> Примечание:: Менеджер паролей

