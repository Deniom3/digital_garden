---
{"dg-publish":true,"dg-path":"Конфиги/Traefik Authentik базовая аутентификация.md","permalink":"/konfigi/traefik-authentik-bazovaya-autentifikacziya/","updated":"2024-10-09T21:27:21+03:00"}
---

Настройки [[Заметки/Self-hosting. Traefik\|Traefik]] для работы с [[Заметки/Self-hosting. Authentik\|Authentik]] отвечают за передачу данных авторизации для анализа. Необходимо создать отдельный файл в папке `traefik/dynamic`

> [!attention]
> Блок authentik-media предназначен для приложений использующих базовую аутентификацию в моем случае приложения arr стека ([[Заметки/Self-hosting. Radarr\|Radarr]] [[Заметки/Self-hosting. Sonarr\|Sonarr]] [[Заметки/Self-hosting. Prowlarr\|Prowlarr]]). Данный блок нельзя указывать для других приложений без базовой аутентификации.

```yml
    authentik-media:
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
          - Authorization 
```