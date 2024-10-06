---
{"dg-publish":true,"dg-path":"Команды и настройки/Создание нового пользователя Linux.md","permalink":"/komandy-i-nastrojki/sozdanie-novogo-polzovatelya-linux/","updated":"2024-10-06T02:51:00+03:00"}
---

Создание пользователя:
```shell
sudo useradd -m -s /bin/bash deniom
```

Установка пароля:
```shell
sudo passwd deniom
```

Добавление в группу sudo:
```shell
sudo usermod -aG sudo deniom
```

Для работы пользователя с docker надо добавить в группу:
```shell
sudo usermod -aG docker deniom
```