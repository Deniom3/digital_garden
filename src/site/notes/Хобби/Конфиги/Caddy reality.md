---
{"dg-publish":true,"dg-path":"Конфиги/Caddy reality.md","permalink":"/konfigi/caddy-reality/","tags":[""]}
---


```shell
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
    root * /var/www/html
    try_files {path} /index.html
    file_server

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
> [[Заметки/Маскировка reality под свой сайт\|Маскировка reality под свой сайт]]

> [!description]- Примечание
> Примечание::  Настройка caddy для маскировки reality
