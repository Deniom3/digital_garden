---
{"dg-publish":true,"permalink":"/zametki/monitoring-open-wrt-s-ispolzovaniem-prometheus/","created":"2024-09-11 00:50","updated":"2024-09-14T01:03:05+03:00"}
---

Для организации сбора метрик с роутера под управлением OpenWrt с использованием Prometheus необходимо выполнить следующие настройки.
## Настройка OpenWrt

Установить пакеты экспорта метрик используя `opkg install` или веб интерфейс:
```sh
prometheus-node-exporter-lua
prometheus-node-exporter-lua-nat_traffic
prometheus-node-exporter-lua-netstat
prometheus-node-exporter-lua-openwrt
prometheus-node-exporter-lua-wifi
prometheus-node-exporter-lua-wifi_stations
```

Внести изменения в файл конфигурации
```
nano /etc/config/prometheus-node-exporter-lua
```
добавить в конец файла конфигурации разрешение публикации на Lan интерфейсе
```
	option listen_interface 'lan'
```

Выполнить перезагрузку:
```sh
/etc/init.d/prometheus-node-exporter-lua restart
```

После перезагрузки метрики будут доступны на интерфейсе роутера по адресу `http://192.168.1.1:9100/metrics`
## Настройка Prometheus

Добавить в конфигурационный файл Prometheus новую задачу сбора данных:
```yaml
	- job_name: "OpenWRT"
	  static_configs:
	    - targets: ["192.168.1.1:9100"]
```

> [!note]
> Для добавления дополнительных роутеров достаточно добавить дополнительные параметры в targets вида `["192.168.1.1:9100", "192.168.2.1:9100"]`
## Мониторинг в Grafana

![Мониторинг OpenWRT с использованием Prometheus.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20OpenWRT%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20Prometheus.png)

Можно использовать следующий дашборд для Grafana:
![[GrafanaOpenWrt.json]]

Описание дашборда на официальном сайте: [OpenWRT | Grafana Labs](https://grafana.com/grafana/dashboards/18153-openwrt/)

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Prometheus\|Prometheus]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Заметки/Операционная система для роутеров OpenWrt\|OpenWrt]]

> [!todo]-
> - [x] Изучить вопрос настройки ➕ 2024-09-11 ⏳ 2024-09-11 ✅ 2024-09-12