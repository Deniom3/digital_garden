---
{"dg-publish":true,"dg-path":"Конфиги/Пример настроенных уведомлений для Grafana.md","permalink":"/konfigi/primer-nastroennyh-uvedomlenij-dlya-grafana/","updated":"2024-09-22T03:23:01+03:00"}
---

Пример правил уведомлений для Garana Alert

```yaml
apiVersion: 1
groups:
    - orgId: 1
      name: Мониторинг виртуальных серверов
      folder: Уведомления
      interval: 1m
      rules:
        - uid: ddyilw68adaf4f
          title: LXC использование ОЗУ
          condition: C
          data:
            - refId: A
              relativeTimeRange:
                from: 3600
                to: 0
              datasourceUid: fdi0ylj8epn9ca
              model:
                datasource:
                    type: influxdb
                    uid: fdi0ylj8epn9ca
                intervalMs: 1000
                maxDataPoints: 43200
                query: |
                    mem = from(bucket: "proxmox")
                      |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
                      |> filter(fn: (r) => r["_measurement"] == "system")
                      |> filter(fn: (r) => r["_field"] == "mem")
                      |> filter(fn: (r) => r["object"] == "lxc")
                      |> filter(fn: (r) => r["nodename"] == "proxmox")
                      |> filter(fn: (r) => r["_value"] > 0)
                      |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)

                    maxmem = from(bucket: "proxmox")
                      |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
                      |> filter(fn: (r) => r["_measurement"] == "system")
                      |> filter(fn: (r) => r["_field"] == "maxmem")
                      |> filter(fn: (r) => r["object"] == "lxc")
                      |> filter(fn: (r) => r["_value"] > 0)
                      |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)

                    join(
                        tables: {mem:mem, maxmem:maxmem},
                        on: ["_time","_stop","_start","host"]

                    )

                    |> map(fn: (r) => ({
                          _time: r._time,
                          _value: float(v:r._value_mem) / float(v:r._value_maxmem) * float(v:100),
                          host: r.host
                        })
                      )

                    |> group(columns: ["host"])
                refId: A
            - refId: B
              relativeTimeRange:
                from: 3600
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 0
                            - 0
                        type: gt
                      operator:
                        type: and
                      query:
                        params: []
                      reducer:
                        params: []
                        type: avg
                      type: query
                datasource:
                    name: Expression
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: B
                type: reduce
            - refId: C
              relativeTimeRange:
                from: 3600
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 80
                            - 0
                        type: gt
                      operator:
                        type: and
                      query:
                        params: []
                      reducer:
                        params: []
                        type: avg
                      type: query
                datasource:
                    name: Expression
                    type: __expr__
                    uid: __expr__
                expression: B
                intervalMs: 1000
                maxDataPoints: 43200
                refId: C
                type: threshold
          dashboardUid: IfgdXjtnk
          panelId: 29
          noDataState: OK
          execErrState: Error
          for: 1m
          annotations:
            __dashboardUid__: IfgdXjtnk
            __panelId__: "29"
            description: На виртуальном сервере "{{ $labels.host }}" заканчивается ОЗУ, использовано {{ humanize $values.B.Value }}%.
          labels:
            LXC: ОЗУ
          isPaused: false
        - uid: bdylwouk68x6oe
          title: Количество запущенных контейнеров
          condition: C
          data:
            - refId: A
              relativeTimeRange:
                from: 900
                to: 0
              datasourceUid: PBFA97CFB590B2093
              model:
                datasource:
                    type: prometheus
                    uid: PBFA97CFB590B2093
                editorMode: code
                expr: count(count_over_time(container_memory_usage_bytes{image!=""}[1m])) by (instance) - count(count_over_time(container_memory_usage_bytes{image!=""}[5m])) by (instance)
                instant: false
                interval: ""
                intervalFactor: 2
                intervalMs: 15000
                legendFormat: containers
                maxDataPoints: 43200
                range: true
                refId: A
                step: 2
            - refId: B
              relativeTimeRange:
                from: 900
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params: []
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - B
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: B
                type: reduce
            - refId: C
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 0
                            - 0
                        type: lt
                      operator:
                        type: and
                      query:
                        params: []
                      reducer:
                        params: []
                        type: avg
                      type: query
                datasource:
                    name: Expression
                    type: __expr__
                    uid: __expr__
                expression: B
                intervalMs: 1000
                maxDataPoints: 43200
                refId: C
                type: threshold
          dashboardUid: edx4j6dcg38xsc
          panelId: 9
          noDataState: OK
          execErrState: Error
          for: 0s
          annotations:
            __dashboardUid__: edx4j6dcg38xsc
            __panelId__: "9"
            description: Количество запущенных docker на сервере "{{ reReplaceAll "(.*).local:8090" "$1" $labels.instance }}" контейнеров уменьшилось.
          labels:
            Docker: Количество
          isPaused: false
        - uid: ddykla6xrp0xsd
          title: Использование дискового пространства
          condition: C
          data:
            - refId: A
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: PBFA97CFB590B2093
              model:
                datasource:
                    type: prometheus
                    uid: PBFA97CFB590B2093
                editorMode: code
                expr: "(\r\n  node_filesystem_size_bytes{fstype=~\"ext.*|xfs\", mountpoint !~\".*pod.*|/var/log.*|/var/log.hdd.*\"} \r\n  - node_filesystem_free_bytes{fstype=~\"ext.*|xfs\", mountpoint !~\".*pod.*|/var/log.*|/var/log.hdd.*\"}\r\n) * 100 / (\r\n  node_filesystem_avail_bytes{fstype=~\"ext.*|xfs\", mountpoint !~\".*pod.*|/var/log.*|/var/log.hdd.*\"} \r\n  + (\r\n    node_filesystem_size_bytes{fstype=~\"ext.*|xfs\", mountpoint !~\".*pod.*|/var/log.*|/var/log.hdd.*\"} \r\n    - node_filesystem_free_bytes{fstype=~\"ext.*|xfs\", mountpoint !~\".*pod.*|/var/log.*|/var/log.hdd.*\"}\r\n  )\r\n)"
                format: time_series
                instant: false
                interval: ""
                intervalFactor: 1
                intervalMs: 15000
                legendFormat: '{{mountpoint}}'
                maxDataPoints: 43200
                range: false
                refId: A
            - refId: B
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params: []
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - C
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: B
                type: reduce
            - refId: C
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 80
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - D
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: B
                intervalMs: 1000
                maxDataPoints: 43200
                refId: C
                type: threshold
          noDataState: NoData
          execErrState: Error
          for: 1m
          annotations:
            description: |-
                На сервере "{{ reReplaceAll "(.*):9100" "$1" $labels.instance }}" заканчивается место {{ if (gt $labels.mountpoint "/mnt/(.*)") -}} на диске "{{ reReplaceAll "/mnt/(.*)" "$1" $labels.mountpoint }}"
                {{ else -}} на основном диске {{- end }} занято {{ humanize $values.B.Value }}%.
            Занято: занято {{ humanize $values.B.Value }}%.
          labels:
            LXC: Диск
          isPaused: false
        - uid: fdylyfjaoacxse
          title: Использование ОЗУ docker контейнерами
          condition: C
          data:
            - refId: A
              relativeTimeRange:
                from: 900
                to: 0
              datasourceUid: PBFA97CFB590B2093
              model:
                datasource:
                    type: prometheus
                    uid: PBFA97CFB590B2093
                editorMode: code
                expr: 'round((sum by (name, instance)(container_memory_usage_bytes{image!=""}) -  sum by (name, instance) (container_memory_cache{image!=""})) / (1024 * 1024), 2) '
                instant: false
                interval: ""
                intervalFactor: 1
                intervalMs: 15000
                legendFormat: '{{ name }}'
                maxDataPoints: 43200
                metric: container_memory_usage
                range: true
                refId: A
                step: 1
            - refId: B
              relativeTimeRange:
                from: 900
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params: []
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - B
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: B
                type: reduce
            - refId: C
              relativeTimeRange:
                from: 900
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 1024
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - C
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: B
                intervalMs: 1000
                maxDataPoints: 43200
                refId: C
                type: threshold
          dashboardUid: edx4j6dcg38xsc
          panelId: 11
          noDataState: NoData
          execErrState: Error
          for: 1m
          annotations:
            __dashboardUid__: edx4j6dcg38xsc
            __panelId__: "11"
            description: Контейнер "{{ $labels.name}}" на сервере "{{ reReplaceAll "(.*).local:8090" "$1" $labels.instance }}" использует много ОЗУ, занято {{ humanize $values.B.Value }}Mb.
          labels:
            Docker: ОЗУ
          isPaused: false
    - orgId: 1
      name: Мониторинг физических серверов
      folder: Уведомления
      interval: 5m
      rules:
        - uid: adyihsmeykef4b
          title: Proxmox использование ОЗУ
          condition: C
          data:
            - refId: A
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: fdi0ylj8epn9ca
              model:
                datasource:
                    type: influxdb
                    uid: fdi0ylj8epn9ca
                intervalMs: 1000
                maxDataPoints: 43200
                query: |-
                    from(bucket: "proxmox")
                      |> range(start: v.timeRangeStart, stop:v.timeRangeStop)
                      |> filter(fn: (r) =>
                        r._measurement == "memory" and
                        r._field == "memused"
                      )
                      |> filter(fn: (r) => r["host"] == "proxmox")
                      |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
                      |> map(fn: (r) => ({
                            r with
                            _value: float(v: r._value) / 1073741824.0
                          })
                        )
                      |> last()
                refId: A
            - refId: D
              relativeTimeRange:
                from: 600
                to: 0
              datasourceUid: fdi0ylj8epn9ca
              model:
                datasource:
                    type: influxdb
                    uid: fdi0ylj8epn9ca
                intervalMs: 1000
                maxDataPoints: 43200
                query: "from(bucket: \"proxmox\")\r\n  |> range(start: v.timeRangeStart, stop:v.timeRangeStop)\r\n  |> filter(fn: (r) =>\r\n    r._measurement == \"memory\" and\r\n    r._field == \"memtotal\"\r\n  )\r\n  |> filter(fn: (r) => r[\"host\"] == \"proxmox\")\r\n  |> limit(n:1)\r\n  |> map(fn: (r) => ({\r\n      r with\r\n      _value: float(v: r._value) / 1073741824.0\r\n    })\r\n  )"
                refId: D
            - refId: B
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params: []
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - B
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: B
                type: reduce
            - refId: C
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 14
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - C
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: B
                intervalMs: 1000
                maxDataPoints: 43200
                refId: C
                type: threshold
            - refId: E
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 0
                            - 0
                        type: gt
                      operator:
                        type: and
                      query:
                        params: []
                      reducer:
                        params: []
                        type: avg
                      type: query
                datasource:
                    name: Expression
                    type: __expr__
                    uid: __expr__
                expression: D
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: E
                type: reduce
          dashboardUid: IfgdXjtnk
          panelId: 24
          noDataState: NoData
          execErrState: Error
          for: 5m
          annotations:
            __dashboardUid__: IfgdXjtnk
            __panelId__: "24"
            description: Заканчивается оперативная память на хосте Proxmox, использовано {{ humanize $values.B.Value }} из {{ humanize $values.E.Value }} Гб.
          labels:
            Proxmox: ОЗУ
          isPaused: false
        - uid: ddyituh1mrrwgd
          title: Proxmox использование дисков
          condition: C
          data:
            - refId: A
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: fdi0ylj8epn9ca
              model:
                datasource:
                    type: influxdb
                    uid: fdi0ylj8epn9ca
                intervalMs: 1000
                maxDataPoints: 43200
                query: |
                    used = from(bucket: "proxmox")
                      |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
                      |> filter(fn: (r) => r["_measurement"] == "system")
                      |> filter(fn: (r) => r["_field"] == "used")
                      |> filter(fn: (r) => r["nodename"] == "proxmox")
                      |> filter(fn: (r) => r["_value"] > 0)
                      |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)

                    avail = from(bucket: "proxmox")
                      |> range(start: v.timeRangeStart, stop: v.timeRangeStop)
                      |> filter(fn: (r) => r["_measurement"] == "system")
                      |> filter(fn: (r) => r["_field"] == "avail")
                      |> filter(fn: (r) => r["nodename"] == "proxmox")
                      |> filter(fn: (r) => r["_value"] > 0)
                      |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)

                    join(
                        tables: {used: used, avail: avail},
                        on: ["_time","_stop","_start","host"]
                    )
                    |> map(fn: (r) => ({
                          _time: r._time,
                          _value: float(v: r._value_used) / (float(v: r._value_used) + float(v: r._value_avail)) * 100.0,
                          host: r.host
                        })
                      )
                    |> group(columns: ["host"])
                    |> keep(columns: ["_time", "host", "_value"])
                refId: A
            - refId: B
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 0
                            - 0
                        type: gt
                      operator:
                        type: and
                      query:
                        params: []
                      reducer:
                        params: []
                        type: avg
                      type: query
                datasource:
                    name: Expression
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: B
                type: reduce
            - refId: C
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 80
                            - 0
                        type: gt
                      operator:
                        type: and
                      query:
                        params: []
                      reducer:
                        params: []
                        type: avg
                      type: query
                datasource:
                    name: Expression
                    type: __expr__
                    uid: __expr__
                expression: B
                intervalMs: 1000
                maxDataPoints: 43200
                refId: C
                type: threshold
          dashboardUid: IfgdXjtnk
          panelId: 14
          noDataState: NoData
          execErrState: Error
          for: 5m
          annotations:
            __dashboardUid__: IfgdXjtnk
            __panelId__: "14"
            description: На сервере Proxmox заполнен пул памяти "{{ $labels.host }}" занято {{ humanize $values.B.Value }}%
          labels:
            Proxmox: Диск
          isPaused: false
        - uid: edykj0nbfmbr4f
          title: Proxmox нагрузка процессора
          condition: E
          data:
            - refId: A
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: fdi0ylj8epn9ca
              model:
                datasource:
                    type: influxdb
                    uid: fdi0ylj8epn9ca
                intervalMs: 1000
                maxDataPoints: 43200
                query: |
                    from(bucket: "proxmox")
                      |> range(start: v.timeRangeStart, stop:v.timeRangeStop)
                      |> filter(fn: (r) =>
                        r._measurement == "cpustat" and
                        r._field == "cpu"
                      )
                      |> filter(fn: (r) => r["host"] =~ /proxmox/)
                      |> aggregateWindow(every: v.windowPeriod, fn: mean, createEmpty: false)
                      |> map(fn: (r) => ({
                            r with
                            _time: r._time,
                            _value: float(v: r._value) * float(v: 100)
                          })
                        )
                refId: A
            - refId: D
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params: []
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - D
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: A
                intervalMs: 1000
                maxDataPoints: 43200
                reducer: last
                refId: D
                type: reduce
            - refId: E
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: __expr__
              model:
                conditions:
                    - evaluator:
                        params:
                            - 80
                        type: gt
                      operator:
                        type: and
                      query:
                        params:
                            - E
                      reducer:
                        params: []
                        type: last
                      type: query
                datasource:
                    type: __expr__
                    uid: __expr__
                expression: D
                intervalMs: 1000
                maxDataPoints: 43200
                refId: E
                type: threshold
          dashboardUid: IfgdXjtnk
          panelId: 21
          noDataState: NoData
          execErrState: Error
          for: 5m
          annotations:
            __dashboardUid__: IfgdXjtnk
            __panelId__: "21"
            description: На сервере Proxmox высокая нагрузка на процессор.
          labels:
            Proxmox: CPU
          isPaused: false
        - uid: bdyinc12k5gqof
          title: Контроль температуры
          condition: A
          data:
            - refId: A
              relativeTimeRange:
                from: 300
                to: 0
              datasourceUid: PBFA97CFB590B2093
              model:
                datasource:
                    type: prometheus
                    uid: PBFA97CFB590B2093
                editorMode: code
                exemplar: false
                expr: (max by (instance, chip)(node_hwmon_temp_celsius{job=~"Servers"}) > 30) > avg by (instance, chip)(avg_over_time(node_hwmon_temp_celsius{job=~"Servers"}[1h])) * 1.1
                instant: true
                interval: ""
                intervalMs: 15000
                legendFormat: '{{chip}}'
                maxDataPoints: 43200
                range: false
                refId: A
          dashboardUid: xfpJB9FGz
          panelId: 198
          noDataState: OK
          execErrState: Error
          for: 5m
          annotations:
            __dashboardUid__: xfpJB9FGz
            __panelId__: "198"
            description: |-
                Перегрев сервера "{{ reReplaceAll "(.*):9100" "$1" $labels.instance }}", текущая температура {{ if (gt $labels.chip "thermal_thermal_zone(.*)") -}}
                процессора{{ else if (gt $labels.chip "target1:0(.*)") -}}
                HDD{{ else if (gt $labels.chip "target0:0(.*)") -}}
                SSD{{- end }} {{ humanize $values.A.Value }}°C.
          labels:
            Servers: Перегрев
          isPaused: false

```