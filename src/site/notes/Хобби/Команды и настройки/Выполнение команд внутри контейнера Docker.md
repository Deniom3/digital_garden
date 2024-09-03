---
{"dg-publish":true,"dg-path":"Команды и настройки/Выполнение команд внутри контейнера Docker.md","permalink":"/komandy-i-nastrojki/vypolnenie-komand-vnutri-kontejnera-docker/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---

```shell
docker exec -it <ContainerID> <Comand>
```

Некоторые контейнеры могут не поддерживать часть команд для выполнения во внутреннем окружении, это зависит от того как сформирован Dockerfile.