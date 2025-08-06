---
{"dg-publish":true,"permalink":"/zametki/dopolnenie-i-obedinenie-docker-compose-fajlov/","created":"2025-08-06 20:57","updated":"2025-08-06T21:37:05+03:00"}
---

При использовании [[Заметки/Docker\|Docker]] compose можно использовать механизм объединения разных файлов запуска в единый стек. Для работы необходимо создать в той же директории файл с именем `compose.override.yaml`  в котором указать дополнительные контейнеры или дополнить свойства создаваемых контейнеров.

Подробно описано в документации: [Merge \| Docker Docs](https://docs.docker.com/compose/how-tos/multiple-compose-files/merge/)

> [!note]
> Данный механизм очень удобен когда есть поставляемый файл запуска docker compose который необходимо переопределить или дополнить например метками для watchtower или traefik.

---
> [!urls]- Упоминания:
> - 