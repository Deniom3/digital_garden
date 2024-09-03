---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/vypolnenie-komand-nastroek-vnutri-kontejnera-nextcloud-aio/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для запуска команды `occ` для варианта установки Nextcloud AIO необходимо выполнить:
```shell
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ <command>
```