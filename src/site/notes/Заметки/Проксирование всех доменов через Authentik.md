---
{"dg-publish":true,"permalink":"/zametki/proksirovanie-vseh-domenov-cherez-authentik/","created":"2024-08-08 01:26","updated":"2025-06-09T10:45:14+03:00"}
---

Применяется для пропуска всех проектируемых [[Заметки/Self-hosting. Traefik\|Traefik]] приложений через окно аутентификации [[Заметки/Self-hosting. Authentik\|Self-hosting. Authentik]]. Для этого необходимо в настройках динамической конфигурации использовать [[Хобби/Конфиги/Traefik Authentik\|middlewares authentik]].

> [!attention]
> Используемые имена в настройках должны соответвовать настройкам конфигурации middlewares traefik

Пример настроек:
![Проксирование всех доменов через Authentik.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%B2%D1%81%D0%B5%D1%85%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%BE%D0%B2%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20Authentik.png)
![Проксирование всех доменов через Authentik-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%B2%D1%81%D0%B5%D1%85%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%BE%D0%B2%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20Authentik-3.png)
![Проксирование всех доменов через Authentik-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%B2%D1%81%D0%B5%D1%85%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%BE%D0%B2%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20Authentik-2.png)
![Проксирование всех доменов через Authentik-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%BA%D1%81%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%20%D0%B2%D1%81%D0%B5%D1%85%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%BE%D0%B2%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%20Authentik-4.png)
> [!attention] Важно 
> Дополнительно надо проверить параметр authentik_host что бы был аналогично как на скрине.

При таких настройках перед входом на сайт который использует middlewares authentik будет окно авторизации authentik.

---
> [!urls]- Упоминания:
> - 