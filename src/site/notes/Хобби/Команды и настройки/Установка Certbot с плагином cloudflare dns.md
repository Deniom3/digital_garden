---
{"dg-publish":true,"dg-path":"Команды и настройки/Установка Certbot с плагином cloudflare dns.md","permalink":"/komandy-i-nastrojki/ustanovka-certbot-s-plaginom-cloudflare-dns/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Стандартная установка Certbot выполняется без установки плагинов для получения ssl через dns chelenge. Для установки плагинов необходимо устанавливать через pip.

```shell
sudo apt update && sudo apt install python3 python3-venv libaugeas0
```

Если Certbot был ранее установлен то необходимо удалить

```sh
sudo apt-get remove certbot
```

Создание среды питон
```sh
sudo python3 -m venv /opt/certbot/
sudo /opt/certbot/bin/pip install --upgrade pip
```

Установка Certbot
```sh
sudo /opt/certbot/bin/pip install certbot certbot
sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot
sudo certbot certonly --standalone
```

Добавление плагина Cloudflare
```sh
sudo /opt/certbot/bin/pip install certbot-dns-cloudflare
```