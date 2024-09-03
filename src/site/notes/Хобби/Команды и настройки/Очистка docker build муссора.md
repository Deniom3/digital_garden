---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/ochistka-docker-build-mussora/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для проверки занятого на диске пространства можно выполнить команду

```shell
docker system df
```

Для очистки кеша от сборки образов выполнить команду
```shell
docker builder prune
```