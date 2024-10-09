---
{"dg-publish":true,"dg-path":"Конфиги/Traefik общий шаблон.md","permalink":"/konfigi/traefik-obshhij-shablon/","updated":"2024-10-09T21:25:56+03:00"}
---


Общий шаблон для трафика с использованием списков [[Хобби/Конфиги/Traefik ipAllowList\|разрешенных адресов]], а так же [[Хобби/Конфиги/Traefik Authentik\|контролем аутентификации]]. Для настройки каждого приложения создаю отдельный файл в папке `traefik/dynamic`

```yml
http:
  routers:
    <name>:
      rule: "Host(`<name>.<domain>.ru`)"
      entrypoints:
        - http   
      middlewares:
        - <name>-https-redirect
        - authentik
        - ip-allow-list
      service: <name>
          
    <name>-secure:
      rule: "Host(`<name>.<domain>.ru`)"
      entrypoints:
        - https
      middlewares:
        - authentik
        - ip-allow-list
      tls: true
      service: <name>

  middlewares:
    <name>-https-redirect:
      redirectScheme:
        scheme: https

  services:
    <name>:
      loadBalancer:
        servers:
          - url: "http://192.168.0.130:1234"

```