---
{"dg-publish":true,"dg-path":"Конфиги/Traefik Nextcloud.md","permalink":"/konfigi/traefik-nextcloud/","updated":"2024-10-09T21:27:08+03:00"}
---

Для работы [[Заметки/Self-hosting. Nextcloud\|Nextcloud]] через [[Заметки/Self-hosting. Traefik\|Traefik]] необходимо использовать особую конфигурацию.

```yml
http:
  middlewares:
    nextcloud-secure-headers:
      headers:
        hostsProxyHeaders:
          - "X-Forwarded-Host"
        referrerPolicy: "same-origin"
        customResponseHeaders:
          X-Robots-Tag: "noindex, nofollow"
    https-redirect:
      redirectScheme:
        scheme: https
        permanent: true
    nextcloud-chain:
      chain:
        middlewares:
          - https-redirect
          - nextcloud-secure-headers
  routers:
    nextcloud:
      entrypoints:
        - "https"
      rule: "Host(`nextcloud.<domain>.ru`)"
      middlewares:
        - https-redirect
        - nextcloud-secure-headers
      tls:
        domains:
          - main: "nextcloud.<domain>.ru"
      service: nextcloud
  services:
    nextcloud:
      loadBalancer:
        servers:
          - url: "http://192.168.0.133:11000"
        passHostHeader: true
```