---
{"dg-publish":true,"permalink":"/zametki/sbor-logov-linux-i-proxmox-dlya-grafana/","created":"2024-10-10 01:50","updated":"2024-10-10T02:00:07+03:00"}
---

Для сбора логов используется связка [[Заметки/Self-hosting. Alloy\|Alloy]] и [[Заметки/Self-hosting. Grafana Loki\|Loki]] которые принимают логи от `rsyslog` и отправляют в [[Заметки/Self-hosting. Grafana\|Grafana]] для анализа и визуализации.
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
### Настройка хоста

Для передачи логов с серверов linux или [[Заметки/Self-hosting. Proxmox Virtual Environment\|Proxmox]] в [[Заметки/Self-hosting. Grafana Loki\|Loki]] можно использовать программу `rsyslog`:

```
apt install rsyslog
```

Необходимо изменить конфигурацию `rsyslog`:
```
nano /etc/rsyslog.conf
```

В конец файла добавить следующий блок:
```
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

Таким образом все логи с хоста будут преобразовываться в формат `rfc5424` который может принимать alloy.

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Grafana Loki\|Grafana Loki]]
> - [[Заметки/Self-hosting. Proxmox Virtual Environment\|Proxmox]]
> - [[Заметки/Self-hosting. Alloy\|Self-hosting. Alloy]]