---
{"dg-publish":true,"permalink":"/zametki/self-hosting-i-perf3-exporter/","tags":[""],"created":"2025-12-16 22:36","updated":"2025-12-17T01:06:46+03:00"}
---

Экспортер Prometheus для показателей сетевой производительности iPerf3.
Позволяет в автоматическом режиме запускать замеры скорости сети с использованием iperf и передавать результаты в [[Заметки/Self-hosting. Prometheus\|Prometheus]]

> [!danger] Важно
> Автоматические замеры могут потреблять значительный объем трафика. Это необходимо учитывать при настройке. Среднее потребление трафика на один замер со скоростью 1Gb/s продолжительностью 5 секунд составляет ~600 Мб.

Репозиторий: https://github.com/edgard/iperf3_exporter

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/i-perf3-exporter/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  iperf3_exporter:
    container_name: iperf3-exporter
    image: ghcr.io/edgard/iperf3_exporter:latest
    ports:
      - '9579:9579'
```


</div></div>


После запуска будет доступен веб интерфейс по указанному порту 
![Self-hosting. iPerf3 exporter.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20iPerf3%20exporter.png)
Для выполнения ручного замера можно воспользоваться обычным запросом браузера используя адрес: 
`http://monitoring.local:9579/probe?target=example.com`
где 
- `monitoring.local:9579` - адрес экспортера в локальной сети
- `example.com` - адрес проверяемого сервера

### Настройка Prometheus:
```
  - job_name: 'iperf3'
    scrape_interval: 1h
    metrics_path: /probe
    static_configs:
      - targets:
        - iperf.server.com
    params:
      port: ['5201']
      # Optional: enable reverse mode
      # reverse_mode: ['true']
      # Optional: enable UDP mode
      #udp_mode: ['true']
      # Optional: set bitrate limit
      #bitrate: ['1G']
      # Optional: set test period
      period: ['5s']
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: monitoring.local:9579  # The iPerf3 exporter's real hostname:port.
```

- В разделе `targets` указывается список серверов замеры к которым необходимо выполнять.
- Лучше использовать `udp_mode` позволяет использовать udp протокол для тестирования с возможностью задавать параметры тестирования а не устанавливать их автоматически.
- Параметр `bitrate` отвечает за предельную скорость с которой будет проводиться тест, это та максимальная скорость которую вы можете получить.
- `period` - время за которое будет проводиться тестирование
- `monitoring.local` - адрес `iperf3-exporter` по которому будет обращаться prometheus для выполнения замера.

### Настройка сервера приемника

Необходимо установить iperf3
```
apt install iperf3
sudo systemctl daemon-reload
sudo systemctl enable iperf3
sudo systemctl start iperf3
sudo systemctl status iperf3
```

Если используется ufw необходимо открыть порты для теста (открывать только для белых ip с которых будет проходить тест):
```
sudo ufw allow from <ip> to any port 5201 proto udp
sudo ufw allow from <ip> to any port 5201 proto tcp
```

### Запрос prometheus

Для получения данных можно использовать запрос:
```
((last_over_time(iperf3_received_bytes[2h]) / last_over_time(iperf3_received_seconds[2h])) * 8) 
```

```
(
    (last_over_time(iperf3_sent_bytes[2h]) / last_over_time(iperf3_sent_seconds[2h]))
    * 8
) 
```

Пример панели:
![[iperf monitoring-1765922599921.json]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]

> [!description]- Примечание
> Примечание:: Мониторинг скорости сети в prometheus
