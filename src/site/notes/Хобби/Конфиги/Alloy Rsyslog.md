---
{"dg-publish":true,"dg-path":"Конфиги/Alloy Rsyslog.md","permalink":"/konfigi/alloy-rsyslog/","tags":[""],"updated":"2024-10-09T19:59:37+03:00"}
---


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
