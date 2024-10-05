---
{"dg-publish":true,"dg-path":"Конфиги/Traefik.md","permalink":"/konfigi/traefik/","updated":"2024-10-05T17:36:27+03:00"}
---


```yml
api:
  dashboard: true
  debug: true
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
  metrics:
    address: ":8082"

serversTransport:
  insecureSkipVerify: true
providers:
  file:
    directory: /config
    watch: true
certificatesResolvers:
  cloudflare:
    acme:
      email: <email>@gmail.com
      storage: acme.json
      dnsChallenge:
        provider: cloudflare
        resolvers:
          - "1.1.1.1:53"
          - "1.0.0.1:53"
metrics:
  prometheus:
    entryPoint: metrics
log:
  level: "INFO"
  filePath: "/var/log/traefik/traefik.log"
accessLog:
  filePath: "/var/log/traefik/access.log"

experimental:
  plugins:
    crowdsec-bouncer-traefik-plugin:
      moduleName: "github.com/maxlerebourg/crowdsec-bouncer-traefik-plugin"
      version: "v1.3.4-beta1"
```
