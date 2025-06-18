---
{"dg-publish":true,"dg-path":"Команды и настройки/Команды работы с Crowdsec.md","permalink":"/komandy-i-nastrojki/komandy-raboty-s-crowdsec/","updated":"2025-06-03T15:24:57+03:00"}
---

Для получения статуса системы:
```shell
cscli metrics
```

Список заблокированных
```shell
cscli decisions list
```

Ручная блокировка по IP
```shell
cscli decisions add --ip <ip>
```

Снятие блокировки
```shell
cscli decisions delete --ip <ip>
```

Обновление базы данных выполняется командами:
```sh
cscli hub update && docker exec crowdsec cscli hub upgrade
```

Данный процесс можно автоматизировать добавив в cron запись `crontab -e`:
```
* 3 * * * cscli hub update && docker exec crowdsec cscli hub upgrade
```
При такой настройке обновление будет выполняться каждые три часа.


---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. CrowdSec\|CrowdSec]]