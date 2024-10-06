---
{"dg-publish":true,"dg-path":"Команды и настройки/Работа с docker compose.md","permalink":"/komandy-i-nastrojki/rabota-s-docker-compose/","updated":"2024-10-06T02:50:27+03:00"}
---

Для запуска контейнера выполнить команду в папке где размещен файл docker-compose.yml

```console
docker compose up -d
```
Если не указан ключ d то вывод будет вестись прямо в консоль и прерывание остановит контейнер.

Для остановки выполнить в той же папке команду
```console
docker compose stop
```