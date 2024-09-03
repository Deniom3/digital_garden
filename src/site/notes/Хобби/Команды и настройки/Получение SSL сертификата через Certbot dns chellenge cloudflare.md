---
{"dg-publish":true,"dg-path":"Команды и настройки/Получение SSL сертификата через Certbot dns chellenge cloudflare.md","permalink":"/komandy-i-nastrojki/poluchenie-ssl-sertifikata-cherez-certbot-dns-chellenge-cloudflare/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Создать новый домен третьего уровня для панели в cloudflare.

Создать API token по инструкции:  [Create API token · Cloudflare Fundamentals docs](https://developers.cloudflare.com/fundamentals/api/get-started/create-token/)
У токена должны быть права на изменение DNS зоны.

> [!bug] 
> В последних версиях стандартная установка не содержит плагина на работу с dns (возможно и не содержала ранее) для работы с cloudflare необходимо отдельно устанавливать плагин по инструкции [[Хобби/Команды и настройки/Установка Certbot с плагином cloudflare dns\|Установка Certbot с плагином cloudflare dns]]

Установить certbot
```shell
apt install certbot
```

Создать файл `cloudflare.ini`  с содержимым токена
```
dns_cloudflare_api_token = "<Api token>"
```

Выполнить команду получения сертификата:
```shell
sudo certbot certonly \
  --dns-cloudflare \
  --dns-cloudflare-credentials /root/cloudflare.ini \
  -d <домен>.ru
```

Если надо переносить сертификаты в другую папку после получения надо добавить еще одну команду:
```shell
--deploy-hook "cp /etc/letsencrypt/live/<домен>.ru/fullchain.pem /<целевой путь>/fullchain.pem && cp /etc/letsencrypt/live/<домен>.ru/privkey.pem /<целевой путь>/privkey.pem"
```

Для запуска процесса автоматического обновления:
```shell
certbot renew --dry-run
```
