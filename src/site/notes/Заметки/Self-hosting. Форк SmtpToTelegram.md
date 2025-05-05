---
{"dg-publish":true,"permalink":"/zametki/self-hosting-fork-smtp-to-telegram/","created":"2024-09-01 01:00","updated":"2025-05-05T22:34:25+03:00"}
---

Для отправки сообщений в телеграмм в конкретную тему супергруппы (форума) необходимо использовать форк версию.

Изменение приведено тут: [Add support for chat topics · Issue #57 · KostyaEsmukov/smtp\_to\_telegram · GitHub](https://github.com/KostyaEsmukov/smtp_to_telegram/issues/57)

Готовый репозиторий размещен в моем github [GitHub - Deniom3/smtp\_to\_telegram: A simple program that listens for SMTP and forwards all incoming Email messages to Telegram](https://github.com/Deniom3/smtp_to_telegram)

1. Клонировать репозиторий
```
git clone https://github.com/Deniom3/smtp_to_telegram.git
cd smtp_to_telegram
```
2. Собрать образ докер (не обязательно можно просто воспользоваться docker compose)
```
docker build -t smtp_to_telegram .
```
3. Указать параметры отправки в  `docker-compose.yml`
```
    environment:
      - ST_TELEGRAM_CHAT_IDS=
      - ST_TELEGRAM_BOT_TOKEN=
      - ST_TELEGRAM_MESSAGETHREAD_IDS=
```
3. Запустить 
```
docker compose up -d
```

> [!bug] Санкции
> Из за санкций нет доступа к некоторым сайтам go при сборке необходимо использовать VPN для сайта [The Go Programming Language](https://golang.org)

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Отправка сообщений из почты в телеграмм каналы супер групп
