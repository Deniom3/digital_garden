---
{"dg-publish":true,"permalink":"/zametki/self-hosting-prometheus/","created":"2024-09-10 23:51","updated":"2025-06-10T02:10:19+03:00"}
---

Универсальная система сбора и обработки метрик для организации систем мониторинга различных программ и компьютерных систем. Сбор показателей выполняется по принципу что каждый сервис публикует о себе информацию а Prometheus периодически их опрашивает.

Для Prometheus существует целый комплекс программ для организации мониторинга и реакции на события.

Сайт: [Prometheus - Monitoring system & time series database](https://prometheus.io/)

Документация: [Overview | Prometheus](https://prometheus.io/docs/introduction/overview/)

Репозиторий: [GitHub - prometheus/prometheus: The Prometheus monitoring system and time series database.](https://github.com/prometheus/prometheus)

Докер файл для запуска основного кластера Prometheus представлен в стеке [[Хобби/Docker compose/Monitoring\|Monitoring]] составленном на основе [репозитория](https://github.com/stefanprodan/dockprom).

Для визуализации (при развертывании [[Хобби/Docker compose/Monitoring\|стека]]) можно использовать панели Grafana для контроля состояния систем мониторинга:
![[monitor_services.json]]

> [!idea] Настройки
> - [[Заметки/Self-hosting. Cadvisor\|Self-hosting. Cadvisor]]
> - [[Заметки/Self-hosting. Nodeexpoeter\|Self-hosting. Nodeexpoeter]]
> - [[Заметки/Настройка объема хранимых данных для Prometheus\|Настройка объема хранимых данных для Prometheus]]
> - [[Заметки/Настройка мониторинга Uptime Kuma\|Настройка мониторинга Uptime Kuma]]
> - [[Заметки/Мониторинг OpenWRT с использованием Prometheus\|Мониторинг OpenWRT с использованием Prometheus]]
> - [[Заметки/Мониторинг proxmox через Prometheus\|Мониторинг proxmox через Prometheus]]
> - [[Хобби/Команды и настройки/Установка node exporter как служба\|Настройка мониторинга хоста proxmox]]
> - [[Заметки/Мониторинг traefik через Prometheus\|Мониторинг traefik через Prometheus]]
> - [[Заметки/Мониторинг Crowdsec с использованием Prometheus\|Мониторинг Crowdsec с использованием Prometheus]]
> - [[Заметки/Self-hosting. Adguard exporter\|Self-hosting. Adguard exporter]]
> - [[Заметки/Self-hosting. MySpeed#Настройка мониторинга через Prometheus\|Тестирование скорости интернета через MySpeed]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]

> [!description]- Примечание
> Примечание:: База данных временных рядов для мониторинга
