---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/obnovlenie-3-x-ui/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Для обновления 3X-UI установленного в docker выполнить команду:

```shell
cd 3x-ui && docker compose pull 3x-ui && docker compose up -d && docker images && docker rmi $(docker images -f "dangling=true" -q)
```