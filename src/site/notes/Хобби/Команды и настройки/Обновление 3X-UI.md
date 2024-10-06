---
{"dg-publish":true,"dg-path":"Команды и настройки/Обновление 3X-UI.md","permalink":"/komandy-i-nastrojki/obnovlenie-3-x-ui/","updated":"2024-10-06T02:51:56+03:00"}
---

Для обновления 3X-UI установленного в docker выполнить команду:

```shell
cd 3x-ui && docker compose pull 3x-ui && docker compose up -d && docker images && docker rmi $(docker images -f "dangling=true" -q)
```