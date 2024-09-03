---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/obnovlenie-proshivki-zigbee-ustrojstva-po-ota-hom-ed/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---
Для обновления:
- Скачать файл прошивки OTA и положить его на координатор по пути `/usr/share/ota` который будет указан в команде обновления.
- Разместить прошиваемое устройство максимально близко к координатору.
- Включить лог в [[Хобби/Конфиги/Homed zigbee\|файле конфигурации]] HOMEd-ZigBee (без debug)
```conf
[log]
enabled=true
file=/var/log/homed.log
```
- Подключиться к координатору по ssh и выполнить команду для вывода логов обновления в реальном времени
```comand
cd /var/log/
tail homed.log -f
```
- Отправить MQTT команду в топик `homed/command/zigbee` следующего содержимого
```json
{
"action": "otaUpgrade",
"device": "<DeviceName>",
"endpointId": 1,
"fileName": "/usr/share/ota/<FileName>.zigbee"
}
```
Команду можно отправить из любого связанного брокера, например через MQTT Explorer из Home Assistant.