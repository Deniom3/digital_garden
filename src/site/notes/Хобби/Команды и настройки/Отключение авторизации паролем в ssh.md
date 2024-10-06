---
{"dg-publish":true,"dg-path":"Команды и настройки/Отключение авторизации паролем в ssh.md","permalink":"/komandy-i-nastrojki/otklyuchenie-avtorizaczii-parolem-v-ssh/","updated":"2024-10-06T02:52:50+03:00"}
---

Чтобы отключить вход по паролю, изменяем значение параметра в файле /etc/ssh/sshd\_config:

```shell
PasswordAuthentication no
```

Далее выполните перезапуск ssh сервера.

```shell
service sshd restart
```
