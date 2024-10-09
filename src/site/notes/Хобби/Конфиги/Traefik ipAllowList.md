---
{"dg-publish":true,"dg-path":"Конфиги/Traefik ipAllowList.md","permalink":"/konfigi/traefik-ip-allow-list/","updated":"2024-10-09T21:26:13+03:00"}
---

Необходимо создать отдельный файл в папке `traefik/dynamic`

```yml
http:
  middlewares:
    ip-allow-list:
      ipAllowList:
        sourceRange:
          - "192.168.0.0/24"
          - "192.168.1.0/24"
          - "192.168.2.0/24"
          - "192.168.4.0/24"
```

Подробнее: [Traefik HTTP Middlewares IPAllowList - Traefik](https://doc.traefik.io/traefik/middlewares/http/ipallowlist/)