---
{"dg-publish":true,"dg-path":"Команды и настройки/Шифрование пароля для basicauth Caddy.md","permalink":"/komandy-i-nastrojki/shifrovanie-parolya-dlya-basicauth-caddy/","tags":[""],"updated":"2024-10-13T20:33:28+03:00"}
---



```shell
docker exec caddy caddy hash-password --plaintext <Password>
```

> [!attention] Важно
> При использовании в докере необходимо экранировать все символы `$` заменой на `$$` 