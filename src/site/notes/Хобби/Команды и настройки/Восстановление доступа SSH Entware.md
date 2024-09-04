---
{"dg-publish":true,"dg-path":"Команды и настройки/Восстановление доступа SSH Entware.md","permalink":"/komandy-i-nastrojki/vosstanovlenie-dostupa-ssh-entware/","updated":"2024-09-03T16:09:50+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Если не проходит подключение к Entware по 222 порту необходимо перезапустить службу SSH. Для этого подключаемся по SSH к Keenetic по 22 порту или по telnet 23 порт.

Для доступа в систему Entware через SSH роутера ввести команду
```shell
exec sh
```

Проверить порт сервера SSH для кинетика указанный в файле `/opt/etc/config/dropbear.conf` 
```sh
cat /opt/etc/config/dropbear.conf
```

В выводе порт должен отличаться от занятых основной системой Keenetic, по умолчанию 222.

Для перезапуска SSH Entware выполнить команду:
```sh
/opt/etc/init.d/S51dropbear start
```
