---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка ротация журнала docker.md","permalink":"/komandy-i-nastrojki/nastrojka-rotacziya-zhurnala-docker/","tags":[""],"updated":"2024-12-08T14:43:33+03:00"}
---

По умолчанию ротация логов в докер контейнерах отключена для ее настройки необходимо внести изменения в файл:
```shell
sudo nano /etc/docker/daemon.json
```
указав:
```shell
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "30m",
    "max-file": "5"
  }
}

```

Где: 
- `max-size` - размер файла логов при достижении которого происходит ротация
- `max-file` - количество файлов логов которые сохраняются для контейнера, после удаляются по старшинству.

После установки настройки необходимо перезагрузить сервис и перезапустить все контейнеры:
```shell
sudo systemctl restart docker
```

Для проверки что настройки применились выполнить команду:
```shell
docker inspect <container_id> | grep -A 5 LogConfig
```

Уже имеющиеся логи не удаляться их необходимо очистить командой:
```shell
sudo truncate -s 0 <путь к логам>
```

