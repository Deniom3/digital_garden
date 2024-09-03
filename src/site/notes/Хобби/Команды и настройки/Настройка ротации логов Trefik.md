---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка ротации логов Trefik.md","permalink":"/komandy-i-nastrojki/nastrojka-rotaczii-logov-trefik/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Ротация логов выполняется с помощью встроенной программы `logrotate` для ротации необходимо создать файл с настройками.

```shell
nano /etc/logrotate.d/traefik
```

```sh
/home/deniom/docker/traefik/log/*.log {
  size 10M
  rotate 5
  missingok
  notifempty
  postrotate
    docker kill --signal="USR1" trefik
  endscript
}
```

Где
- `/home/deniom/docker/traefik/log` - путь к файлам логов
- `trefik` - имя контейнера

Запустить:
```sh
logrotate /etc/logrotate.conf --debug 
logrotate /etc/logrotate.conf
```