---
{"dg-publish":true,"permalink":"/zametki/self-hosting-hom-ed-zigbee/","created":"2024-10-01 22:18","updated":"2024-10-01T22:23:40+03:00"}
---

Легковесная программа для подключения устройств умного дома работающих на базе zigbee 3.0. Для взаимодействия между элементами системы и умным домом используется протокол mqtt.

Система относится к стеку [[Заметки/Система умного дома HOMEd\|HOMEd]]

Репозиторий: https://github.com/u236/homed-service-zigbee

### Файл конфигурации: 

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">




Актуальная версия конфигурации приведена на [wiki](https://wiki.homed.dev/page/ZigBee/Configuration)

```shell
[log]
enabled=false
file=/var/log/homed.log

[mqtt]
host=localhost
port=1883
username=homed
password=secret
names=true

[homeassistant]
enabled=true
legacy=false
prefix=homeassistant
status=homeassistant/status

[device]
database=/opt/homed-zigbee/database.json
properties=/opt/homed-zigbee/properties.json
options=/opt/homed-zigbee/options.json
external=/opt/homed-zigbee/external
library=/usr/share/homed-zigbee
offsets=true

[zigbee]
adapter=ezsp
port=/dev/ttyUSB0
baudrate=115200
panid=0x1010
channel=11
reset=soft
write=true

[security]
enabled=true
key=0x000102030405060708090a0b0c0d0e0f

[debug]
adapter=false
zigbee=false

```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Роутер Perenio\|Роутер Perenio]]

> [!info]-
> Примечание:: Подключение zigbee устройств к умному дому
