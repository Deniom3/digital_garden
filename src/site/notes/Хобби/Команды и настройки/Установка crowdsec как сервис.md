---
{"dg-publish":true,"dg-path":"Команды и настройки/Установка crowdsec как сервис.md","permalink":"/komandy-i-nastrojki/ustanovka-crowdsec-kak-servis/","tags":[""],"updated":"2025-05-04T18:35:01+03:00"}
---

Для установки на сервер crowdsec как сервис для защиты ssh необходимо.

Добавляем репозиторий:
```bash
curl -s https://install.crowdsec.net | sudo sh
```

Установка:
```bash
sudo apt update && sudo apt install crowdsec
```

Проверка работы:
```bash
sudo cscli collections list
```

> [!note]
> По умолчанию должны быть сразу обнаружены логи ssh и linux

Установка bouncer для блокировки по правилам:
```bash
sudo apt install crowdsec-firewall-bouncer-iptables
```

Проверка bouncer:
```bash
sudo cscli bouncers list
```

Обновление базы данных выполняется командами:
```sh
sudo cscli hub update && sudo cscli hub upgrade
```

Данный процесс можно автоматизировать добавив в cron запись `crontab -e`:

```
crontab -e
```

```
* 3 * * * sudo cscli hub update && sudo cscli hub upgrade
```