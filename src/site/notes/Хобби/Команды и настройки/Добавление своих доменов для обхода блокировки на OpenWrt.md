---
{"dg-publish":true,"dg-path":"Команды и настройки/Добавление своих доменов для обхода блокировки на OpenWrt.md","permalink":"/komandy-i-nastrojki/dobavlenie-svoih-domenov-dlya-obhoda-blokirovki-na-open-wrt/","updated":"2024-10-06T02:54:35+03:00"}
---

Используется при реализации метода [[Хобби/Команды и настройки/Настройка обхода блокировок на роутере с OpenWrt\|обхода блокировок по списку на OpenWrt]] 
## Изменения файлов конфигурации по SSH

Изменение файлов конфигурации корректируя файл `/etc/config/dhcp`

```shell
config ipset
        list name 'vpn_domains'
        list domain 'graylog.org'
        list domain 'terraform.io'
```

Каждый необходимый домен добавляем новой строкой. Dnsmasq работает по wildcard, поэтому субдомены добавлять не нужно.

Для применения этой настройки нужно перезапустить dnsmasq

```
service dnsmasq restart
```

## Изменение настроек через веб интерфейс

Для этого надо зайти в настройки `Сеть -> DHSP и DNS -> Наборы IP-адресов`

![Добавление своих доменов для обхода блокировки на OpenWrt.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%94%D0%BE%D0%B1%D0%B0%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5%20%D1%81%D0%B2%D0%BE%D0%B8%D1%85%20%D0%B4%D0%BE%D0%BC%D0%B5%D0%BD%D0%BE%D0%B2%20%D0%B4%D0%BB%D1%8F%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%D0%B0%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BA%D0%B8%20%D0%BD%D0%B0%20OpenWrt.png)