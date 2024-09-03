---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/pereadresacziya-portov-cherez-ssh/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Позволяет выполнить переадресацию удаленного порта сервера на локальную машину

```shell
ssh -L <LocalPort>:localhost:<RemotePort> <ServerIP>
```
