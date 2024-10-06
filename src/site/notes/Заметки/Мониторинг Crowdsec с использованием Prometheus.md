---
{"dg-publish":true,"permalink":"/zametki/monitoring-crowdsec-s-ispolzovaniem-prometheus/","created":"2024-09-14 01:20","updated":"2024-10-06T19:33:04+03:00"}
---

[[Заметки/Self-hosting. CrowdSec\|CrowdSec]] поддерживает из коробки публикацию метрик в формате prometheus. Объем публикуемых данных достаточно скудный и позволяет получить мало информации.

Документация по настройке: [CrowdSec](https://docs.crowdsec.net/docs/observability/prometheus/)
## Настройки Crowdsec

Добавить в файл конфигурации `/crowdsec/config/config.yaml` блок включения мониторинга:
```
prometheus:
  enabled: true
  level: full
  listen_addr: 0.0.0.0
  listen_port: 6060
```

Внести изменения в докер файл добавив проброс порта публикации метрик из контейнера docker.

```yaml
...
    ports:
      - 6060:6060
...
```

## Настройка Prometheus

Добавить в конфигурационный файл [[Заметки/Self-hosting. Prometheus\|Prometheus]] новую задачу сбора данных:
```yaml
#Crowdsec
  - job_name: "Crowdsec"
    scrape_interval: 5s
    static_configs:
      - targets: ["192.168.0.131:6060"]
        labels:
          machine: 'gateway'
```

## Мониторинг в Grafana

![Мониторинг Crowdsec с использованием Prometheus.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20Crowdsec%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20Prometheus.png)

Можно использовать следующий дашборд для [[Заметки/Self-hosting. Grafana\|Grafana]]:
![[GrafanaCrowdsec.json]]

Описание дашборда на официальном сайте: [Crowdsec Metrics | Grafana Labs](https://grafana.com/grafana/dashboards/21419-crowdsec/)

Или использовать официальные дашборы представленные командой CrowdSec: [GitHub - crowdsecurity/grafana-dashboards: Grafana dashboards for Crowdsec monitoring using Prometheus](https://github.com/crowdsecurity/grafana-dashboards)

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Prometheus\|Prometheus]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Заметки/Self-hosting. CrowdSec\|CrowdSec]]

> [!todo]-
> - [/] Изучить настройку мониторинга Crowdsec с использованием [[Статьи/Анализ киберугроз с помощью Crowdsec, VictoriaMetrics и Grafana\|Анализ киберугроз с помощью Crowdsec, VictoriaMetrics и Grafana]] ➕ 2024-09-14
