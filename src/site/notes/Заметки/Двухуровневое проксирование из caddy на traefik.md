---
{"dg-publish":true,"permalink":"/zametki/dvuhurovnevoe-proksirovanie-iz-caddy-na-traefik/","created":"2025-06-19 23:46","updated":"2025-06-19T23:52:23+03:00"}
---

В моей сети основным прокси выступает [[Заметки/Self-hosting. Traefik\|Traefik]]  но для публикации сервисов в интернет я использую удаленный сервер где основным прокси выступает [[Заметки/Self-hosting. Caddy\|Caddy]] из за своей простоты и возможности поднимать сайты. 

Для организации проксирования из caddy на traefik с сохранением всех заголовков можно использовать следующий блок конфигурации Caddyfile:

```Caddyfile
https://<subdomein>.<domein>.ru {
    reverse_proxy https://<ip_traefik> {
        header_up Host {host}
        header_up X-Real-IP {remote_host}
        header_up X-Forwarded-For {remote_host}
        header_up X-Forwarded-Proto {scheme}

        transport http {
            tls_insecure_skip_verify
        }
    }
}
```

> [!note]
> Такой вариант передачи заголовков должен работать для любого нижестоящего прокси не только для traefik.

---
> [!urls]- Упоминания:
> - 