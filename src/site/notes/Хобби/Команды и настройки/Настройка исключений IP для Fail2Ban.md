---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка исключений IP для Fail2Ban.md","permalink":"/komandy-i-nastrojki/nastrojka-isklyuchenij-ip-dlya-fail2-ban/","updated":"2024-10-06T02:52:25+03:00"}
---

Сделать это можно, отредактировав файл jail.local:

```
nano /etc/fail2ban/jail.local
```

Добавьте следующие строки, заменив ip1 и ip2 на доверенные адреса:

```
[DEFAULT]
ignoreip = ip1 ip2
```

ignoreip — перечень IP-адресов, которые не должны блокироваться сервисом ни при каких условиях. В этом параметре можно также задать маску подсети или имя DNS-сервера.

Перезагрузить для применения изменений:

```
systemctl restart fail2ban
```
