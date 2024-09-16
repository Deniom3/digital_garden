---
{"dg-publish":true,"permalink":"/zametki/testirovanie-skorosti-interneta-s-pomoshhyu-my-speed/","created":"2024-09-16 22:22","updated":"2024-09-16T23:04:11+03:00"}
---

Программа MySpeed предназначена для автоматического измерения скорости интернета с удобной визуализацией и простой настройкой. Имеет простой и красивый дашборд и точку интеграции с prometheus.

Имеет возможность тестировать на основании одного из трех провайдеров:
- Ookla (speed test)
- LibreSpeed
- Cloudflare

> [!note]
> Основным преимуществом по сравнению с тестированием скорости интернета через SpeedTest на базе Home Assistant является несколько другой набор серверов и возможность жесткой настройки сервера тестирования (в Home Assistant сервера плавающие и могут пропадать).

Интерфейс:
![Тестирование скорости интернета с помошью MySpeed-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%A2%D0%B5%D1%81%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D1%81%D0%BA%D0%BE%D1%80%D0%BE%D1%81%D1%82%D0%B8%20%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82%D0%B0%20%D1%81%20%D0%BF%D0%BE%D0%BC%D0%BE%D1%88%D1%8C%D1%8E%20MySpeed-1.png)
Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
services:
  myspeed:
    image: germannewsmaker/myspeed
    container_name: myspeed
    ports:
      - 5216:5216
    volumes:
      - myspeed:/myspeed/data
    restart: unless-stopped
    networks:
      - monitoring
volumes:
  myspeed: {}
networks:
  monitoring:
    name: monitoring
    external: true
```



</div></div>


## Настройка мониторинга через Prometheus

Добавить в конфигурационный файл Prometheus новую задачу сбора данных:
```yaml
#myspeed
  - job_name: 'myspeed'
    scrape_interval: 1m # Je nach Testzeitraum anpassen
    scrape_timeout: 8s
    metrics_path: /api/prometheus/metrics
    static_configs:
      - targets: ['myspeed:5216']
```

Метрики prometheus доступны по умолчанию в api по адресу `/api/prometheus/metrics` и не требуют дополнительных настроек.
## Мониторинг в Grafana

![Тестирование скорости интернета с помошью MySpeed-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%A2%D0%B5%D1%81%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D1%81%D0%BA%D0%BE%D1%80%D0%BE%D1%81%D1%82%D0%B8%20%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82%D0%B0%20%D1%81%20%D0%BF%D0%BE%D0%BC%D0%BE%D1%88%D1%8C%D1%8E%20MySpeed-2.png)

Можно использовать следующий дашборд для Grafana:
![[GrafanaMySpeed.json]]

Описание дашборда на официальном сайте: [MySpeed | Grafana Labs](https://grafana.com/grafana/dashboards/21647-myspeed/)

---
> [!urls]- Упоминания:
> - [[Заметки/Система мониторинга Prometheus\|Prometheus]]
> - [[Заметки/Программа визуализации статистики Grafana\|Grafana]]