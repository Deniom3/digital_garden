---
{"dg-publish":true,"permalink":"/zametki/monitoring-proxmox-s-ispolzovaniem-prometheus/","created":"2025-11-06 23:30","updated":"2025-11-06T23:44:23+03:00"}
---

В версии [[Заметки/Self-hosting. Proxmox Virtual Environment\|Proxmox]] 9.0 добавили поддержку источника мониторинга OpenTelemetry. Данное изменение позволяет на прямую организовать сбор метрик с использованием [[Заметки/Self-hosting. Prometheus\|Prometheus]] без посредничества в виде pve-exporter или influx как я реализовывал [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|ранее]].

Включение otlp в prometheus, для этого при [[Хобби/Docker compose/Monitoring\|использовании докера]] необходимо добавить в секцию command строку
```
    command:
      - --web.enable-otlp-receiver
```

Настройка proxmox выполняется в разделе `Центр обработки данных -> Сервер метрик -> Добавить -> OpenTelemetry` 

![Мониторинг proxmox с использованием prometheus.png](https://s3.gig.ovh/original/1X/900b394d28f15eca98c270e8f91bb9148356d77d.png)

Путь: `/api/v1/otlp/v1/metrics`  

Метрики будут доступны в prometheus по заданию `proxmox-ve`
![Мониторинг proxmox с использованием prometheus-1.png](https://s3.gig.ovh/original/1X/7359cd8ab126421746e4d6ae7b2f48b4b27013bb.png)

Дашбор [Proxmox VE Dashboard \| Grafana Labs](https://grafana.com/grafana/dashboards/23855-proxmox-ve-dashboard/)

![[23855_rev1.json]]

---
> [!urls]- Упоминания:
> - 