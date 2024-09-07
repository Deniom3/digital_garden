---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка подключения по ssh ключу для VS Code.md","permalink":"/komandy-i-nastrojki/nastrojka-podklyucheniya-po-ssh-klyuchu-dlya-vs-code/","updated":"2024-09-06T02:12:48+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
## Генерация ssh ключа в виндовс

Для подключения по ssh из VS Code к серверу без ввода пароля необходимо получить ключ и передать его на сервер:

Генерация ключа в консоли на Windows:
```shell
ssh-keygen
```

При генерации оставляем все поля ввода пустыми. Имя файла по умолчанию `id_rsa` и `id_rsa.pub`. 

Ключ сохраняется по пути `C:\Users\<user>\.ssh`

## Настройка подключения ssh VS Code

Необходимо указать путь к файлу авторизации в настройках подключения которые использует VS Code. 

Файл настроек расположен по пути `C:\Users\<user>\.ssh\config`

```
Host <ip>
  HostName <ip>
  User <login>
  IdentityFile "C:\Users\<user>\.ssh\id_rsa"
```

## Отправка ключа на удаленный сервер

Можно отправить командой:
```
scp ~/.ssh/id_rsa.pub username@server_ip:~/.ssh/authorized_keys
```

Или отправить используя сторонние программы например Putty или Termius.