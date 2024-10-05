---
{"dg-publish":true,"permalink":"/zametki/self-hosting-gitea/","created":"2024-08-12 00:06","updated":"2024-10-01T20:48:32+03:00"}
---

Легковесный вариант гит репозитория для хранения проектов git и создание зеркал общедоступных проектов. Имеет возможность использовать OAuth2 авторизацию и создание исполнителей.

Сайт проекта: [Gitea Official Website](https://about.gitea.com/)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
version: '3.9'
services:
  gitea:
    container_name: gitea
    image: gitea/gitea:latest
    restart: unless-stopped
    environment:
      - USER_GID=1000
      - USER_UID=1000
    ports:
      - 3002:3000
      - 222:22
    volumes:
      - /DATA/AppData/gitea/data:/data
```

</div></div>


Альтернативный форк: [Forgejo – Beyond coding. We forge.](https://forgejo.org/)

Настройка репозитория: [Customizing Gitea | Gitea Documentation](https://docs.gitea.com/administration/customizing-gitea)

> [!note]
>Настройка аутентификации через Authentik:
>- Основное руководство [Gitea | authentik](https://docs.goauthentik.io/integrations/services/gitea/)
>- Уточнения по настройке групп [Gitea groups documentation is outdated · Issue #6987 · goauthentik/authentik · GitHub](https://github.com/goauthentik/authentik/issues/6987)
>- [[Заметки/Переключение встроенной авторизации на Authentik для GitTea\|Переключение встроенной авторизации на Authentik для GitTea]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!info]-
> Примечание:: Облегченный git репозиторий