---
{"dg-publish":true,"dg-path":"Конфиги/Traefik Netbird.md","permalink":"/konfigi/traefik-netbird/","tags":[""],"updated":"2025-10-27T02:25:12+03:00"}
---


```shell
http:
  routers:
# netbird-dashboard
    netbird-dashboard:
      rule: "Host(`netbird.domein.ru`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-dashboard-https-redirect
      service: netbird-dashboard
          
    netbird-dashboard-secure:
      rule: "Host(`netbird.domein.ru`)"
      entrypoints:
        - https
      tls:
        certResolver: cloudflare
      service: netbird-dashboard
# netbird-signal
    netbird-signal:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/signalexchange.SignalExchange/`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-signal-https-redirect
      service: netbird-signal

    netbird-signal-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/signalexchange.SignalExchange/`)"
      entrypoints:
        - https
      service: netbird-signal
      tls:
        certResolver: cloudflare
# netbird-relay
    netbird-relay:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/relay`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-relay-https-redirect
      service: netbird-relay

    netbird-relay-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/relay`)"
      entrypoints:
        - https
      service: netbird-relay
      tls:
        certResolver: cloudflare

# netbird-api
    netbird-api:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/api`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-api-https-redirect
      service: netbird-api

    netbird-api-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/api`)"
      entrypoints:
        - https
      service: netbird-api
      tls:
        certResolver: cloudflare

# netbird-api
    netbird-management:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/management.ManagementService/`)"
      entrypoints:
        - http   
      middlewares:
        - netbird-management-https-redirect
      service: netbird-management

    netbird-management-secure:
      rule: "Host(`netbird.domein.ru`) && PathPrefix(`/management.ManagementService/`)"
      entrypoints:
        - https
      service: netbird-management
      tls:
        certResolver: cloudflare

  middlewares:
    netbird-dashboard-https-redirect:
      redirectScheme:
        scheme: https
    netbird-signal-https-redirect:
      redirectScheme:
        scheme: https
    netbird-relay-https-redirect:
      redirectScheme:
        scheme: https
    netbird-api-https-redirect:
      redirectScheme:
        scheme: https
    netbird-management-https-redirect:
      redirectScheme:
        scheme: https

  services:
    netbird-dashboard:
      loadBalancer:
        servers:
          - url: "http://192.168.0.131:8085"

    netbird-signal:
      loadBalancer:
        servers:
          - url: "h2c://192.168.0.131:10000"

    netbird-relay:
      loadBalancer:
        servers:
          - url: "http://192.168.0.131:3478"

    netbird-api:
      loadBalancer:
        servers:
          - url: "http://192.168.0.131:33073"

    netbird-management:
      loadBalancer:
        servers:
          - url: "h2c://192.168.0.131:33073"

```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Netbird\|Self-hosting. netbird]]

> [!description]- Примечание
> Примечание::  Пример конфигурации traefik для netbird
