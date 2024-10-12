---
{"dg-publish":true,"permalink":"/zametki/nastrojka-ldap-authentik/","created":"2024-08-08 01:33","updated":"2024-10-12T18:43:56+03:00"}
---

Основная инструкция: [authentik](https://docs.goauthentik.io/docs/providers/ldap/)

> [!attention] Внимание
> Создание отдельного outpost при запуске через докер необходимо выполнять из интерфейса программы. А таком случае будет запущен отдельный контейнер с правильными настройками.

Для каждого отдельного приложения которое будет проходить LDAP аутентификацию необходимо создать свою группу. Только пользователи этой группы смогут входить через LDAP.

![Настройка LDAP Authentik.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik.png)
> [!note]
> Отдельного пользователя как администратора LDAP создавать не обязательно можно использовать просто администратора Authentik.

Настройки `Directory -> Groups`
![Настройка LDAP Authentik-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-1.png)
Настройки `Flows and Stages -> Stages`
![Настройка LDAP Authentik-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-2.png)
![Настройка LDAP Authentik-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-3.png)
![Настройка LDAP Authentik-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-4.png)
Настройки `Flows and Stages -> Flows`
![Настройка LDAP Authentik-5.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-5.png)
![Настройка LDAP Authentik-6.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-6.png)
![Настройка LDAP Authentik-7.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-7.png)
Настройки `Applications -> Outposts`
![Настройка LDAP Authentik-9.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-9.png)
![Настройка LDAP Authentik-8.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20LDAP%20Authentik-8.png)

Настройка конкретного примера авторизации: [[Заметки/Авторизация Calibre через Authentik\|Авторизация Calibre через Authentik]]

---
> [!urls]- Упоминания:
> - 