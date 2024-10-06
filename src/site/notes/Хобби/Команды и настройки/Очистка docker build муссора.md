---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка docker build муссора.md","permalink":"/komandy-i-nastrojki/ochistka-docker-build-mussora/","updated":"2024-10-06T02:52:42+03:00"}
---

Для проверки занятого на диске пространства можно выполнить команду

```shell
docker system df
```

Для очистки кеша от сборки образов выполнить команду
```shell
docker builder prune
```