---
{"dg-publish":true,"dg-path":"Команды и настройки/Отключение авторизации паролем в ssh.md","permalink":"/komandy-i-nastrojki/otklyuchenie-avtorizaczii-parolem-v-ssh/","updated":"2024-09-03T16:13:05+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Чтобы отключить вход по паролю, изменяем значение параметра в файле /etc/ssh/sshd\_config:

```shell
PasswordAuthentication no
```

Далее выполните перезапуск ssh сервера.

```shell
service sshd restart
```
