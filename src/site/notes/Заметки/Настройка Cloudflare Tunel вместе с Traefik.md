---
{"dg-publish":true,"permalink":"/zametki/nastrojka-cloudflare-tunel-vmeste-s-traefik/","created":"2024-08-18 01:15","updated":"2024-10-25T13:00:53+03:00"}
---

Для переадресации запросов через [[Заметки/Self-hosting. Cloudflare Tunel\|Self-hosting. Cloudflare Tunel]]  на внутренний [[Заметки/Self-hosting. Traefik\|Traefik]] необходимо выполнить дополнительные настройки для передачи заголовков. 

Для этого при создании нового подключения необходимо указать следующие настройки:

![Настройка Cloudflare Tunel вместе с Traefik.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20Cloudflare%20Tunel%20%D0%B2%D0%BC%D0%B5%D1%81%D1%82%D0%B5%20%D1%81%20Traefik.png)
Для случая когда в единой сети докера, или указать Ip адрес контейнера/сервера на котором прослушивает traefik входящий трафик на 443 порту.

![Настройка Cloudflare Tunel вместе с Traefik-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20Cloudflare%20Tunel%20%D0%B2%D0%BC%D0%B5%D1%81%D1%82%D0%B5%20%D1%81%20Traefik-1.png)

Включить настройку **HTTP2 connection**, а так же заполнить **Origin Server Name** и **HTTP Host Header** указав https адрес по которому traefik обрабатывает сервис который необходимо пробросить.

---
> [!urls]- Упоминания:
> - 