---
{"dg-publish":true,"permalink":"/zametki/dostup-k-proxmox-cherez-authentik/","created":"2024-08-06 00:16","updated":"2024-10-12T19:44:57+03:00"}
---

Основные настройки описаны в документации: [authentik](https://docs.goauthentik.io/integrations/services/proxmox-ve/)

> [!bug]
> Консоль из веб интерфейса работает только для root пользователя изменить это нельзя. Можно только дать вход по ssh для пользователя authentik

- [-] Настроить доступ к консоли в proxmox через authentik ➕ 2024-08-06 ❌ 2024-09-12

> [!idea]
> Необходимо настроить связь между sso и ssh. authentik debian ssh
надо привязать к pam. в таком случае всё что связано с pam в том числе и ssh будет нормально авторизовываться через authentik.
[authentik](https://docs.goauthentik.io/integrations/services/sssd/)

---
> [!urls]- Упоминания:
> - 