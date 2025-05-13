---
{"dg-publish":true,"dg-path":"Команды и настройки/Установка и настройка брандмауэра UFW.md","permalink":"/komandy-i-nastrojki/ustanovka-i-nastrojka-brandmauera-ufw/","updated":"2025-05-11T17:26:15+03:00"}
---

Устанавливаем UFW
```shell
apt install ufw
```

Установка правил по умолчанию:
```shell
ufw default deny incoming && ufw default allow outgoing
```

Для открытия доступа на порт выполнить команду
```shell
ufw allow <PORT>
```

Включение брандмауэра (перед включением обязательно добавить в список разрешённых порт ssh).
```shell
ufw enable
```

Выключение (разрешение всех портов)
```shell
ufw desable
```

Просмотр правил:
```
sudo ufw status numbered
```

Удалить правило:
```
sudo ufw delete <номер>
```

> [!danger] Внимание
> При использовании с docker на последних версиях есть проблемы [[Хобби/Команды и настройки/Включение ufw для контейнеров докер\|Включение ufw для контейнеров докер]]