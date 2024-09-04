---
{"dg-publish":true,"dg-path":"Команды и настройки/Команда выключения окна авторизации Nextloud.md","permalink":"/komandy-i-nastrojki/komanda-vyklyucheniya-okna-avtorizaczii-nextloud/","updated":"2024-09-03T16:10:43+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для отключения окна авторизации Nextloud для использования внешней авторизации (например [[Заметки/Провайдер авторизации Authentik\|Authentik]]) выполнить команду:
```shell
sudo -u www-data php var/www/nextcloud/occ config:app:set --value=0 user_oidc allow_multiple_user_backends
```

Или команда для Nextcloud AIO:
```shell
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ config:app:set --value=0 user_oidc allow_multiple_user_backends
```