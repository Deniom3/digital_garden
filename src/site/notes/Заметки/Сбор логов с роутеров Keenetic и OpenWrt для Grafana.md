---
{"dg-publish":true,"permalink":"/zametki/sbor-logov-s-routerov-keenetic-i-open-wrt-dlya-grafana/","created":"2024-10-08 23:46","updated":"2024-10-10T01:21:23+03:00"}
---

В роутерах [[Заметки/Роутеры Keenetic\|Keenetic]] и [[Заметки/Операционная система для роутеров OpenWrt\|OpenWrt]] есть встроенная возможность пересылать журнал логов на удаленный сервер с использованием Sysylog. Данный функционал можно удобно использовать для организации мониторинга роутера по логам с использованием [[Заметки/Self-hosting. Grafana\|Grafana]].

Для организации сбора и обработки логов используется связка [[Заметки/Self-hosting. Grafana Loki\|Grafana Loki]] + [[Заметки/Self-hosting. Alloy\|Alloy]] . Для сбора используется компонента `syslog`.

### Настройка сбора логов через Alloy
Общий файл конфигурации:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/alloy-rsyslog/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
loki.relabel "syslog" {
  forward_to = []

  rule {
    source_labels = ["__syslog_message_hostname"]
    target_label  = "host"
  }
}
loki.source.syslog "syslog" {
  listener {
    address  = "0.0.0.0:1514"
    protocol = "tcp"
    labels   = { component = "loki.source.syslog", protocol = "tcp"}
  }

  listener {
    address  = "0.0.0.0:1514"
    protocol = "udp"
    labels   = { component = "loki.source.syslog", protocol = "udp"}  
  }

  relabel_rules = loki.relabel.syslog.rules
  forward_to = [loki.write.local.receiver]
}

loki.write "local" {
    endpoint {
        url = "http://loki:3100/loki/api/v1/push"
    }
    external_labels = {}
}
```
---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Alloy\|Self-hosting. Alloy]]

> [!description]-
> Примечание::  Сбор логов Keenetic


</div></div>


> [!bug]
> В текущей версии Alloy прием компонентой `syslog` поддерживает только формат `rfc5424`. Данный формат можно переопределить параметром `syslog_format` но он не работает в текущей версии.

Описание компоненты: [alloy/docs/sources/reference/components/loki/loki.source.syslog.md at main · grafana/alloy · GitHub](https://github.com/grafana/alloy/blob/main/docs/sources/reference/components/loki/loki.source.syslog.md)

Документация: [loki.source.syslog | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/loki/loki.source.syslog/)

### Настройка  rsyslog

Для обхода проблемы форматов можно использовать rsyslog как преобразователь логов с дальнейшей пересылкой в Alloy.

Для этого необходимо создать файл `/etc/rsyslog.d/keenetik.conf` со следующим содержимым:
```
# Load the necessary modules
module(load="imuxsock")  # local system logging
module(load="imudp")      # UDP logging
input(type="imudp" port="514")
module(load="imtcp")      # TCP logging
input(type="imudp" port="514")

# Forward everything to the remote server
*.* action(type="omfwd"
        protocol="tcp"
        target="192.168.0.135"
        port="1514"
        Template="RSYSLOG_SyslogProtocol23Format"
        TCP_Framing="octet-counted"
        KeepAlive="on"
        action.resumeRetryCount="-1"
        queue.type="linkedlist"
        queue.size="50000")
```

Перезапустить сервис:
```
sudo systemctl restart rsyslog
sudo systemctl status rsyslog
```

Таким образом все собранные логи от роутера на порту `514/UDP` будут переправлять на порт `1514` по протоколу `tcp` на сервер `192.168.0.135`. Можно запустить на том же сервере где работает и Alloy.

> [!note]
> Дополнительная настройка Loki не требуется, он должен быть доступен для Grafana и Alloy.

### Настройка роутера Keenetic

На роутере необходимо включить передачу логов на удаленный сервер в настройках `Управление` -> `Диагностика` -> `Системный журнал` -> `Использовать Syslog`

![Сбор логов с роутера Keenetik для Grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%A1%D0%B1%D0%BE%D1%80%20%D0%BB%D0%BE%D0%B3%D0%BE%D0%B2%20%D1%81%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%B0%20Keenetik%20%D0%B4%D0%BB%D1%8F%20Grafana.png)

Указанный сервер должен принимать логи на 514 порту по UDP протоколу.

> [!Attention] Особенность
> Keenetik передает логи в формате `rfc3164`, не все программы сбора данных по умолчанию работают с устаревшим форматом.

Подробная настройка роутера для других программ: [Saving system event logs to a Syslog server – Keenetic](https://help.keenetic.com/hc/en-us/articles/213966129-Saving-system-event-logs-to-a-Syslog-server)

Доработанный дашборд для grafana:
![[GrafanaKeenetic and Logs.json]]
### Настройка роутера OpenWrt

На роутере необходимо указать сервер удаленного журналирования. Для этого надо зайти `Система` -> `Журналирование`

![Сбор логов с роутеров Keenetic и OpenWrt для Grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%A1%D0%B1%D0%BE%D1%80%20%D0%BB%D0%BE%D0%B3%D0%BE%D0%B2%20%D1%81%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20Keenetic%20%D0%B8%20OpenWrt%20%D0%B4%D0%BB%D1%8F%20Grafana.png)

Доработанный дашборд для grafana:
![[OpenWRT and Logs.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Роутеры Keenetic\|Роутеры Keenetic]]
> - [[Заметки/Self-hosting. Alloy\|Self-hosting. Alloy]]
> - [[Заметки/Операционная система для роутеров OpenWrt\|OpenWrt]]
> - [[Заметки/Self-hosting. Grafana Loki\|Loki]]