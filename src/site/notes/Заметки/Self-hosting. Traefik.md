---
{"dg-publish":true,"permalink":"/zametki/self-hosting-traefik/","created":"2024-08-03 14:44","updated":"2025-04-20T20:32:09+03:00"}
---

Многофункциональный реверс прокси со сложной системой динамических и статических настроек. Имеет большое количество примеров и различных интеграций. Предназначен в первую очередь для использования в динамических системах на базе docker контейнеров.

Основные настройки проксирования выполняются на уровне конкретного контейнера с использованием меток.

Сайт проекта: [Traefik Labs: Say Goodbye to Connectivity Chaos](https://traefik.io/)

Репозиторий: [GitHub - traefik/traefik: The Cloud Native Application Proxy](https://github.com/traefik/traefik)

Документация: [Traefik Proxy Documentation - Traefik](https://doc.traefik.io/traefik/)
### Пример docker файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/traefik/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Traefik

</div>




Обязательно надо настроить файлы конфигурации [[Хобби/Конфиги/Traefik\|Traefik]]

```yaml
services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
      - 8082:8082
    environment:
      - CF_API_EMAIL=<CF_API_EMAIL>
      - CF_DNS_API_TOKEN=<CF_DNS_API_TOKEN>
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./traefik.yml:/traefik.yml:ro
      - ./acme.json:/acme.json
      - ./logs:/var/log/traefik
      - ./dynamic:/config:ro

networks:
  proxy:
    name: proxy
    external: true
```



</div></div>

## Настройка прокси

Так же в папке `/config` размещаются файлы динамической конфигурации в которую вносятся все настройки проксирования.

> [!idea]
> Для удобной навигации в конфигурации применяются следующие правила именования:
> - \_\<name>.yml - служебный файл содержащий настройки промежуточного слоя middlewares
> - !out-\<name>.yml  - конфигурация приложения которое размещается в общем доступе из интернета. В нем отсутствует настройка `ip-allow-list`
> - \<name>.yml - обычное приложение для внутренней сети

Используются два основных файла конфигурации:
- `acme.json` - создается пустым и предназначен для хранения данных о сертификатах
- `traefik.yml` - файл глобальных настроек, общие настройки применяемые ко всему серверу traefik

> [!check] Примеры конфигураций:
> - [[Хобби/Конфиги/Traefik\|Основные настройки]]
> - [[Хобби/Конфиги/Traefik общий шаблон\|Traefik общий шаблон]]
> - [[Хобби/Конфиги/Traefik ipAllowList\|Traefik ipAllowList]]
> - [[Хобби/Конфиги/Traefik Crowdsek\|Traefik Crowdsek]]
> - [[Хобби/Конфиги/Traefik Authentik\|Traefik Authentik]]
> - [[Хобби/Конфиги/Traefik Nextcloud\|Traefik Nextcloud]]
> - [[Хобби/Конфиги/Traefik Authentik базовая аутентификация\|Traefik Authentik базовая аутентификация]]
> - [[Хобби/Конфиги/Traefik Remnaweve\|Traefik Remnaweve]]

> [!warning]
> Метки докера и динамическая конфигурация взаимоисключающие настройки. Надежнее использовать динамическую конфигурацию.

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]

> [!description]- Примечание
> Примечание::  Реверс прокси
