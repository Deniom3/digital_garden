---
{"dg-publish":true,"permalink":"/zametki/sistema-monitoringa-prometheus/","created":"2024-09-10 23:51","updated":"2024-09-14T23:55:11+03:00"}
---

Универсальная система сбора и обработки метрик для организации систем мониторинга различных программ и компьютерных систем. Сбор показателей выполняется по принципу что каждый сервис публикует о себе информацию а Prometheus периодически их опрашивает.

Для Prometheus существует целый комплекс программ для организации мониторинга и реакции на события.

Сайт: [Prometheus - Monitoring system & time series database](https://prometheus.io/)

Документация: [Overview | Prometheus](https://prometheus.io/docs/introduction/overview/)

Репозиторий: [GitHub - prometheus/prometheus: The Prometheus monitoring system and time series database.](https://github.com/prometheus/prometheus)

Докер файл для запуска основного кластера Prometheus представлен в стеке [[Хобби/Docker compose/Monitoring\|Monitoring]] составленном на основе [репозитория]([GitHub - stefanprodan/dockprom: Docker hosts and containers monitoring with Prometheus, Grafana, cAdvisor, NodeExporter and AlertManager](https://github.com/stefanprodan/dockprom)).

Для визуализации (при развертывании [[Хобби/Docker compose/Monitoring\|стека]]) можно использовать панели Grafana для контроля состояния систем мониторинга:
![[monitor_services.json]]

> [!idea] Настройки
> - [[Заметки/Сбор данных о докер контейнерах для мониторинга с использованием cadvisor\|Сбор данных о докер контейнерах для мониторинга с использованием cadvisor]]
> - [[Заметки/Данные о системе с использованием node exporter\|Данные о системе с использованием node exporter]]
> - [[Заметки/Настройка объема хранимых данных для Prometheus\|Настройка объема хранимых данных для Prometheus]]
> - [[Заметки/Настройка мониторинга Uptime Kuma\|Настройка мониторинга Uptime Kuma]]
> - [[Заметки/Мониторинг OpenWRT с использованием Prometheus\|Мониторинг OpenWRT с использованием Prometheus]]
> - [[Заметки/Мониторинг proxmox через Prometheus\|Мониторинг proxmox через Prometheus]]
> - [[Хобби/Команды и настройки/Установка node exporter как служба\|Настройка мониторинга хоста proxmox]]
> - [[Заметки/Мониторинг traefik через Prometheus\|Мониторинг traefik через Prometheus]]
> - [[Заметки/Мониторинг Crowdsec с использованием Prometheus\|Мониторинг Crowdsec с использованием Prometheus]]
> - [[Заметки/Мониторинг AdguardHome с использованием Prometheus\|Мониторинг AdguardHome с использованием Prometheus]]

---
> [!urls]- Упоминания:
> - [[Заметки/Программа визуализации статистики Grafana\|Grafana]]