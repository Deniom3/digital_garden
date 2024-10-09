---
{"dg-publish":true,"dg-path":"Конфиги/Traefik Authentik.md","permalink":"/konfigi/traefik-authentik/","updated":"2024-10-09T21:26:53+03:00"}
---

Настройки [[Заметки/Self-hosting. Traefik\|Traefik]] для работы с [[Заметки/Self-hosting. Authentik\|Authentik]] отвечают за передачу данных авторизации для анализа. Необходимо создать отдельный файл в папке `traefik/dynamic`

```yml
http:
  middlewares:
    authentik:
      forwardAuth:
        address: "http://192.168.0.131:9000/outpost.goauthentik.io/auth/traefik"
        trustForwardHeader: true
        authResponseHeaders:
          - X-authentik-username
          - X-authentik-groups
          - X-authentik-email
          - X-authentik-name
          - X-authentik-uid
          - X-authentik-jwt
          - X-authentik-meta-jwks
          - X-authentik-meta-outpost
          - X-authentik-meta-provider
          - X-authentik-meta-app
          - X-authentik-meta-version
```