---
{"dg-publish":true,"dg-path":"Конфиги/Traefik Crowdsek.md","permalink":"/konfigi/traefik-crowdsek/","updated":"2024-10-06T00:46:58+03:00"}
---

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