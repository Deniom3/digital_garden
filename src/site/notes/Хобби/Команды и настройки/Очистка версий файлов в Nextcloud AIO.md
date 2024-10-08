---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка версий файлов в Nextcloud AIO.md","permalink":"/komandy-i-nastrojki/ochistka-versij-fajlov-v-nextcloud-aio/","updated":"2024-10-06T02:52:48+03:00"}
---

Версии файлов хранятся в папке пользователя на смонтированном диске в папке `files_versions` 

Занимаемое место можно проверить командой
```shell
du -sh ./files_versions
```

Очистка версий файлов для конкретного пользователя
```sh
sudo docker exec --user www-data -it nextcloud-aio-nextcloud php occ versions:cleanup Deniom
```

Для настройки хранения версий файлов можно внести в [[Хобби/Команды и настройки/Редактирование параметров Nextclouds AIO config.php\|изменения в файл конфигурации]] 

```
'versions_retention_obligation' => 'auto 3',
```

Подробнее про настройку: [Controlling file versions and aging — Nextcloud latest Administration Manual latest documentation](https://docs.nextcloud.com/server/latest/admin_manual/configuration_files/file_versioning.html#controlling-file-versions-and-aging)