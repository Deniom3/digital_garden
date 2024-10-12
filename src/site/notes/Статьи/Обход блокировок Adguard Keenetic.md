---
{"dg-publish":true,"permalink":"/stati/obhod-blokirovok-adguard-keenetic/","updated":"2024-10-11T18:16:39+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]
> [!urls]- Заметки по статье
>- [[Хобби/Домашняя лаборатория/Роутер Keenetic\|Роутер Keenetic]]
>- [[Хобби/Команды и настройки/Отключение встроенного DNS Keenetic\|Отключение встроенного DNS Keenetic]]
>- [[Хобби/Команды и настройки/Управление Adguard Home\|Управление Adguard Home]]
>- [[Хобби/Команды и настройки/Добавление сайта в список для обхода блокировки Keneetic Adguard\|Добавление сайта в список для обхода блокировки Keneetic Adguard]]
>- [[Заметки/Список дополнительных сайтов для обхода блокировок Keenetic\|Список дополнительных сайтов для обхода блокировок Keenetic]]
>- [[Хобби/Команды и настройки/Автоматическое обновление списков обхода блокировок для Keenetic\|Автоматическое обновление списков обхода блокировок для Keenetic]]
>- [[Заметки/Обход блокировок на Keenetic с разделением на несколько маршрутов\|Обход блокировок на Keenetic с разделением на несколько маршрутов]]

---
**Требования:**

-   KeenOS версия 4.х
-   Развёрнутая среда [Entware](https://help.keenetic.com/hc/ru/articles/360021214160-%D0%A3%D1%81%D1%82%D0%B0%D0%BD%D0%BE%D0%B2%D0%BA%D0%B0-%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D1%8B-%D0%BF%D0%B0%D0%BA%D0%B5%D1%82%D0%BE%D0%B2-%D1%80%D0%B5%D0%BF%D0%BE%D0%B7%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D1%8F-Entware-%D0%BD%D0%B0-USB-%D0%BD%D0%B0%D0%BA%D0%BE%D0%BF%D0%B8%D1%82%D0%B5%D0%BB%D1%8C),
-   Рабочее VPN-соединение поверх провайдерского, с включённой опцией **“Использовать для выхода в интернет”**, по которому будет идти обращение к выбранным вами доменам.
-   В настройках роутера, в разделе “Приоритеты подключений”, должна быть создана отдельная политика(запомните её название, оно понадобится позже), в ней должно быть включено рабочее VPN-соединение и обязательно исключено основное подключение.
-   Для работы через ipv6 - наличие рабочего ipv6 на основном подключении и на VPN-соединении

___

**Установка пакетов:**

В терминале выполните команду:

```shell
opkg install adguardhome-go ipset iptables ip-full curl jq
```

В CLI роутера выполните команды:

```shell
opkg dns-override
system configuration save
```

(далее все команды должны будут выполняться в теримнале)

В этот момент Entware-сервисы будут перезапущены, а интерфейс для первоначальной настройки AGH станет доступен по адресу:

```
http://ваш-ip-роутера:3000
```

По этому адресу надо перейти в настройки AGH и выполнить первоначальные настройки с помощью маcтера настроек AGH. В первом окне настроек, смените 80й порт на 3000 и жмите далее. Все остальные настройки используемые по умолчанию подойдут для большинства случаев.

___

**Скрипты:**

Создайте файл `/opt/etc/init.d/S52ipset` со следующим содержимым:

```
nano /opt/etc/init.d/S52ipset
```

```
#!/bin/sh

PATH=/opt/sbin:/opt/bin:/opt/usr/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

if [ "$1" = "start" ]; then
    ipset create bypass hash:ip
    ipset create bypass6 hash:ip family inet6
fi
```

Создайте файл `/opt/etc/ndm/netfilter.d/010-bypass.sh` для ipv4 со следующим содержимым:

```
nano /opt/etc/ndm/netfilter.d/010-bypass.sh
```

```
#!/bin/sh

[ "$type" == "ip6tables" ] && exit
[ "$table" != "mangle" ] && exit
[ -z "$(ipset --quiet list bypass)" ] && exit

if [ -z "$(iptables-save | grep bypass)" ]; then
     mark_id=`curl -kfsS http://localhost:79/rci/show/ip/policy 2>/dev/null | jq -r '.[] | select(.description == "policy_vpn") | .mark'`
     iptables -w -t mangle -A PREROUTING ! -i nwg0 -m conntrack --ctstate NEW -m set --match-set bypass dst -j CONNMARK --set-mark 0x$mark_id
     iptables -w -t mangle -A PREROUTING ! -i nwg0 -m set --match-set bypass dst -j CONNMARK --restore-mark
fi
```

Где:  
**nwg0** - это сетевой интерфейс VPN-соединения для выборочного обхода блокировок.  
**policy\_vpn** - это созданная политика в которой был включено VPN-соединение

Создайте файл `/opt/etc/ndm/netfilter.d/011-bypass6.sh` для ipv6 со следующим содержимым:

```
nano /opt/etc/ndm/netfilter.d/011-bypass6.sh
```

```
#!/bin/sh

[ "$type" != "ip6tables" ] && exit
[ "$table" != "mangle" ] && exit
[ -z "$(ipset --quiet list bypass6)" ] && exit

if [ -z "$(ip6tables-save | grep bypass6)" ]; then
     mark_id=`curl -kfsS http://localhost:79/rci/show/ip/policy 2>/dev/null | jq -r '.[] | select(.description == "policy_vpn") | .mark'`
     ip6tables -w -t mangle -A PREROUTING ! -i nwg0 -m conntrack --ctstate NEW -m set --match-set bypass6 dst -j CONNMARK --set-mark 0x$mark_id
     ip6tables -w -t mangle -A PREROUTING ! -i nwg0 -m set --match-set bypass6 dst -j CONNMARK --restore-mark
fi
```

Где:  
**nwg0** - это сетевой интерфейс VPN-соединения для выборочного обхода блокировок.  
**policy\_vpn** - это созданная политика в которой был включено VPN-соединение

Что бы узнать название своего интерфейса, используйте команду:

```
ip addr
```

Сделайте скрипты исполняемыми:

```
chmod +x /opt/etc/init.d/S52ipset
chmod +x /opt/etc/ndm/netfilter.d/010-bypass.sh
chmod +x /opt/etc/ndm/netfilter.d/011-bypass6.sh
```

___

**Список доменов для обхода блокировок:**

Найдите в конфигурационном файле AGH **/opt/etc/AdGuardHome/AdGuardHome.yaml** строчку

и замените на

```
ipset_file: /opt/etc/AdGuardHome/ipset.conf
```

Создайте файл **/opt/etc/AdGuardHome/ipset.conf** Он будет единственным, требующим редактирования время от времени, в зависимости от изменения вашего персонального списка доменов для разблокировки. Он имеет следующий синтаксис:

```
intel.com,ipinfo.io/bypass,bypass6
instagram.com,cdninstagram.com/bypass,bypass6
epicgames.com,gog.com/bypass,bypass6
```

Если не будет использоваться ipv6, то:

```
intel.com,ipinfo.io/bypass
instagram.com,cdninstagram.com/bypass
epicgames.com,gog.com/bypass
```

Т.е. в левой части через запятую указаны домены, требующие обхода блокировок, справа после слэша — название ipset, в который AGH складывает результаты разрешения DNS-имён. Можно указать всё в одну строчку, можно разделить логически на несколько строк как в примере. Домены третьего уровня и выше также включаются в обход блокировок, т.е. указание intel.com включает www.intel.com, download.intel.com и пр. Рекомендую добавить какой-нибудь «сигнальный» сервис, показывающий ваш текущий IP-адрес (ipinfo.io в примере). Так вы сможете проверить работоспособность настроенного решения. Учтите, что AGH не перечитывает изменённый файл, поэтому после правки требуется перезапускать его с помощью команды:

```
/opt/etc/init.d/S99adguardhome restart
```

По завершению всех настроек, требуется перезагрузить роутер. После перезагрузки роутера проверьте в веб-интерфейсе Системный журнал, в нём не должно быть красных строк, связанных с настроенными скриптами.

___

**Диагностика проблем:**

Убедитесь в том, что набор ipset создан и наполняется в процессе работы:

```
# Проверка  ipv4
ipset --list bypass
# Проверка  ipv6
ipset --list bypass6
```

Вывод должен быть не пустой.

Посмотрите, существуют ли правила netfilter для пометки пакетов:

```
# Проверка  ipv4
iptables-save | grep bypass
# Проверка  ipv6
ip6tables-save | grep bypass6
```

 Продолжение настроек: [[Хобби/Команды и настройки/Автоматическое обновление списков обхода блокировок для Keenetic\|Автоматическое обновление списков обхода блокировок для Keenetic]]