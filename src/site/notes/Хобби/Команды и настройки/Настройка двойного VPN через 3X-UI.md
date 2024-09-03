---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка двойного VPN через 3X-UI.md","permalink":"/komandy-i-nastrojki/nastrojka-dvojnogo-vpn-cherez-3-x-ui/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
# Получение SSL сертификата

Получить сертификат используя [[Хобби/Команды и настройки/Получение SSL сертификата через Certbot dns chellenge cloudflare\|Certbot dns chellenge]] 

```shell
sudo certbot certonly \
	--dns-cloudflare \  
	--dns-cloudflare-credentials /root/3x-ui/cert/cloudflare.ini \
	-d ui-home.<domain>.ru \
	--deploy-hook "cp /etc/letsencrypt/live/ui-home.<domain>.ru/fullchain.pem /root/3x-ui/cert/fullchain.pem && cp /etc/letsencrypt/live/ui-home.<domain>.ru/privkey.pem /root/3x-ui/cert/privkey.pem"
```

# Установка 3x-ui

Установить панель [[Заметки/Панель для x-ray прокси 3X-UI\|3x-ui]] на сервере в докере:

```shell
git clone https://github.com/MHSanaei/3x-ui.git
cd 3x-ui
```

Изменить файл `docker-compose.yml` изменить `hostname`

```shell
docker compose up -d
```

# Настройка панели

Зайти в панель по адресу `http://192.168.0.138:2053` используя данные авторизации `admin\admin`

Выполнить настройку панели как на скринах ниже
![Настройка двойного VPN через 3X-UI-8.png|600](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-8.png)
![Настройка двойного VPN через 3X-UI-9.png|600](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-9.png)
# Создание wg подключения

Создать новое [[Заметки/VPN Wireguard\|wireguard]] подключение 
![Настройка двойного VPN через 3X-UI-3.png|600](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-3.png)
> [!attention]
> Для корректной маршрутизации обязательно включить Sniffing.

# Подключение внешнего сервера как выход

На удаленном сервере создать новое подключение vless для сервера и скопировать конфигурационную ссылку (нажать на QR код с именем клиента).

На локальном сервере перейти `Настройки Xray -> Исходящие -> Добавить исходящий` в окне перейти в режим Json и указать ссылку для запроса настроек.
![Настройка двойного VPN через 3X-UI-10.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-10.png)

В настройках маршрутизации задать правила перенаправления
![Настройка двойного VPN через 3X-UI-5.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-5.png)

> [!abstract]
> На этом базовая настройка закончена, весь трафик который приходит по wg будет направлен через vless на внешний сервер.

# Настройка разделения трафика
Дополнительно можно настроить разделение трафика по правилам что бы например трафик для России шел на обычный выход интернета а не в VPN тоннель.
> [!danger]
> Из за особенностей работы все равно будет увеличена задержка из за резолвинга внутри самого x-ray. 

Необходимо включить в настройках `Настройки Xray -> Базовый шаблон`
![Настройка двойного VPN через 3X-UI-6.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-6.png)
Итоговые правила маршрутизации должны быть
![Настройка двойного VPN через 3X-UI-7.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%B2%D0%BE%D0%B9%D0%BD%D0%BE%D0%B3%D0%BE%20VPN%20%D1%87%D0%B5%D1%80%D0%B5%D0%B7%203X-UI-7.png)
> [!attention]
> Для работы разделения обязательно в настройках входящего подключения должно быть включен Sniffing
