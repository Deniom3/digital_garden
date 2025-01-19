---
{"dg-publish":true,"dg-path":"Команды и настройки/Управление Adguard Home.md","permalink":"/komandy-i-nastrojki/upravlenie-adguard-home/","updated":"2024-12-22T18:43:41+03:00"}
---

Для запуска [[Заметки/Self-hosting. Adguard Home\|Adguard Home]] на роутере необходимо подключиться по ssh к Entware порт 222 и использовать команду:

```console
/opt/etc/init.d/S99adguardhome start
```

Для остановки 

```console
/opt/etc/init.d/S99adguardhome stop
```
