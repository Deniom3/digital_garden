---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/otklyuchenie-avtorizaczii-parolem-v-ssh/"}
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
