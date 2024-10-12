---
{"dg-publish":true,"dg-path":"Команды и настройки/Обновление HOMEd Zigbee из песочницы разработки.md","permalink":"/komandy-i-nastrojki/obnovlenie-hom-ed-zigbee-iz-pesochniczy-razrabotki/","updated":"2024-10-11T01:04:53+03:00"}
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