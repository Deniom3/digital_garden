---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/redaktirovanie-parametrov-nextclouds-aio-config-php/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для редактирования основных параметров конфигурации Nextcloud можно воспользоваться командой:

```shell
sudo docker run -it --rm --volume nextcloud_aio_nextcloud:/var/www/html:rw alpine sh -c "apk add --no-cache nano && nano /var/www/html/config/config.php"
```