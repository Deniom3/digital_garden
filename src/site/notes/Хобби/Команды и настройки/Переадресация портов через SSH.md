---
{"dg-publish":true,"dg-path":"Команды и настройки/Переадресация портов через SSH.md","permalink":"/komandy-i-nastrojki/pereadresacziya-portov-cherez-ssh/","updated":"2024-09-03T16:13:37+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Позволяет выполнить переадресацию удаленного порта сервера на локальную машину

```shell
ssh -L <LocalPort>:localhost:<RemotePort> <ServerIP>
```
