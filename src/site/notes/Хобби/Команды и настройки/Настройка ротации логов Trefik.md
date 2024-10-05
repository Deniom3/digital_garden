---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка ротации логов Trefik.md","permalink":"/komandy-i-nastrojki/nastrojka-rotaczii-logov-trefik/","updated":"2024-10-05T18:07:56+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Ротация логов выполняется с помощью встроенной программы `logrotate` для ротации необходимо создать файл с настройками.

```shell
nano /etc/logrotate.d/traefik
```

```sh
/home/deniom/docker/traefik/logs/*.log {
  size 10M
  rotate 5
  missingok
  notifempty
  postrotate
    docker kill --signal="USR1" traefik
  endscript
}
```

Где
- `/home/deniom/docker/traefik/logs` - путь к файлам логов
- `traefik` - имя контейнера

Запустить:
```sh
logrotate /etc/logrotate.conf --debug 
logrotate /etc/logrotate.conf
```