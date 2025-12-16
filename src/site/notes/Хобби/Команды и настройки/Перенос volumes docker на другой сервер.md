---
{"dg-publish":true,"dg-path":"Команды и настройки/Перенос volumes docker на другой сервер.md","permalink":"/komandy-i-nastrojki/perenos-volumes-docker-na-drugoj-server/","updated":"2025-12-15T01:04:16+03:00"}
---

Некоторые контейнеры docker требуют для работы использовать виртуальные папки volumes вместо обычного монтирования (например Grafana, Prometheus). Обычно это задается таким образом 
```yaml
...
	volumes:
      - prometheus_data:/prometheus
...
volumes:
  prometheus_data: {}
```

## Создание бэкапа

Перед созданием бэкапа остановить работу контейнера для исключения ошибок записи файлов.

Вывести список существующих томов данных:
```bash
docker volume ls
```

Создание бэкапа тома данных:
```sh
docker run --rm -v <volumes_name>:/volume -v $(pwd):/backup busybox tar czf /backup/<volumes_name>.tar.gz -C /volume .
```
## Восстановление из бэкапа

Восстановление необходимо выполнить до запуска контейнера, или удалить созданный ранее том данных.

Создадим volumes
```
docker volume create <volume_name>
```

Docker volumes хранятся в `/var/lib/docker/volumes/` по умолчанию. Найдите путь к вашему volume:

```bash
docker volume inspect <volume_name>
```

Ищите в выводе ключ `Mountpoint`, который указывает на путь к данному volume. Это обычно `/var/lib/docker/volumes/<volume_name>/_data`.

Распаковка архива
```sh
sudo tar -xzvf volume_backup.tar.gz -C /var/lib/docker/volumes/<volume_name>/_data
```

Необязательно проверка и изменение прав на данные (может потребоваться если запускалась распаковка не через sudo)
```
sudo chown -R root:root /var/lib/docker/volumes/<volume_name>/_data
sudo chmod -R 755 /var/lib/docker/volumes/<volume_name>/_data
```

После этого можно выполнить запуск контейнера.