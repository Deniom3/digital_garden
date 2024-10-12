---
{"dg-publish":true,"permalink":"/zametki/dostup-k-vaultwarden-cherez-authentik/","created":"2024-08-06 00:08","updated":"2024-10-12T18:38:20+03:00"}
---

Для работы приложений и расширений [[Заметки/Self-hosting. Vaultwarden\|Vaultwarden]] при настройки авторизации через [[Заметки/Self-hosting. Authentik\|Authentik]] необходимо выполнить следующие настройки:

Перейти в настройки `Authentik > Provider > Advanced protocol settings > Unauthenticated Paths` и внести следующие значений:

```
^/api/.*
^/wl/.*
^/identity/.*
^/images/.*
```

> [!bug]
> При такой настройке в приложении не отображаются иконки сайтов

Если необходимо закрыть ТОЛЬКО страницу администратора то внести следующие значения:
```
^/$
^/.*\.js
^/#/.*
^/#/login
^/#/2fa
^/#/vault
^/api/.*
^/images/.*
^/identity/.*
^/app/.*
^/locales/.*
```

Источник: [Can Bitwarden access vaultwarden through authentik or security proxy? · dani-garcia/vaultwarden · Discussion #4611 · GitHub](https://github.com/dani-garcia/vaultwarden/discussions/4611)

---
> [!urls]- Упоминания:
> - 