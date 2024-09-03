---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/vklyuchenie-ufw-dlya-kontejnerov-doker/"}
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
