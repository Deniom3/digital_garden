---
{"dg-publish":true,"dg-path":"Команды и настройки/Выполнение команд настроек внутри контейнера Nextcloud AIO.md","permalink":"/komandy-i-nastrojki/vypolnenie-komand-nastroek-vnutri-kontejnera-nextcloud-aio/","updated":"2024-10-06T02:54:28+03:00"}
---


Для запуска команды `occ` для варианта установки Nextcloud AIO необходимо выполнить:
```shell
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ <command>
```