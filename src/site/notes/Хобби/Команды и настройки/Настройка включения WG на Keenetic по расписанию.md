---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка включения WG на Keenetic по расписанию.md","permalink":"/komandy-i-nastrojki/nastrojka-vklyucheniya-wg-na-keenetic-po-raspisaniyu/","updated":"2024-09-03T16:10:58+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---
Настройка выполняется через web cli 

```shell
schedule schedule0
description wg
action start 0 7 *
action stop 0 20 *
exit
interface Wireguard0
schedule schedule0
exit
system configuration save
```

Подробно смотри в руководстве пользователя по работе с cli страница 264 и 459

![[cli_manual_kn-3810_ru 1.pdf#page=459]]