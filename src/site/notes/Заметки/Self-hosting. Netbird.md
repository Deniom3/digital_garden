---
{"dg-publish":true,"permalink":"/zametki/self-hosting-netbird/","created":"2025-04-20 03:48","updated":"2025-10-27T02:27:05+03:00"}
---

NetBird объединяет в одной платформе одноранговую частную сеть без необходимости настройки и централизованную систему контроля доступа, что позволяет легко создавать защищённые частные сети для вашей организации или дома.

Я использую его для организации связи между серверами, для прокидывания данных мониторинга и для проксирования сайтов с домашнего сервера как замену [[Заметки/Self-hosting. Cloudflare Tunel\|claudflare tunel]].

> [!danger] Важно
> Основой решения остается wireguard из за чего могут быть проблемы с ТСПУ.

Репозиторий: https://github.com/netbirdio/netbird

Документация: [Introduction to NetBird - NetBird Docs](https://docs.netbird.io/)

Ниже рассмотрен вариант установки Netbird за реверс прокси Caddy и с авторизацией по Authentik. 

Если необходимо просто базовое использование без внедрения в сложные системы можно воспользоваться скриптом быстрого запуска из официальной документации: [Self-hosting quickstart guide (5 min) - NetBird Docs](https://docs.netbird.io/selfhosted/selfhosted-quickstart)

### Настройка и установка

> [!bug] Важно
> В официальной документации очень много неточностей что касаются нестандартной установки. Особенно много проблем с authentik и caddy.

Основной процесс установки описан в документации: [Advanced guide - NetBird Docs](https://docs.netbird.io/selfhosted/selfhosted-guide)
#### Настройка Authentik

Документация: [Identity Providers - NetBird Docs](https://docs.netbird.io/selfhosted/identity-providers#authentik)

Лучше использовать вариант на сайте Authentik: [Integrate with NetBird \| authentik](https://docs.goauthentik.io/integrations/services/netbird/)

Важные моменты не описанные в документации:
1. Необходимо использовать не пароль служебного пользователя а пароль приложения, указывать его в параметр `NETBIRD_IDP_MGMT_EXTRA_PASSWORD`
![Self-hosting. Netbird.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Netbird.png)
2. Необходимо изменить конфигурацию провайдера в разделе перенаправление и в разделе области по аналогии со скринами ниже:
![Self-hosting. Netbird-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Netbird-1.png)
![Self-hosting. Netbird-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Netbird-2.png)
3. Указать пользователя Netbird в приложении 
![Self-hosting. Netbird-5.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Netbird-5.png)

Данные для `NETBIRD_AUTH_OIDC_CONFIGURATION_ENDPOINT` указаны в поле провайдера `URL-адрес конфигурации OpenID`.
#### Пример файла конфигурации:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```shell
NETBIRD_DASHBOARD_TAG=""
NETBIRD_SIGNAL_TAG=""
NETBIRD_MANAGEMENT_TAG=""
COTURN_TAG=""
NETBIRD_RELAY_TAG=""

# Dashboard domain. e.g. app.mydomain.com
NETBIRD_DOMAIN="subdomein.domein.ru"

# TURN server domain. e.g. turn.mydomain.com
# if not specified it will assume NETBIRD_DOMAIN
NETBIRD_TURN_DOMAIN=""

# TURN server public IP address
# required for a connection involving peers in
# the same network as the server and external peers
# usually matches the IP for the domain set in NETBIRD_TURN_DOMAIN
NETBIRD_TURN_EXTERNAL_IP="<IP_Server>"

# -------------------------------------------
# OIDC
#  e.g., https://example.eu.auth0.com/.well-known/openid-configuration
# -------------------------------------------
NETBIRD_AUTH_OIDC_CONFIGURATION_ENDPOINT="https://authentik.<domein>.ru/application/o/netbird/.well-known/openid-configuration"
NETBIRD_USE_AUTH0=false
NETBIRD_AUTH_CLIENT_ID="<CLIENT_ID>"
NETBIRD_AUTH_SUPPORTED_SCOPES="openid profile email offline_access api"
NETBIRD_AUTH_AUDIENCE="<CLIENT_ID>"
NETBIRD_AUTH_DEVICE_AUTH_CLIENT_ID="<CLIENT_ID>"
NETBIRD_AUTH_DEVICE_AUTH_AUDIENCE="<CLIENT_ID>"
# -------------------------------------------
# OIDC Device Authorization Flow
# -------------------------------------------
NETBIRD_AUTH_DEVICE_AUTH_PROVIDER="none"
NETBIRD_AUTH_DEVICE_AUTH_CLIENT_ID=""
# Some IDPs requires different audience, scopes and to use id token for device authorization flow
# you can customize here:
NETBIRD_AUTH_DEVICE_AUTH_AUDIENCE=$NETBIRD_AUTH_AUDIENCE
NETBIRD_AUTH_DEVICE_AUTH_SCOPE="openid"
NETBIRD_AUTH_DEVICE_AUTH_USE_ID_TOKEN=false
# -------------------------------------------
# OIDC PKCE Authorization Flow
# -------------------------------------------
# Comma separated port numbers. if already in use, PKCE flow will choose an available port from the list as an alternative
# eg. 53000,54000
NETBIRD_AUTH_PKCE_REDIRECT_URL_PORTS="53000"
# -------------------------------------------
# IDP Management
# -------------------------------------------
NETBIRD_MGMT_IDP="authentik"
NETBIRD_IDP_MGMT_CLIENT_ID="<CLIENT_ID>"
NETBIRD_IDP_MGMT_EXTRA_USERNAME="Netbird"
NETBIRD_IDP_MGMT_EXTRA_PASSWORD="<Пароль приложения>"
# -------------------------------------------
# Letsencrypt
# -------------------------------------------
# Disable letsencrypt
#  if disabled, cannot use HTTPS anymore and requires setting up a reverse-proxy to do it instead
NETBIRD_DISABLE_LETSENCRYPT=true
# e.g. hello@mydomain.com
NETBIRD_LETSENCRYPT_EMAIL="<EMAIL>"
# -------------------------------------------
# Extra settings
# -------------------------------------------
# Disable anonymous metrics collection, see more information at https://netbird.io/docs/FAQ/metrics-collection
NETBIRD_DISABLE_ANONYMOUS_METRICS=false
# DNS DOMAIN configures the domain name used for peer resolution. By default it is netbird.selfhosted
NETBIRD_MGMT_DNS_DOMAIN=netbird.selfhosted

# -------------------------------------------
# Relay settings
# -------------------------------------------
# Relay server domain. e.g. relay.mydomain.com
# if not specified it will assume NETBIRD_DOMAIN
NETBIRD_RELAY_DOMAIN=""

# Relay server connection port. If none is supplied
# it will default to 33080
NETBIRD_RELAY_PORT=""

NETBIRD_SIGNAL_PORT="443"
NETBIRD_MGMT_API_PORT="443"
```

---
> [!urls]- Упоминания:

> [!description]- Примечание
> Примечание::  Конфигурация netbird


</div></div>


Дальнейшая генерация конфигурационных файлов выполняется автоматически по инструкции. 

> [!tip] Важно
> Обязательно надо запускать скрипт генерации файлов из официальной инструкции так как он создает сразу несколько служебных файлов на основе заданных параметров.

#### Модификация docker compose

После создания docker-compose его необходимо привести к следующему виду.

> [!danger]
> Важно изменить порт для контейнера `management` в трёх местах указав любой отличный от 443.

Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/netbird-caddy/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  # UI dashboard
  dashboard:
    image: netbirdio/dashboard:latest
    restart: unless-stopped
    ports:
      - 8080:80
#      - 443:443
    environment:
      # Endpoints
      - NETBIRD_MGMT_API_ENDPOINT=https://netbird.<domein>.ru:443
      - NETBIRD_MGMT_GRPC_API_ENDPOINT=https://netbird.<domein>.ru:443
      # OIDC
      - AUTH_AUDIENCE=<secret_authentik>
      - AUTH_CLIENT_ID=<secret_authentik>
      - AUTH_CLIENT_SECRET=
      - AUTH_AUTHORITY=https://authentik.<domein>.ru/application/o/netbird/
      - USE_AUTH0=false
      - AUTH_SUPPORTED_SCOPES=openid profile email offline_access api
      - AUTH_REDIRECT_URI=
      - AUTH_SILENT_REDIRECT_URI=
      - NETBIRD_TOKEN_SOURCE=accessToken
      # SSL
      - NGINX_SSL_PORT=443
      # Letsencrypt
      - LETSENCRYPT_DOMAIN=
      - LETSENCRYPT_EMAIL=<email>
    volumes:
      - netbird-letsencrypt:/etc/letsencrypt/
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

  # Signal
  signal:
    image: netbirdio/signal:latest
    restart: unless-stopped
    volumes:
      - netbird-signal:/var/lib/netbird
    ports:
      - 10000:10000
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

  # Relay
  relay:
    image: netbirdio/relay:latest
    restart: unless-stopped
    environment:
    - NB_LOG_LEVEL=info
    - NB_LISTEN_ADDRESS=:33080
    - NB_EXPOSED_ADDRESS=netbird.<domein>.ru:33080
    # todo: change to a secure secret
    - NB_AUTH_SECRET=<secret>
    ports:
      - 33080:33080
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

  # Management
  management:
    image: netbirdio/management:latest
    restart: unless-stopped
    depends_on:
      - dashboard
    volumes:
      - netbird-mgmt:/var/lib/netbird
      - netbird-letsencrypt:/etc/letsencrypt:ro
      - ./management.json:/etc/netbird/management.json
    ports:
      - 33073:33073 #API port
  #    # command for Let's Encrypt validation without dashboard container
  #    command: ["--letsencrypt-domain", "", "--log-file", "console"]
    command: [
      "--port", "33073",
      "--log-file", "console",
      "--log-level", "info",
      "--disable-anonymous-metrics=false",
      "--single-account-mode-domain=spb.<domein>.ru",
      "--dns-domain=netbird.selfstil"
      ]
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"
    environment:
      - NETBIRD_STORE_ENGINE_POSTGRES_DSN=
      - NETBIRD_STORE_ENGINE_MYSQL_DSN=
      
  # Coturn
  coturn:
    image: coturn/coturn:latest
    restart: unless-stopped
    volumes:
      - ./turnserver.conf:/etc/turnserver.conf:ro
    network_mode: host
    command:
      - -c /etc/turnserver.conf
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

volumes:
  netbird-mgmt:
  netbird-signal:
  netbird-letsencrypt:

```


</div></div>


### Настройка реверс прокси traefik

Конфигурация для traefik

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/traefik-netbird/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
http:
  routers:
# netbird-dashboard
    netbird-dashboard:
      rule: "Host(`netbird.domein.ru`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-dashboard-https-redirect
      service: netbird-dashboard
          
    netbird-dashboard-secure:
      rule: "Host(`netbird.domein.ru`)"
      entrypoints:
        - https
      tls:
        certResolver: cloudflare
      service: netbird-dashboard
# netbird-signal
    netbird-signal:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/signalexchange.SignalExchange/`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-signal-https-redirect
      service: netbird-signal

    netbird-signal-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/signalexchange.SignalExchange/`)"
      entrypoints:
        - https
      service: netbird-signal
      tls:
        certResolver: cloudflare
# netbird-relay
    netbird-relay:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/relay`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-relay-https-redirect
      service: netbird-relay

    netbird-relay-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/relay`)"
      entrypoints:
        - https
      service: netbird-relay
      tls:
        certResolver: cloudflare

# netbird-api
    netbird-api:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/api`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-api-https-redirect
      service: netbird-api

    netbird-api-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/api`)"
      entrypoints:
        - https
      service: netbird-api
      tls:
        certResolver: cloudflare

# netbird-api
    netbird-management:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/management.ManagementService/`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-management-https-redirect
      service: netbird-management

    netbird-management-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/management.ManagementService/`)"
      entrypoints:
        - https
      service: netbird-management
      tls:
        certResolver: cloudflare

  middlewares:
    netbird-dashboard-https-redirect:
      redirectScheme:
        scheme: https
    netbird-signal-https-redirect:
      redirectScheme:
        scheme: https
    netbird-relay-https-redirect:
      redirectScheme:
        scheme: https
    netbird-api-https-redirect:
      redirectScheme:
        scheme: https
    netbird-management-https-redirect:
      redirectScheme:
        scheme: https

  services:
    netbird-dashboard:
      loadBalancer:
        servers:
          - url: "http://192.168.0.131:8085"

    netbird-signal:
      loadBalancer:
        servers:
          - url: "h2c://192.168.0.131:10000"

    netbird-relay:
      loadBalancer:
        servers:
          - url: "http://192.168.0.131:3478"

    netbird-api:
      loadBalancer:
        servers:
          - url: "http://192.168.0.131:33073"

    netbird-management:
      loadBalancer:
        servers:
          - url: "h2c://192.168.0.131:33073"

```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Netbird\|Self-hosting. netbird]]

> [!description]- Примечание
> Примечание::  Пример конфигурации traefik для netbird


</div></div>


### Настройка реверс прокси caddy

Caddy со следующими настройками


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/caddy-netbird/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  caddy:
    image: caddy:latest
    container_name: caddy
    restart: unless-stopped
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
      - ./logs:/var/log/caddy
      - caddy_data_panel:/data
      - caddy_config_panel:/config
    env_file:
      - .env
    network_mode: "host"

volumes:
  caddy_data_panel:
  caddy_config_panel:
```

env
```
SELF_STEAL_DOMAIN=netberd.domein.ru
```

</div></div>


Конфигурация Caddy:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/caddy-netbird/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Caddy Netbird

</div>




```shell
(security_headers) {
    header * {
        Strict-Transport-Security "max-age=3600; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        X-XSS-Protection "1; mode=block"
        -Server
        Referrer-Policy strict-origin-when-cross-origin
    }
}

https://{$SELF_STEAL_DOMAIN} {
    import security_headers
	reverse_proxy /signalexchange.SignalExchange/* h2c://127.0.0.1:10000
	reverse_proxy /api/* 127.0.0.1:33073
	reverse_proxy /management.ManagementService/* h2c://127.0.0.1:33073
	reverse_proxy /* 127.0.0.1:8080
}
```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Netbird\|Self-hosting. Netbird]]

> [!description]- Примечание
> Примечание::  Настройка Caddy для Netbird


</div></div>


> [!bug]
> Иногда происходят подвисания после авторизации и может долго крутить страницу. Необходимо перезагрузить страницу.

### Настройка  в программе и подключение клиентов

Основные настройки выполняются через удобный web интерфейс. Подключение клиентов лучше выполнять через меню `Setup Keys` при подключении сразу указывать группу клиента, они используются для правил маршрутизации.

Для подключения линукс клиентов лучше использовать установку через пакет, докер клиент зависти полноценно так и не получилось.

Правила маршрутизации устанавливаются в разделе `Acces Control` -> `Policies`

> [!danger] Важно
> Политику по умолчанию лучше сразу отключить она дает полный доступ между серверами!

#### Настройка политики доступа для пинга
![Self-hosting. Netbird-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Netbird-3.png)

#### Настройка доступа для мониторинга серверов
![Self-hosting. Netbird-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20Netbird-4.png)

### Проектирование сайтов с локального сервера на ноду

В процессе описания

### Использование для обхода блокировок

Система позволяет организовать выходную ноду и направлять трафик по конкретному ip или домену сразу на конкретного клиента и выходить через него в интернет. Для этого используются Exit Node.

[Configuring default routes for Internet traffic - NetBird Docs](https://docs.netbird.io/how-to/configuring-default-routes-for-internet-traffic)

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 
> [!description]- Примечание
> Примечание:: zerotier сеть
