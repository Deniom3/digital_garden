---
{"dg-publish":true,"permalink":"/zametki/pereklyuchenie-vstroennoj-avtorizaczii-na-authentik-dlya-git-tea/","created":"2024-08-11 23:58","updated":"2024-10-12T19:45:13+03:00"}
---

Для того что бы отключить обычную авторизацию в [[Заметки/Self-hosting. Gitea\|Gitea]] и использовать только авторизацию [[Заметки/Self-hosting. Authentik\|Authentik]] (или любого другого провайдера OAuth2) необходимо сделать переадресацию в реверс прокси.

Пример для [[Заметки/Self-hosting. Traefik\|Traefik]]
```
 http:
  routers:
	gitea-secure:
      rule: "Host(`gitea.<domein>.ru`)"
      entrypoints:
        - https
      middlewares:
        - authentik
        - gitea-login-redirect
        - ip-allow-list
      tls: true
      service: gitea
      
  middlewares:
    gitea-login-redirect:
      redirectregex:
        regex: https://gitea.<domein>.ru/user/login*
        replacement: https://gitea.<domein>.ru/user/oauth2/authentik
```

Где `authentik` имя провайдера OAuth2 в gitea.

---
> [!urls]- Упоминания:
> - 