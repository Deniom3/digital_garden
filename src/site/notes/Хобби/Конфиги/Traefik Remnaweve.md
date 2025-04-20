---
{"dg-publish":true,"dg-path":"Конфиги/Traefik Remnaweve.md","permalink":"/konfigi/traefik-remnaweve/","updated":"2025-04-20T20:36:26+03:00"}
---

Для панели применяется в общем виде стандартный шаблон с защитой через authentik:
```yaml
http:
  routers:
    remnawave:
      rule: "Host(`panel.<domain>.ru`)"
      entrypoints:
        - http   
      middlewares:
        - remnawave-https-redirect
        - authentik
      service: remnawave
          
    remnawave-secure:
      rule: "Host(`panel.<domain>.ru`)"
      entrypoints:
        - https
      middlewares:
        - authentik
      tls: true
      service: remnawave

  middlewares:
    remnawave-https-redirect:
      redirectScheme:
        scheme: https

  services:
    remnawave:
      loadBalancer:
        servers:
          - url: "http://192.168.0.132:3030"

```

Для страницы подписки защита authentik не используется для свободного доступа пользователей:
```yaml
http:
  routers:
    sub-remnawave:
      rule: "Host(`sub.<domain>.ru`)"
      entrypoints:
        - http   
      middlewares:
        - sub-remnawave-https-redirect
      service: sub-remnawave
          
    sub-remnawave-secure:
      rule: "Host(`sub.<domain>.ru`)"
      entrypoints:
        - https
      tls: true
      service: sub-remnawave

  middlewares:
    sub-remnawave-https-redirect:
      redirectScheme:
        scheme: https

  services:
    sub-remnawave:
      loadBalancer:
        servers:
          - url: "http://192.168.0.132:3010"
```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Traefik\|Self-hosting. Traefik]]
> [[Заметки/Self-hosting. Remnawave\|Remnawave]]

> [!description]- Примечание
> Примечание::  Настройка проксирования для Remnaweve
