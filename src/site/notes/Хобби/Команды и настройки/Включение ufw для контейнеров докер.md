---
{"dg-publish":true,"dg-path":"Команды и настройки/Включение ufw для контейнеров докер.md","permalink":"/komandy-i-nastrojki/vklyuchenie-ufw-dlya-kontejnerov-doker/","updated":"2024-09-03T16:09:42+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

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
