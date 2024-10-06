---
{"dg-publish":true,"dg-path":"Команды и настройки/Изменение порта доступа по SSH.md","permalink":"/komandy-i-nastrojki/izmenenie-porta-dostupa-po-ssh/","updated":"2024-10-06T02:51:25+03:00"}
---

Для изменения порта доступа (по умолчанию 22) для SSH необходимо скорректировать файл
```shell
nano /etc/ssh/sshd_config
```

Необходимо закомментировать или добавить строку, заменив 22 на нужный порт.
```shell
Port 22
```

После перезагрузить SSH сервер
```shell
service sshd restart
```