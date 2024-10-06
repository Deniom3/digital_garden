---
{"dg-publish":true,"dg-path":"Команды и настройки/Команда выключения окна авторизации Nextloud.md","permalink":"/komandy-i-nastrojki/komanda-vyklyucheniya-okna-avtorizaczii-nextloud/","updated":"2024-10-06T02:54:54+03:00"}
---

Для отключения окна авторизации Nextloud для использования внешней авторизации (например [[Заметки/Self-hosting. Authentik\|Authentik]]) выполнить команду:
```shell
sudo -u www-data php var/www/nextcloud/occ config:app:set --value=0 user_oidc allow_multiple_user_backends
```

Или команда для Nextcloud AIO:
```shell
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ config:app:set --value=0 user_oidc allow_multiple_user_backends
```