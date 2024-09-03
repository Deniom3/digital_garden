---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/nastrojka-crowd-sec-v-dokere/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

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