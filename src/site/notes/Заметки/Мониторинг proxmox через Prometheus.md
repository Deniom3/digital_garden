---
{"dg-publish":true,"permalink":"/zametki/monitoring-proxmox-cherez-prometheus/","created":"2024-09-11 00:50","updated":"2024-10-10T00:48:44+03:00"}
---


> [!note]
> Существующие решения собирают меньше данных чем предоставляет сам proxmox при использовании influx. Так как нет возможности отказаться от использования influx из за роутеров keenetic нет смысла переходить и на более слабое решение.

Вариант для запуска на удаленном компьютере [GitHub - Starttoaster/proxmox-exporter: A Prometheus metrics exporter for Proxmox VE](https://github.com/starttoaster/proxmox-exporter)

Вариант для запуска на хосте: [GitHub - prometheus-pve/prometheus-pve-exporter: Exposes information gathered from Proxmox VE cluster for use by the Prometheus monitoring system](https://github.com/prometheus-pve/prometheus-pve-exporter)

Дашборд [Proxmox via Prometheus | Grafana Labs](https://grafana.com/grafana/dashboards/10347-proxmox-via-prometheus/)

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Proxmox Virtual Environment\|Proxmox]]
> - [[Заметки/Self-hosting. Prometheus\|Prometheus]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]

> [!todo]-
> - [x] Изучить вопрос настройки ➕ 2024-09-11 ⏳ 2024-09-12 ✅ 2024-09-12