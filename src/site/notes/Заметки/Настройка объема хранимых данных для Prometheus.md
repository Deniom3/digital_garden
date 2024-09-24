---
{"dg-publish":true,"permalink":"/zametki/nastrojka-obema-hranimyh-dannyh-dlya-prometheus/","created":"2024-09-11 00:25","updated":"2024-09-11T00:56:15+03:00"}
---

Основные параметры задаются как команды запуска докер контейнера. Основные параметры `storage.tsdb.retention.time` и `storage.tsdb.retention.size`

Документация: [Storage | Prometheus](https://prometheus.io/docs/prometheus/latest/storage/#operational-aspects)

> [!example] Пример указания настроек
>     command:
>       - --storage.tsdb.retention.time=15d
>       - --storage.tsdb.retention.size=10GB

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Prometheus\|Prometheus]]