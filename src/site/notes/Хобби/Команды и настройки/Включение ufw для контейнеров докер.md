---
{"dg-publish":true,"dg-path":"Команды и настройки/Включение ufw для контейнеров докер.md","permalink":"/komandy-i-nastrojki/vklyuchenie-ufw-dlya-kontejnerov-doker/","updated":"2024-10-06T02:53:46+03:00"}
---

В последних версиях докер ufw не работает корректно, необходимо вносить изменения в конфиг файлы.

```shell
nano /etc/docker/daemon.json
```

Добавить запись:
```
{ "iptables": false }
```

Перезапустить службу докер
```
sudo service docker restart
```
