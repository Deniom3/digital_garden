---
{"dg-publish":true,"dg-path":"Команды и настройки/Изменение порта доступа по SSH.md","permalink":"/komandy-i-nastrojki/izmenenie-porta-dostupa-po-ssh/","updated":"2025-05-06T15:19:45+03:00"}
---

Для изменения порта доступа (по умолчанию 22) для SSH необходимо скорректировать файл
```shell
nano /etc/ssh/sshd_config
```

Необходимо закомментировать или добавить строку, заменив 22 на нужный порт.
```shell
Port 222
```

После перезапустить службу
```shell
sudo systemctl daemon-reload
sudo service ssh restart
```
