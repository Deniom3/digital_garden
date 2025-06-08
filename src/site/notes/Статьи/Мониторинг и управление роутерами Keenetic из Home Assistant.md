---
{"dg-publish":true,"permalink":"/stati/monitoring-i-upravlenie-routerami-keenetic-iz-home-assistant/","updated":"2025-05-24T01:59:16+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]
> [!urls]- Заметки по статье
> - [[Хобби/Команды и настройки/Настройка Rest Api на роутере Keenetic\|Настройка Rest Api на роутере Keenetic]]
> - [[Хобби/Команды и настройки/Изменение политики доступа клиента через rest api keenetic в Home Assistant\|Изменение политики доступа клиента через rest api keenetic в Home Assistant]]
> - [[Хобби/Команды и настройки/Мониторинг системных показателей Keenetic в Home Assistant\|Мониторинг системных показателей Keenetic в Home Assistant]]

---
У данных роутеров есть [CLI, консоль управления](https://help.keenetic.com/hc/ru/articles/213965889-%D0%98%D0%BD%D1%82%D0%B5%D1%80%D1%84%D0%B5%D0%B9%D1%81-%D0%BA%D0%BE%D0%BC%D0%B0%D0%BD%D0%B4%D0%BD%D0%BE%D0%B9-%D1%81%D1%82%D1%80%D0%BE%D0%BA%D0%B8-CLI-%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82-%D1%86%D0%B5%D0%BD%D1%82%D1%80%D0%B0 "Ссылка"), вот все что можно сделать в ней можно сделать через ХА. [Тут мануал по командам](http://docs.help.keenetic.com/cli/3.1/ru/cli_manual_kn-1711_ru.pdf "Ссылка").  
1\. Нужно включить доступ к ней в ЛОКАЛЬНОЙ СЕТИ без авторизации. Для этого в самом роутере нужно создать правило переадресации портов.

![Мониторинг и управление роутерами Keenetic из Home Assistant.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D0%B8%20%D1%83%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%B0%D0%BC%D0%B8%20Keenetic%20%D0%B8%D0%B7%20Home%20Assistant.png)

цифрой 1 отмечено, это диапазон ВАШЕЙ ЛОКАЛЬНОЙ сети, у кого то 192.168.0.0 или 192.168.1.0. Порты оставить как на скрине.  
После этого из локальной сети для проверки можно зайти [http://IP-адрес:81/rci/show/system](http://xn--ip--8cdug0fj:81/rci/show/system "Ссылка") , должны увидеть инфо, если так все норм.

![Мониторинг и управление роутерами Keenetic из Home Assistant-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D0%B8%20%D1%83%D0%BF%D1%80%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%B0%D0%BC%D0%B8%20Keenetic%20%D0%B8%D0%B7%20Home%20Assistant-1.png)

2\. Теперь можно создавать сенсоры, ip заменить на свой. Эти сенсоры стандартные для всех кинетиков. Загрузка проца\\памяти и аптайм.

```yaml
sensor:
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/system
		name: Keenetic CPU load
		value_template: "{{ value_json.cpuload }}"
		unit_of_measurement: '%'
		unique_id: Keenetic CPU load

	- platform: rest
		resource: http://10.0.0.1:81/rci/show/system
		name: Keenetic RAM load
		value_template: >
		{%- set mem = value_json.memory-%}
		{%- set memfree = mem.split('/')[0]|int(0)-%}
		{%- set memtotal = mem.split('/')[1]|int(1)-%}
		{{ (memfree*100/memtotal)| round(2) }}
		unit_of_measurement: '%'
		unique_id: Keenetic RAM load

	- platform: rest
		resource: http://10.0.0.1:81/rci/show/system
		name: Keenetic Uptime
		unique_id: Keenetic Uptime
		value_template: >
		{%- set test = value_json.uptime | int(0) / 60-%}
		{%- set minutes = (test % 60) | int %}
		{%- set hours = ((test % 1440) /60) | int %}
		{%- set days = (test / 1440)|int -%}
		{%if days > 0 %}{{days}}д {% endif %}{%if hours > 0 %}{{hours}}ч {% endif %}{%if minutes > 0 or test >60 %}{{minutes}}м{%else%}<1m{%endif%}
```

3\. Можно мониторить интернет соединение или подключенный ВПН, в моем случае это интерфейсы PPPoE0 и Wireguard0  
посмотреть свои можно если зайти на страницу [http://IP-адрес:81/rci/show/interface](http://xn--ip--8cdug0fj:81/rci/show/interface "Ссылка") , Далее создаем сенсоры этих интерфейсов где будет видно поднят он или нет, и в атрибутах IP адрес, есть ли линк и аптайм именно этого подключения.

```yaml
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/interface/PPPoE0
		name: Keenetic PPPoE0
		value_template: "{{ value_json.state }}"
		unique_id: Keenetic PPPoE0
		json_attributes:
		- address
		- link
		- uptime
		- platform: rest
		resource: http://10.0.0.1:81/rci/show/interface/Wireguard0
		name: Keenetic Wireguard0
		value_template: "{{ value_json.state }}"
		unique_id: Keenetic Wireguard0
		json_attributes:
		- address
		- link
		- uptime
```
Если требуются интерфейсы с двойным названием через слеш (например WifiMaster0/WifiStation0)  
то строка запроса на них будет вида

```
resource: http://10.0.0.1:81/rci/show/interface/?name=WifiMaster0/WifiStation0
```

4\. Так же есть возможность перезагрузить роутер. Это уже не сенсор, а rest\_command , прописываем отдельно в configuration.yaml И второй rest принудительного обновления KeenDNS

```
rest_command:
	keenetic_reboot:
		url: http://10.0.0.1:81/rci/system/reboot
		method: POST
		payload: "{}"
	keenetic_keendns_update:
		url: http://10.0.0.1:81/rci/ndns/get-update
		method: POST
		payload: "{}"
```

5\. вообще возможностей куча, для роутеров с USB и подключенными хардами, можно достать так же инфо о диске, сколько свободного места. Можно кол-во подключенных устройств достать и вообще все все) так же можно управлять таблицей маршрутизации, я например прямо из телеги, для обхода блокировок, отправляю ХА домен который мне нужно обойти через ВПН, и ха его добавляет.

6\. Отключение итнернета конкретному устройству сети. Заменить mac в 3 местах, на нужный!

```yaml
switch:
	- platform: rest
		name: keenetic_comp_inet
		unique_id: keenetic_comp_inet
		resource: "http://10.0.0.1:81/rci/ip/hotspot/host"
		body_on: '{"mac":"54:48:e6:0b:67:8a", "access":"permit"}'
		body_off: '{"mac":"54:48:e6:0b:67:8a", "access":"deny" }'
		is_on_template: >
		{% for k in value_json %}
		{% if k.mac == '54:48:e6:0b:67:8a' %}
		{{ k.access == 'permit' }}
		{% endif %}{% endfor %}
```
7\. включение и отключение интерфейсов роутера, на примере гостевой WiFi сети

```yaml
switch:
	- platform: rest
		name: keenetic_GuestWiFi
		unique_id: keenetic_GuestWiFi
		resource: "http://10.0.0.1:81/rci/interface/GuestWiFi"
		body_on: '{"up":"true"}'
		body_off: '{"down":"true"}'
		is_on_template: >
		{{ value_json.up is defined }}
```

8\. Скорость приема\\передачи в мегабитах в секунду, на интерфейсе PPPoE0

```yaml
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/interface/stat?name=PPPoE0
		name: Keenetic txspeed
		value_template: "{{ (value_json.txspeed / 1250000) | round(2) }}"
		unique_id: Keenetic txspeed
		scan_interval: 20
		unit_of_measurement: 'Mbit/s'
		json_attributes:
		- txbytes
		- txerrors
		- txspeed
		- platform: rest
		resource: http://10.0.0.1:81/rci/show/interface/stat?name=PPPoE0
		name: Keenetic rxspeed
		value_template: "{{ (value_json.rxspeed / 1250000) | round(2) }}"
		unique_id: Keenetic rxspeed
		scan_interval: 20
		unit_of_measurement: 'Mbit/s'
		json_attributes:
		- rxbytes
		- rxerrors
		- rxspeed
```

9\. Температура чипов Wi-Fi 2,4 и 5 ГГц

```yaml
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/interface/WifiMaster0
		name: Keenetic temperature cip wifi 2.4
		value_template: "{{ value_json.temperature }}"
		unit_of_measurement: '°C'
		unique_id: Keenetic temperature cip wifi 2.4
	
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/interface/WifiMaster1
		name: Keenetic temperature cip wifi 5
		value_template: "{{ value_json.temperature }}"
		unit_of_measurement: '°C'
		unique_id: Keenetic temperature cip wifi 5
```

10\. Включение и выключение через ХА, удаленного доступа к веб морде роутера.

```yaml
- platform: rest
	name: "Web Ext Access"
	unique_id: Web Ext Access
	resource: http://10.0.0.1:81/rci/ip/http/security-level
	method: post
	body_on: '{ "public": true, "ssl": true }'
	body_off: '{ "private": true }'
	is_on_template: "{{ 'public' in value_json }}"
```

**Для меш роутеров**  
Можно мониторить ресурсы меш роутеров , доступны они на странице(главного роутера)[http://IP:81/rci/show/mws/member](http://ip:81/rci/show/mws/member "Ссылка") в виде массива  
если роутер один, то первый элемент массива это 0, ниже в коде \[0\], для остальных 1\\2\\3 и так далее..

```yaml
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/mws/member
		name: Keenetic_mesh CPU load
		value_template: "{{ value_json[0].system.cpuload }}"
		unit_of_measurement: '%'
		unique_id: Keenetic_mesh CPU load
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/mws/member
		name: Keenetic_mesh RAM load
		value_template: >
		{%- set mem = value_json[0].system.memory-%}
		{%- set memfree = mem.split('/')[0]|int(0)-%}
		{%- set memtotal = mem.split('/')[1]|int(1)-%}
		{{ (memfree*100/memtotal)| round(2) }}
	- platform: rest
		resource: http://10.0.0.1:81/rci/show/mws/member
		name: Keenetic_mesh Uptime
		unique_id: Keenetic_mesh Uptime
		value_template: >
		{%- set test = value_json[0].system.uptime | int(0) / 60-%}
		{%- set minutes = (test % 60) | int %}
		{%- set hours = ((test % 1440) /60) | int %}
		{%- set days = (test / 1440)|int -%}
		{%if days > 0 %}{{days}}д {% endif %}{%if hours > 0 %}{{hours}}ч {% endif %}{%if minutes > 0 or test >60 %}{{minutes}}м{%else%}<1m{%endif%}
```

Перезагрузить пока возможно только через сторонний скрипт авторизации.  
Нужно создать файл keenetic.py, положить в корень, рядом с configuration.yaml. **В нем подправить логин и пароль на свои**.

```python
# -*- coding: utf-8 -*-
import sys
ip_addr=str(sys.argv[1])
method_str=str(sys.argv[2])
command_str=str(sys.argv[3])
data_str=str(sys.argv[4])

login = "admin"
passw = "password"

CONFIG_FILE_NAME = "keenetic.conf"

import configparser
import requests
import hashlib

cookies_current = None
session = requests.session()

def keen_auth(login, passw):
response = keen_request(ip_addr, "auth", None, "GET")

if response.status_code == 401:
md5 = login + ":" + response.headers["X-NDM-Realm"] + ":" + passw
md5 = hashlib.md5(md5.encode('utf-8'))
sha = response.headers["X-NDM-Challenge"] + md5.hexdigest()
sha = hashlib.sha256(sha.encode('utf-8'))
response = keen_request(ip_addr, "auth", {"login": login, "password": sha.hexdigest()}, "POST")
if response.status_code == 200:
return True
elif response.status_code == 200:
return True
else:
return False

def keen_request(ip_addr, query, data = None, method="GET"):

global session

url = "http://" + ip_addr + "/" + query

if method == "POST":
return session.post(url, json=data)
else:
return session.get(url)

config = configparser.ConfigParser()
config.read(CONFIG_FILE_NAME)
ip_addr = ip_addr
login = login
passw = passw

if keen_auth(login, passw):

response = keen_request(ip_addr, command_str, data_str, method_str);

print(response.text)
```
командой вида, где IP-адрес это ip меш роутера

```yaml
- switch:
	name: Reboot Keenetic_mash
	command_on: "python3 /config/keenetic.py 'IP-адрес' 'POST' 'rci/system/reboot' '{}'"
```

При помощи данного скрипта можно, через command\_line, выдергивать с меш роутера любые данные, копии которых нет на главном.  
Для примера загрузка процессора:

```yaml
command_line:
	- sensor:
		name: kenetic_mash_cpu
		unique_id: kenetic_mash_cpu
		command: "python3 /config/keenetic.py '10.0.0.2' 'GET' 'rci/show/system' ''"
		value_template: '{{value_json.cpuload}}'
		scan_interval: 120
		unit_of_measurement: '%'
```

