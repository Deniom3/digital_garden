---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/sozdanie-novogo-polzovatelya-linux/"}
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