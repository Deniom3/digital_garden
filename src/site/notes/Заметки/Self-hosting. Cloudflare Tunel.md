---
{"dg-publish":true,"permalink":"/zametki/self-hosting-cloudflare-tunel/","created":"2024-08-18 01:15","updated":"2025-11-07T10:09:59+03:00"}
---

Cloudfkare Tunel - это zerotier подключение для публикации сервисов и сайтов без использования белого ip адреса. Имеет ряд ограничений и особенностей таких как ограничение разовой передачи данных (100 Мб за файл на бесплатном плане) более низкая надежность и легкая блокируемость.

Документация: [Cloudflare Tunnel | Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Cloudflare Tunel

</div>



Докер файл написан из расчет на подключение к Traefik в сети докер proxy. Токен выдается при создании нового подключения в cloudflare

```yaml
services:
  cloudflare:
    image: cloudflare/cloudflared:latest
    container_name: cloudflare
    command: tunnel --no-autoupdate run
    environment:
      - TUNNEL_TOKEN=$TUNNEL_TOKEN
    networks:
      - proxy
    restart: unless-stopped

networks:
  proxy:
    external: true
```



</div></div>


### Настройка Cloudflare Tunel вместе с Traefik

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/zametki/nastrojka-cloudflare-tunel-vmeste-s-traefik/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Для переадресации запросов через [[Заметки/Self-hosting. Cloudflare Tunel\|Self-hosting. Cloudflare Tunel]]  на внутренний [[Заметки/Self-hosting. Traefik\|Traefik]] необходимо выполнить дополнительные настройки для передачи заголовков. 

Для этого при создании нового подключения необходимо указать следующие настройки:

![Настройка Cloudflare Tunel вместе с Traefik.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20Cloudflare%20Tunel%20%D0%B2%D0%BC%D0%B5%D1%81%D1%82%D0%B5%20%D1%81%20Traefik.png)
Для случая когда в единой сети докера, или указать Ip адрес контейнера/сервера на котором прослушивает traefik входящий трафик на 443 порту.

![Настройка Cloudflare Tunel вместе с Traefik-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20Cloudflare%20Tunel%20%D0%B2%D0%BC%D0%B5%D1%81%D1%82%D0%B5%20%D1%81%20Traefik-1.png)

Включить настройку **HTTP2 connection**, а так же заполнить **Origin Server Name** и **HTTP Host Header** указав https адрес по которому traefik обрабатывает сервис который необходимо пробросить.

---
> [!urls]- Упоминания:
> - 

</div></div>


### Передача ip клиента при использовании тунеля

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/zametki/blokirovka-realnogo-ip-atakuyushhego-v-crowdsec-tarefik-cf-tunel/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




При использование защиты [[Заметки/Self-hosting. CrowdSec\|CrowdSec]] в связке с проксирование cloudflare или [[Заметки/Self-hosting. Cloudflare Tunel\|Cloudflare Tunel]]  возникает проблема с баном не атакующего клиента а вышестоящего прокси. 

В обычной настройке crowdsec получает ip адрес клиента которого надо заблокировать из журнала доступа, но при использовании сложной системы проксирования когда перед [[Заметки/Self-hosting. Traefik\|Traefik]] находится еще один сервер пробирования (например cloudflare) клиентом в журнале становиться сам вышестоящий прокси сервер. 

### Настройка при использовании cloudflare tunnel

Для решения данной проблемы в статическую конфигурацию traefik необходимо добавить разрешенные прокси.

Разрешенные ip с которых необходимо передавать ip клиента указываются в разделе
```
forwardedHeaders:
      trustedIPs:
```

Пример:
```
entryPoints:
  http:
    address: ":80"
    forwardedHeaders:
      trustedIPs: &trustedIPs
        - 192.168.0.1/24
        - 100.127.101.215
    http:
      middlewares:
        - crowdsec@file
      redirections:
        entryPoint:
          to: https
          scheme: https
  https:
    address: ":443"
    forwardedHeaders:
      trustedIPs: *trustedIPs
    http:
      middlewares:
        - crowdsec@file
```

Необходимо указать ip адрес машины на которой непосредственно установлен cloudflare tunnel (если на одной машине в единой сети докер проблем не должно быть).

> [!note]
> Конструкция `&trustedIPs` используется при объявлении блока конфигурации  для повторного использования. Для вызова используется конструкция `*trustedIPs`

### Настройка при использовании проксирования cloudflare без cloudflare tunnel

Для решения проблемы когда перед traefik находится только сам сервер cloudflare можно воспользоваться плагином для traefik.

Плагин: [Plugin](https://plugins.traefik.io/plugins/62e97498e2bf06d4675b9443/real-ip-from-cloudflare-proxy-tunnel)
GitHub:  [GitHub - BetterCorp/cloudflarewarp](https://github.com/BetterCorp/cloudflarewarp?tab=readme-ov-file)

> [!danger]
> Плагин не обновляется возможно состав адресов неактуальный

Плагин по сути выполняет те же функции что и указание доверенных прокси при этом в его состав уже включены все адреса серверов cf используемые для проксирования.

Пример статической конфигурации:
```
entryPoints:
  http:
    address: ":80"
    http:
      middlewares:
        - crowdsec@file
        - cloudflarewarp@file
      redirections:
        entryPoint:
          to: https
          scheme: https
  https:
    address: ":443"
    http:
      middlewares:
        - crowdsec@file
        - cloudflarewarp@file

experimental:
  plugins:
    crowdsec-bouncer-traefik-plugin:
      moduleName: "github.com/maxlerebourg/crowdsec-bouncer-traefik-plugin"
      version: "v1.4.2"
    cloudflarewarp:
      moduleName: github.com/BetterCorp/cloudflarewarp
      version: v1.3.3
```

В папке динамической конфигурации необходимо создать файл для нового middlewares следующего содержимого:
```
http:
  middlewares:
    cloudflarewarp:
      plugin:
        cloudflarewarp:
          disableDefault: false
```

---
> [!urls]- Упоминания:
> - 

</div></div>



---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]
> - [[Статьи/Получите доступ к локальной установке Immich через Интернет, используя свой собственный домен\|Инструкция по настройке на примере Immich]]

> [!description]- Примечание
> Примечание:: Публикация сервиса в интернете без белого IP
