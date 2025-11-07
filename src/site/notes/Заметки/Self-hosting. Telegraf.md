---
{"dg-publish":true,"permalink":"/zametki/self-hosting-telegraf/","created":"2024-07-03 22:27","updated":"2025-04-20T20:26:37+03:00"}
---

Агент для сбора различных серверных аналитик с открытым исходным кодом. Позволяет собирать различные данные для организации мониторинга систем и передавать их в различные системы сбора и обработки статистики. В основном используется в связке с influxdb, но так же может писать в логи и в mqtt и и.д.

Имеет множество различных плагинов для расширения функционала: [Plugin directory | Telegraf Documentation](https://docs.influxdata.com/telegraf/v1/plugins/)

Официальный сайт: [Telegraf | InfluxData](https://www.influxdata.com/time-series-platform/telegraf/)
### Мониторинг linux системы

Docker compose файл:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Telegraf

</div>




Конфиг настроек [[Хобби/Конфиги/Telegraf\|файле конфигурации]], а так же хранится в influxdb

```yaml
version: "3"
services:
  telegraf:
    image: telegraf:latest
    container_name: telegraf
    environment:
      - HOST_ETC=/hostfs/etc
      - HOST_PROC=/hostfs/proc
      - HOST_SYS=/hostfs/sys
      - HOST_VAR=/hostfs/var
      - HOST_RUN=/hostfs/run
      - HOST_MOUNT_PREFIX=/hostfs
      - TZ=Europe/Moscow
    volumes:
      - /:/hostfs:ro
      - /docker/telegraf/telegraf.conf:/etc/telegraf/telegraf.conf:ro
```



</div></div>
 
Конфигурация:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Telegraf

</div>




```conf
[global_tags]
[agent]
  interval = "30s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "10s"
  flush_jitter = "0s"
  precision = ""
  debug = false
  quiet = false
  hostname = "Armbian"
  omit_hostname = false

[[outputs.influxdb_v2]]
  urls = ["http://192.168.0.135:8086"]
  token = "<TOKEN>"
  organization = "influxdb"
  bucket = "system-monitoring"

[[inputs.cpu]]
  percpu = false
  totalcpu = true
  collect_cpu_time = false
  report_active = false
[[inputs.disk]]
  mount_points = ["/"]
[[inputs.diskio]]
[[inputs.linux_sysctl_fs]]
[[inputs.mem]]
[[inputs.nstat]]
[[inputs.netstat]]
[[inputs.processes]]
[[inputs.swap]]
[[inputs.system]]
[[inputs.temp]]
```

</div></div>


### Мониторинг OpenWrt

Docker compose файл:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





Конфиг настроек  [[Хобби/Конфиги/Telegraf OpenWrt\|файле конфигурации]], а так же хранится в influxdb

```
version: "3"
services:
  telegraf-openwrt:
    image: telegraf:latest
    container_name: telegraf-openwrt
    environment:
      - TZ=Europe/Moscow
    volumes:
      - /docker/telegraf-openwrt/telegraf.conf:/etc/telegraf/telegraf.conf:ro
      - /docker/telegraf-openwrt/types.db:/usr/share/collectd/types.db
    ports:
      - "8094:8094/udp"
```


</div></div>

Конфигурация:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```conf
[global_tags]

[agent]
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "10s"
  flush_jitter = "0s"
  precision = "0s"
  hostname = "Perenio"
  omit_hostname = false

[[outputs.influxdb_v2]]
urls = ["http://192.168.0.137:8086"]
token = "<TOKEN>"
organization = "influxdb"
bucket = "openwrt"

[[inputs.socket_listener]]
  service_address = "udp://0.0.0.0:8094"
  data_format = "collectd"
  collectd_typesdb = ["/usr/share/collectd/types.db"]

```

Файл базы: ![[types.db]]

</div></div>


### Мониторинг NUT

Docker compose файл:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Telegraf-nut

</div>




Конфиг настроек [[Хобби/Конфиги/Telegraf-Nut\|файле конфигурации]], а так же хранится в influxdb

```yaml
version: "3"
services:
  telegraf-nut:
    image: telegraf:latest
    container_name: telegraf-nut
    environment:
      - TZ=Europe/Moscow
    volumes:
      - /docker/telegraf-nut/telegraf.conf:/etc/telegraf/telegraf.conf:ro
```



</div></div>

Конфигурация:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Telegraf-Nut

</div>




```conf
[agent]
  interval = "10s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_jitter = "0s"
  precision = ""
  hostname = "Armbian"
  omit_hostname = false
[[outputs.influxdb_v2]]
  urls = ["http://192.168.0.135:8086"]
  token = "<TOKEN>"
  organization = "influxdb"
  bucket = "nut"
[[inputs.upsd]]
  server = "192.168.0.137"
  port = 3493
  username = "deniom"
  password = "Ap256216"
  
  additional_fields = ["*"]

[[processors.enum]]
  [[processors.enum.mapping]]
    field = "ups_beeper_status"
    [processors.enum.mapping.value_mappings]
      true = "enabled"
      false = "disabled"
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Armbian - не используется\|Сервер Armbian - не используется]]
>- [[Статьи/Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana\|Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana]]
>- [[Статьи/Мониторинг роутеров openwrt в influxdb v2 и grafana\|Мониторинг роутеров openwrt в influxdb v2 и grafana]]
>- [[Статьи/Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа\|Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа]]

> [!description]- Примечание
> Примечание:: Сбор данных мониторинга

