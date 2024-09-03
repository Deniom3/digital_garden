---
{"dg-publish":true,"dg-path":"Команды и настройки/Обновление HOMEd Zigbee из песочницы разработки.md","permalink":"/komandy-i-nastrojki/obnovlenie-hom-ed-zigbee-iz-pesochniczy-razrabotki/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---
Для обновления на предрелизную версию из песочницы разработки выполнить следующие команды:

Остановка сервиса
```shell
/etc/init.d/homed-zigbee stop
```

Загрузка новой версии бинарника
```shell

wget -O /usr/bin/homed-zigbee http://sandbox.u236.org/homed-zigbee/homed-zigbee-mips_24kc
```

Запуск сервиса
```shell
/etc/init.d/homed-zigbee start
```