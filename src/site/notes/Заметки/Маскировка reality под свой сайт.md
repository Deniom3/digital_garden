---
{"dg-publish":true,"permalink":"/zametki/maskirovka-reality-pod-svoj-sajt/","created":"2025-05-13 22:12","updated":"2025-05-14T00:09:55+03:00"}
---

Для работы защиты reality с маскировкой трафика под https трафик необходимо использовать сайт донор. Такой сайт можно найти с использованием [[Хобби/Команды и настройки/Выбор домена маскировки для Reality\|сканера]] при выборе сайта донора очень важную роль играет задержка от xray до сайта.

Самым лучшим решением будет поднятие своего собственного сайта на том же сервере где развернута точка подключения reality.

> [!tip] Важно
> Предложенное решение работает с любым типом панелей и нод от [[Заметки/Self-hosting. Remnawave\|Remnawave]] до [[Заметки/Self-hosting. 3X-UI\|3x-ui]] и чистой установки [[Заметки/Ядро прокси Xray-core\|Xray]].

Основа была взята из проекта для маскировки remnawave: [GitHub - xxphantom/caddy-for-remnawave: Обратный прокси для remnawave](https://github.com/xxphantom/caddy-for-remnawave) 

Далее я буду описывать только маскировки "ноды", конечной точки подключения клиентов, как на схеме ниже:

```
Клиент → 443 порт → Xray → (Прокси-соединения)
                      ↓
                     Caddy → Selfsteal сайт
```

> [!important] Важно
> Весь контент на сайт будет проходить в первую очередь через x-ray что приводит к повышенной зависимости. Если перестанет работать он то сайт полностью перестанет открываться.

### Прокси сервер

Для публикации будем использовать Caddy как очень простой в настройке прокси сервер с возможностью публикации сайта.


<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





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

Env:
```
# Домены
SELF_STEAL_DOMAIN=netbird.<domein>.ru #Должен совпадать с XRAY конфигом (realitySettings.serverNames)
SELF_STEAL_PORT=9443 #Должен совпадать с XRAY конфигом (realitySettings.dest)
```

</div></div>


> [!hint] Важно
> Caddy запускаем именно в режиме host, прослушиванием 443 порта будет заниматься xray а не caddy.

### Конфигурация Caddy

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/caddy-reality/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
{
    https_port {$SELF_STEAL_PORT}
    default_bind 127.0.0.1
    servers {
        listener_wrappers {
            proxy_protocol {
                allow 127.0.0.1/32
            }
            tls
        }
    }
    auto_https disable_redirects
}

http://{$SELF_STEAL_DOMAIN} {
    bind 0.0.0.0
    redir https://{$SELF_STEAL_DOMAIN}{uri} permanent
}

https://{$SELF_STEAL_DOMAIN} {
    root * /var/www/html
    try_files {path} /index.html
    file_server

}


:{$SELF_STEAL_PORT} {
    tls internal
    respond 204
}

:80 {
    bind 0.0.0.0
    respond 204
}

```

---
> [!urls]- Упоминания:
> [[Заметки/Маскировка reality под свой сайт\|Маскировка reality под свой сайт]]

> [!description]- Примечание
> Примечание::  Настройка caddy для маскировки reality


</div></div>


За публикацию сайта отвечает секция конфигурации:
```
https://{$SELF_STEAL_DOMAIN} {
    root * /var/www/html
    try_files {path} /index.html
    file_server

}
```
В предложенном варианте происходит публикация статической страницы пример которой можно взять в [репозитории](https://github.com/xxphantom/caddy-for-remnawave), но лучше использовать свою или опубликовать любой другой сервис.

Заменив содержимое например на `reverse_proxy comteiner:8080`

Пример настройки для настройки с маскировкой под [[Заметки/Self-hosting. Netbird\|Netbird]]:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/caddyfike-netbird-xray/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





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

{
    https_port {$SELF_STEAL_PORT}
    default_bind 127.0.0.1
    servers {
        listener_wrappers {
            proxy_protocol {
                allow 127.0.0.1/32
            }
            tls
        }
    }
    auto_https disable_redirects
}

http://{$SELF_STEAL_DOMAIN} {
    bind 0.0.0.0
    redir https://{$SELF_STEAL_DOMAIN}{uri} permanent
}

https://{$SELF_STEAL_DOMAIN} {
    import security_headers
	reverse_proxy /signalexchange.SignalExchange/* h2c://127.0.0.1:10000
	reverse_proxy /api/* 127.0.0.1:33073
	reverse_proxy /management.ManagementService/* h2c://127.0.0.1:33073
	reverse_proxy /* 127.0.0.1:8080
}

:{$SELF_STEAL_PORT} {
    tls internal
    respond 204
}

:80 {
    bind 0.0.0.0
    respond 204
}

```

---
> [!urls]- Упоминания:

> [!description]- Примечание
> Примечание::  Файл конфигурации для Netbird и Xray


</div></div>



---
> [!urls]- Упоминания:
> - 