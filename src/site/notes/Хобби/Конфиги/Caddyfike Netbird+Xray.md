---
{"dg-publish":true,"dg-path":"Конфиги/Caddyfike Netbird+Xray.md","permalink":"/konfigi/caddyfike-netbird-xray/","tags":[""]}
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

{
    https_port {$SELF_STEAL_PORT}
    default_bind 127.0.0.1
    servers {
        listener_wrappers {
            proxy_protocol {
                allow 127.0.0.1/32
            }
            tls
        }
    }
    auto_https disable_redirects
}

http://{$SELF_STEAL_DOMAIN} {
    bind 0.0.0.0
    redir https://{$SELF_STEAL_DOMAIN}{uri} permanent
}

https://{$SELF_STEAL_DOMAIN} {
    import security_headers
	reverse_proxy /signalexchange.SignalExchange/* h2c://127.0.0.1:10000
	reverse_proxy /api/* 127.0.0.1:33073
	reverse_proxy /management.ManagementService/* h2c://127.0.0.1:33073
	reverse_proxy /* 127.0.0.1:8080
}

:{$SELF_STEAL_PORT} {
    tls internal
    respond 204
}

:80 {
    bind 0.0.0.0
    respond 204
}

```

---
> [!urls]- Упоминания:

> [!description]- Примечание
> Примечание::  Файл конфигурации для Netbird и Xray
