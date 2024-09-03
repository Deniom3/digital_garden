---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/komanda-vyklyucheniya-okna-avtorizaczii-nextloud/"}
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