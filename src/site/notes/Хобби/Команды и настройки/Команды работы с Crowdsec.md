---
{"dg-publish":true,"dg-path":"Команды и настройки/Команды работы с Crowdsec.md","permalink":"/komandy-i-nastrojki/komandy-raboty-s-crowdsec/","updated":"2025-04-05T16:05:10+03:00"}
---

Для получения статуса системы:
```shell
docker exec crowdsec cscli metrics
```

Список заблокированных
```shell
docker exec crowdsec cscli decisions list
```

Ручная блокировка по IP
```shell
docker exec crowdsec cscli decisions add --ip <ip>
```

Снятие блокировки
```shell
docker exec crowdsec cscli decisions delete --ip <ip>
```

Обновление базы данных выполняется командами:
```sh
docker exec crowdsec cscli hub update && docker exec crowdsec cscli hub upgrade
```

Данный процесс можно автоматизировать добавив в cron запись `crontab -e`:
```
* 3 * * * docker exec crowdsec cscli hub update && docker exec crowdsec cscli hub upgrade
```
При такой настройке обновление будет выполняться каждые три часа.
