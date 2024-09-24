---
{"dg-publish":true,"permalink":"/zametki/nastrojka-monitoringa-uptime-kuma/","created":"2024-09-11 00:29","updated":"2024-09-11T00:56:29+03:00"}
---

Uptime Kuma предоставляет из коробки данные для мониторинга показателей с использованием Prometheus подробная настройка описана в документации. Данные метрик доступны из коробки без дополнительных изменений и настроек. 

Документация: [Prometheus Integration · louislam/uptime-kuma Wiki · GitHub](https://github.com/louislam/uptime-kuma/wiki/Prometheus-Integration)

Пример панели мониторинга для Grafana:
![[uptime-kuma.json]]

Конфигурация prometheus:
```yml
scrape_configs:
  - job_name: 'uptime'
    scrape_interval: 30s
    scheme: http
    metrics_path: '/metrics'
    static_configs:
      - targets: ['192.168.0.132:3001']
```

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Uptime Kuma\|Uptime Kuma]]
> - [[Заметки/Self-hosting. Prometheus\|Prometheus]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]