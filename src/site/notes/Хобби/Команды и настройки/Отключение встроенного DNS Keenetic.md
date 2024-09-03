---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/otklyuchenie-vstroennogo-dns-keenetic/"}
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

