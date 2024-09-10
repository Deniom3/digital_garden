---
{"dg-publish":true,"permalink":"/zametki/sbor-dannyh-o-doker-kontejnerah-dlya-monitoringa-s-ispolzovaniem-cadvisor/","created":"2024-09-10 23:59","updated":"2024-09-11T00:55:48+03:00"}
---

Сadvisor программа для сбора данных о контейнерах виртуализации (в основном докер но поддерживает и альтернативные варианты) которая может легко интегрироваться с Prometheus. Программа предоставляет полноценный веб интерфейс для просмотра всех данных мониторинга.

> [!danger]
> Разработкой занимается Google и образ размещен на их серверах. Из за санкций возможно потребуется доступ к сайту gcr.io через VPN.

Репозиторий: [GitHub - google/cadvisor: Analyzes resource usage and performance characteristics of running containers.](https://github.com/google/cadvisor)

Пример развертывания докер: [[Хобби/Docker compose/Monitoring Node\|Monitoring Node]]

Для визуализации (при развертывании стека с [[Заметки/Данные о системе с использованием node exporter\|Node exporter]]) можно использовать панели Grafana:
![[docker_containers.json]]
![[docker_host.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Программа визуализации статистики Grafana\|Grafana]]
> - [[Заметки/Данные о системе с использованием node exporter\|node exporter]]