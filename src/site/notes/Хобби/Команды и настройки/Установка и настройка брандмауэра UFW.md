---
{"dg-publish":true,"dg-path":"Команды и настройки/Установка и настройка брандмауэра UFW.md","permalink":"/komandy-i-nastrojki/ustanovka-i-nastrojka-brandmauera-ufw/","updated":"2024-10-06T02:51:10+03:00"}
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


> [!danger] Внимание
> При использовании с docker на последних версиях есть проблемы [[Хобби/Команды и настройки/Включение ufw для контейнеров докер\|Включение ufw для контейнеров докер]]