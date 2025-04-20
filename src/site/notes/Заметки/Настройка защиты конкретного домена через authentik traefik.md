---
{"dg-publish":true,"permalink":"/zametki/nastrojka-zashhity-konkretnogo-domena-cherez-authentik-traefik/","created":"2025-04-20 20:49","updated":"2025-04-20T22:11:50+03:00"}
---

В общем виде можно использовать проектирование всех под доменов описанное в заметке [[Заметки/Проксирование всех доменов через Authentik\|Проксирование всех доменов через Authentik]], но если необходимо выполнить отдельную настройку для конкретного домена необходимо выполнить следующие настройки.

Настройки приложения:
![Настройка защиты конкретного домена через authentik traefik.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D1%8B%20%D0%BA%D0%BE%D0%BD%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D0%B3%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20authentik%20traefik.png)
![Настройка защиты конкретного домена через authentik traefik-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D1%8B%20%D0%BA%D0%BE%D0%BD%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D0%B3%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20authentik%20traefik-1.png)
Настройки провайдера:
![Настройка защиты конкретного домена через authentik traefik-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D1%8B%20%D0%BA%D0%BE%D0%BD%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D0%B3%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20authentik%20traefik-2.png)
Во внешних компонентах для authentik Embedded Outpost включить провайдера в перечень точек входа:
![Настройка защиты конкретного домена через authentik traefik-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B7%D0%B0%D1%89%D0%B8%D1%82%D1%8B%20%D0%BA%D0%BE%D0%BD%D0%BA%D1%80%D0%B5%D1%82%D0%BD%D0%BE%D0%B3%D0%BE%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%B0%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20authentik%20traefik-3.png)

Дополнительных настроек не требуется.

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Authentik\|Authentik]]
> - [[Заметки/Self-hosting. Traefik\|Traefik]]