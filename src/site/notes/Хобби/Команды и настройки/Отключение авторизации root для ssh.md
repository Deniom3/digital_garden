---
{"dg-publish":true,"dg-path":"Команды и настройки/Отключение авторизации root для ssh.md","permalink":"/komandy-i-nastrojki/otklyuchenie-avtorizaczii-root-dlya-ssh/","tags":[""],"updated":"2025-05-04T20:57:41+03:00"}
---

Чтобы отключить вход по паролю, изменяем значение параметра в файле /etc/ssh/sshd\_config:

```
nano /etc/ssh/sshd_config
```

```shell
PermitRootLogin no
```

Далее выполните перезапуск ssh сервера.

```shell
service ssh restart
```
