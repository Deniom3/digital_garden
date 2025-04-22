---
{"dg-publish":true,"dg-path":"Команды и настройки/Отключение авторизации паролем в ssh.md","permalink":"/komandy-i-nastrojki/otklyuchenie-avtorizaczii-parolem-v-ssh/","updated":"2025-04-21T01:26:06+03:00"}
---

Чтобы отключить вход по паролю, изменяем значение параметра в файле /etc/ssh/sshd\_config:

```
nano /etc/ssh/sshd_config
```

```shell
PasswordAuthentication no
```

Далее выполните перезапуск ssh сервера.

```shell
service ssh restart
```
