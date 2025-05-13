---
{"dg-publish":true,"dg-path":"Конфиги/Caddy Netbird.md","permalink":"/konfigi/caddy-netbird/","tags":[""]}
---


```shell
(security_headers) {
    header * {
        Strict-Transport-Security "max-age=3600; includeSubDomains; preload"
        X-Content-Type-Options "nosniff"
        X-Frame-Options "DENY"
        X-XSS-Protection "1; mode=block"
        -Server
        Referrer-Policy strict-origin-when-cross-origin
    }
}

https://{$SELF_STEAL_DOMAIN} {
    import security_headers
	reverse_proxy /signalexchange.SignalExchange/* h2c://127.0.0.1:10000
	reverse_proxy /api/* 127.0.0.1:33073
	reverse_proxy /management.ManagementService/* h2c://127.0.0.1:33073
	reverse_proxy /* 127.0.0.1:8080
}
```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Netbird\|Self-hosting. Netbird]]

> [!description]- Примечание
> Примечание::  Настройка Caddy для Netbird
