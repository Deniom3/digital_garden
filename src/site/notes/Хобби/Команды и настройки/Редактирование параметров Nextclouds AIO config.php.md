---
{"dg-publish":true,"dg-path":"Команды и настройки/Редактирование параметров Nextclouds AIO config.php.md","permalink":"/komandy-i-nastrojki/redaktirovanie-parametrov-nextclouds-aio-config-php/","updated":"2024-09-03T16:14:16+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для редактирования основных параметров конфигурации Nextcloud можно воспользоваться командой:

```shell
sudo docker run -it --rm --volume nextcloud_aio_nextcloud:/var/www/html:rw alpine sh -c "apk add --no-cache nano && nano /var/www/html/config/config.php"
```