---
{"dg-publish":true,"dg-path":"Конфиги/Remote Server Monitoring Caddyfile.md","permalink":"/konfigi/remote-server-monitoring-caddyfile/","tags":[""],"updated":"2025-05-13T21:55:24+03:00"}
---


```shell
:3001 {
    reverse_proxy 10.2.1.4:3001  
}

:8090 {
    basicauth {
        {$ADMIN_USER} {$ADMIN_PASSWORD_HASH}
    }    
    reverse_proxy 10.2.1.2:8080
}

:2375 {
    reverse_proxy 10.2.1.3:2375
}

:9100 {
    basicauth {
        {$ADMIN_USER} {$ADMIN_PASSWORD_HASH}
    }
    reverse_proxy 10.2.1.6:9100
}

:2112 {
    basicauth {
        {$ADMIN_USER} {$ADMIN_PASSWORD_HASH}
    }    
    reverse_proxy 10.2.1.5:2112
}
```

---
> [!urls]- Упоминания:

> [!description]- Примечание
> Примечание::  Параметры проксирования стека мониторинга
