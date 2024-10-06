---
{"dg-publish":true,"dg-path":"Команды и настройки/Выполнение команд внутри контейнера Docker.md","permalink":"/komandy-i-nastrojki/vypolnenie-komand-vnutri-kontejnera-docker/","updated":"2024-10-06T02:54:24+03:00"}
---


```shell
docker exec -it <ContainerID> <Comand>
```

Некоторые контейнеры могут не поддерживать часть команд для выполнения во внутреннем окружении, это зависит от того как сформирован Dockerfile.