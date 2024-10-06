---
{"dg-publish":true,"permalink":"/zametki/nastrojka-crowdsec-v-svyazke-s-traefik/","created":"2024-10-06 23:43","updated":"2024-10-06T23:50:40+03:00"}
---

Для работы с [[Заметки/Self-hosting. Traefik\|Traefik]] можно использовать вариант настройки как через внешний bouncer контейнер так и с использованием плагина traefik. Вариант с плагином является более предпочтительным так как этот вариант регулярно обновляется и развивается.

Плагин: [Plugin](https://plugins.traefik.io/plugins/6335346ca4caa9ddeffda116/crowdsec-bouncer-traefik-plugin)

Для настройки необходимо дополнить конфигурацию блоком:
```
---
listen_addr: 0.0.0.0:7422
appsec_config: crowdsecurity/virtual-patching
name: Traefik
source: appsec
labels:
  type: appsec
```

Пример итоговой конфигурации: [[Хобби/Конфиги/Crowdsek acquis\|Crowdsek acquis]]

А так же установить плагин в traefik путем добавления в статическую конфигурацию запись:
```yaml
experimental:
  plugins:
    crowdsec-bouncer-traefik-plugin:
      moduleName: "github.com/maxlerebourg/crowdsec-bouncer-traefik-plugin"
      version: "v1.3.4-beta1"
```

`version` - необходимо изменять руками, нет возможности использовать latest.

Для подключения к Traefik bouncer необходимо сгенерировать LAPI_KEY командой:
```shell
docker exec crowdsec cscli bouncers add bouncer-traefik
```

И добавить промежуточную обработку:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/traefik-crowdsek/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Конфигурация traefik для crowdsek

</div>



Позволяет использовать [[Заметки/Self-hosting. CrowdSec\|crowdsek]] вместе с [[Заметки/Self-hosting. Traefik\|Traefik]]. Необходимо создать отдельный файл в папке `traefik/dynamic`

```yml
http:
    middlewares:
        crowdsec:
            plugin:
                crowdsec-bouncer-traefik-plugin:
                    enabled: true
                    logLevel: INFO
                    updateIntervalSeconds: 15
                    updateMaxFailure: 0
                    defaultDecisionSeconds: 15
                    httpTimeoutSeconds: 10
                    crowdsecMode: stream
                    crowdsecAppsecEnabled: true
                    crowdsecAppsecHost: crowdsec:7422
                    crowdsecAppsecFailureBlock: true
                    crowdsecAppsecUnreachableBlock: true
                    crowdsecLapiKey: <LAPI_KEY>     
                    crowdsecLapiHost: crowdsec:8080
                    crowdsecLapiScheme: http
                    forwardedHeadersTrustedIPs:
                      - 172.21.0.0/16                                                            # Reverse Proxy IP address 
                    clientTrustedIPs:
                      - 192.168.0.0/24
```

А так же добавить обращение к middlewares в [[Хобби/Конфиги/Traefik\|статическую конфигурацию]]
```
...
entryPoints:
  http:
    address: ":80"
    http:
      middlewares:
        - crowdsec@file
      redirections:
        entryPoint:
          to: https
          scheme: https
  https:
    address: ":443"
    http:
      middlewares:
        - crowdsec@file
...
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. CrowdSec\|Self-hosting. CrowdSec]]
> - [[Заметки/Self-hosting. Traefik\|Traefik]]