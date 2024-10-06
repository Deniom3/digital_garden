---
{"dg-publish":true,"dg-path":"Команды и настройки/Переадресация портов через SSH.md","permalink":"/komandy-i-nastrojki/pereadresacziya-portov-cherez-ssh/","updated":"2024-10-06T02:52:56+03:00"}
---

Позволяет выполнить переадресацию удаленного порта сервера на локальную машину

```shell
ssh -L <LocalPort>:localhost:<RemotePort> <ServerIP>
```
