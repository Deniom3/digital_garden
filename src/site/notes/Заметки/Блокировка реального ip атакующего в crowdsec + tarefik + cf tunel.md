---
{"dg-publish":true,"permalink":"/zametki/blokirovka-realnogo-ip-atakuyushhego-v-crowdsec-tarefik-cf-tunel/","created":"2025-06-09 00:05","updated":"2025-06-09T01:35:35+03:00"}
---

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