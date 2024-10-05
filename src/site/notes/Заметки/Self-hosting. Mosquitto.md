---
{"dg-publish":true,"permalink":"/zametki/self-hosting-mosquitto/","created":"2024-10-01 21:14","updated":"2024-10-01T21:18:12+03:00"}
---

Программа предназначена для приемки и хранения mqtt сообщений. Протокол используется в рамках системы умного дома для устройств.

> [!attention]
> Использую в моей системе для интеграции устаревшей техники Polaris в систему умного дома.

Репозиторий: https://github.com/eclipse/mosquitto

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/mosquitto/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  mosquitto:
    container_name: mosquitto
    image: eclipse-mosquitto:1.5.11
    restart: unless-stopped
    privileged: true
    volumes:
      - ./config:/mosquitto/config
      - ./data:/mosquitto/data
      - ./log:/mosquitto/log
      - ./certs:/mosquitto/certs
    environment:
      - TZ=Europe/Moscow
    network_mode: host
    logging:
      driver: none

```


</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!info]-
> Примечание:: Mqtt сервер для умного дома

> [!todo]-
>  - [<] Перенастроить на использование Home Assistant ➕ 2024-10-01