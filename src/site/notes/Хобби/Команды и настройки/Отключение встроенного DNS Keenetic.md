---
{"dg-publish":true,"dg-path":"Команды и настройки/Отключение встроенного DNS Keenetic.md","permalink":"/komandy-i-nastrojki/otklyuchenie-vstroennogo-dns-keenetic/","updated":"2024-09-03T16:13:14+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---
Для установки Adguard Home и других задач необходимо выполнить отключение встроенного DNS сервера Keenetic.

Команды можно выполнить при подключении к роутеру по ssh или в web cli по адресу
http://192.168.0.1/a каждая команда отправляется отдельно.

```shell
no opkg dns-override
system configuration save
```

