---
{"dg-publish":true,"permalink":"/zametki/panel-monitoringa-crowdsec-na-baze-grafana-s-ispolzovaniem-victoria-metrics/","created":"2024-10-06 23:30","updated":"2024-12-08T12:07:03+03:00"}
---

Программа [[Заметки/Self-hosting. Victoria Metrics\|Victoria Metrics]] позволяет организовать легкий сбор данных о события блокировки используя POST уведомления которые генерирует crowdsec. И легко связать их с панелью мониторинга на базе Grafana. Основным преимуществом является легкая масштабируемость данного варианта настройки.

> [!hint] Важно
> После запуска Victoria Metrics дополнительно настраивать не требуется, все работа будет в "режиме из коробки".

### Настройка crowdsec

Внести формат отправки http уведомлений по событию в конфигурационном файле `crowdsec\config\notification\http.yaml`:

```yaml
type: http          
name: http_default  
log_level: info
format: >
  {{- range $Alert := . -}}
  {{- range .Decisions -}}
  {"metric":{"__name__":"cs_lapi_decision","instance":"gateway","country":"{{$Alert.Source.Cn}}","asname":"{{$Alert.Source.AsName}}","asnumber":"{{$Alert.Source.AsNumber}}","latitude":"{{$Alert.Source.Latitude}}","longitude":"{{$Alert.Source.Longitude}}","iprange":"{{$Alert.Source.Range}}","scenario":"{{.Scenario}}","type":"{{.Type}}","duration":"{{.Duration}}","scope":"{{.Scope}}","ip":"{{.Value}}"},"values": [1],"timestamps":[{{now|unixEpoch}}000]}
  {{- end }}
  {{- end -}}
url: http://192.168.0.135:8428/api/v1/import
method: POST
```

> [!attention] Модификация конфигурации
> -  `192.168.0.135` - на адрес установки Victoria Metrics
> - `gateway` - название сервера для разделения разных установок Crowdsec

Внести изменение в файл `crowdsec\config\profiles.yaml` закомментировав параметры:

```yaml
notifications:
  - http_default
```

Проверить настройку можно командой:
```sh
docker exec crowdsec cscli notifications list
```

### Настройка Grafana

В [[Заметки/Self-hosting. Grafana\|Grafana]] добавление Victoria Metrics происходит по правилам Prometheus 
![Self-hosting. CrowdSec-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20CrowdSec-2.png)

Для мониторинга использовать панель [Crowdsec Cyber Threat Insights | Grafana Labs](https://grafana.com/grafana/dashboards/21689-crowdsec-cyber-threat-insights/)
![[GrafanaCrowdsecVictoriaMetrics.json]]
Дополнительные настройки панели не требуются.

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. CrowdSec\|Self-hosting. CrowdSec]]
> - [[Заметки/Self-hosting. Victoria Metrics\|Self-hosting. Victoria Metrics]]
> - [[Заметки/Self-hosting. Grafana\|Self-hosting. Grafana]]