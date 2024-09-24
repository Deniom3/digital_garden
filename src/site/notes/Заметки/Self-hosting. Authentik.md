---
{"dg-publish":true,"permalink":"/zametki/self-hosting-authentik/","created":"2024-07-19 00:35","updated":"2024-09-24T23:00:46+03:00"}
---

Провайдер единой централизованной аутентификации. Позволяет организовать единое окно входа для всех сервисов.

> [!bug]
> Использование вместе с Home Assistant приводит к большому числу проблем, лучше не использовать для его защиты.

Репозиторий:  [GitHub - goauthentik/authentik: The authentication glue you need.](https://github.com/goauthentik/authentik)

Пример настройки в связке с caddy: [Home Assistant - Authentik No header set, returning empty flow · Issue #4994 · goauthentik/authentik · GitHub](https://github.com/goauthentik/authentik/issues/4994)

Установка в docker в связке с traefik: [[Хобби/Docker compose/Authentik\|Authentik]]

Документация: [Welcome to authentik | authentik](https://docs.goauthentik.io/docs/)

> [!idea] Настройки приложений
> - Самые распространенные описаны в документации [authentik](https://docs.goauthentik.io/integrations/)
> - [[Заметки/Проксирование всех доменов через Authentik\|Проксирование всех доменов через Authentik]]
> - [[Заметки/Доступ к Vaultwarden через Authentik\|Vaultwarden]]
> - [[Заметки/Настройка Based Aut в Authentik\|Настройка Based Aut в Authentik]]
> - OAuth2/OpenID на примере portainer [Portainer | authentik](https://docs.goauthentik.io/integrations/services/portainer/)
> - [[Заметки/Настройка LDAP Authentik\|Настройка LDAP Authentik]]
> - [[Заметки/Авторизация Calibre через Authentik\|Авторизация Calibre через Authentik]]
> - [[Заметки/Пример OAuth2 OpenID аутентификации Authentik для Immich\|Пример OAuth2 OpenID аутентификации Authentik для Immich]]
> - [[Заметки/Переключение встроенной авторизации на Authentik для GitTea\|Переключение встроенной авторизации на Authentik для GitTea]]
> - [[Заметки/Доступ к Proxmox через Authentik\|Доступ к Proxmox через Authentik]]
> - [[Заметки/Self-hosting. CouchDB\|Настройка для CouchDB]]

> [!hint] Важно
> Можно настроить общую защиты по домену и в дальнейшем переопределять отдельными правилами для каждого приложения.

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Провайдер единой авторизации SSO

> [!todo]-
> - [x] Изучить и попробовать настроить в системе ➕ 2024-07-19 ✅ 2024-08-03
> - [-] Настроить связь пользователя linux pam с пользователем authentik для proxmox 🔼 ❌ 2024-09-12
> - [-] Попробовать метод настройки авторизации как immiсh для приложения HA с адресом перехода в приложение ➕ 2024-08-07 ❌ 2024-09-22
