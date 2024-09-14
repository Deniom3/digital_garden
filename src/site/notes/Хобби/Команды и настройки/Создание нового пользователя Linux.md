---
{"dg-publish":true,"dg-path":"Команды и настройки/Создание нового пользователя Linux.md","permalink":"/komandy-i-nastrojki/sozdanie-novogo-polzovatelya-linux/","updated":"2024-09-11T20:11:49+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

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