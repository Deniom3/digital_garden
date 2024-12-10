---
{"dg-publish":true,"permalink":"/zametki/obhod-blokirovok-na-keenetic-s-razdeleniem-na-neskolko-marshrutov/","created":"2024-09-01 20:38","updated":"2024-11-07T13:20:49+03:00"}
---

Является доработанным вариантом [[Статьи/Обход блокировок Adguard Keenetic\|; Обход блокировок Adguard Keenetic]]
Трафик разделяется на два разных туннеля по разным спискам.

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
    ipset create local hash:ip
    ipset create local6 hash:ip family inet6
fi
```

Подготовим скрипты маршрутов. Необходимо создать политику доступа в приоритетах подключения которая будет содержать только один нужный интерфейс vpn.

Что бы узнать название своего интерфейса, используйте команду:

```
ip addr
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

Создайте файл `/opt/etc/ndm/netfilter.d/011-bypass6.sh` для ipv6 со следующим содержимым:

### Теперь подготовим второй набор скриптов

Создайте файл `/opt/etc/ndm/netfilter.d/012-local.sh` для ipv4 со следующим содержимым:

```
nano /opt/etc/ndm/netfilter.d/012-local.sh
```

```
#!/bin/sh

[ "$type" == "ip6tables" ] && exit
[ "$table" != "mangle" ] && exit
[ -z "$(ipset --quiet list local)" ] && exit

if [ -z "$(iptables-save | grep local)" ]; then
     mark_id=`curl -kfsS http://localhost:79/rci/show/ip/policy 2>/dev/null | jq -r '.[] | select(.description == "vpn_local") | .mark'`
     iptables -w -t mangle -A PREROUTING ! -i nwg3 -m conntrack --ctstate NEW -m set --match-set local dst -j CONNMARK --set-mark 0x$mark_id
     iptables -w -t mangle -A PREROUTING ! -i nwg3 -m set --match-set local dst -j CONNMARK --restore-mark
fi
```

Где:  
**nwg3** - это сетевой интерфейс VPN-соединения для выборочного обхода блокировок.  
**vpn_local** - это созданная политика в которой был включено VPN-соединение

Создайте файл `/opt/etc/ndm/netfilter.d/013-local6.sh` для ipv6 со следующим содержимым:

```
nano /opt/etc/ndm/netfilter.d/013-local6.sh
```

```
#!/bin/sh

[ "$type" != "ip6tables" ] && exit
[ "$table" != "mangle" ] && exit
[ -z "$(ipset --quiet list local6)" ] && exit

if [ -z "$(ip6tables-save | grep local6)" ]; then
     mark_id=`curl -kfsS http://localhost:79/rci/show/ip/policy 2>/dev/null | jq -r '.[] | select(.description == "vpn_local") | .mark'`
     ip6tables -w -t mangle -A PREROUTING ! -i nwg3 -m conntrack --ctstate NEW -m set --match-set local6 dst -j CONNMARK --set-mark 0x$mark_id
     ip6tables -w -t mangle -A PREROUTING ! -i nwg3 -m set --match-set local6 dst -j CONNMARK --restore-mark
fi
```

Где:  
**nwg3** - это сетевой интерфейс VPN-соединения для выборочного обхода блокировок.  
**vpn_local** - это созданная политика в которой был включено VPN-соединение

Сделайте скрипты исполняемыми:

```
chmod +x /opt/etc/init.d/S52ipset
chmod +x /opt/etc/ndm/netfilter.d/010-bypass.sh
chmod +x /opt/etc/ndm/netfilter.d/011-bypass6.sh
chmod +x /opt/etc/ndm/netfilter.d/012-local.sh
chmod +x /opt/etc/ndm/netfilter.d/013-local6.sh
```


___
### Настройка файла со списком доменов:

Найдите в конфигурационном файле AGH **/opt/etc/AdGuardHome/AdGuardHome.yaml** строчку

и замените на

```
ipset_file: /opt/etc/AdGuardHome/ipset.conf
```

```
/opt/etc/init.d/S99adguardhome restart
```

По завершению всех настроек, требуется перезагрузить роутер. После перезагрузки роутера проверьте в веб-интерфейсе Системный журнал, в нём не должно быть красных строк, связанных с настроенными скриптами.

---

Для автоматического обновления списка обхода блокировок можно использовать следующий скрипт:
```sh
#!/bin/sh

# URL проекта на GitHub, содержащего список сайтов
GITHUB_URL="https://raw.githubusercontent.com/itdoginfo/allow-domains/main/Russia/inside-raw.lst"

# Локальный файл для сохранения результатов
OUTPUT_FILE="/opt/etc/AdGuardHome/ipset.conf"

# Локальный файл с первым списком сайтов
LOCAL_FILE="/opt/etc/AdGuardHome/my-domains-list.conf"

# Локальный файл со вторым списком сайтов
LOCAL_FILE_UNIQUE="/opt/etc/AdGuardHome/my-domains-local-list.conf"

# Создаем временный файл для загрузки списка сайтов
TEMP_FILE=$(mktemp)

# Загружаем список сайтов из GitHub
curl -s $GITHUB_URL -o $TEMP_FILE

# Проверяем успешность загрузки
if [ $? -ne 0 ]; then
    echo "Ошибка при загрузке списка сайтов"
    rm -f $TEMP_FILE
    exit 1
fi

# Очищаем файл перед записью новых данных
> $OUTPUT_FILE

# Обрабатываем первый локальный файл и добавляем /bypass,bypass6
if [ -f "$LOCAL_FILE" ]; then
    while IFS= read -r site || [ -n "$site" ]; do
        # Удаляем символы CR (возврат каретки)
        site=$(echo "$site" | tr -d '\r')
        # Пропускаем пустые строки
        [ -z "$site" ] && continue
        echo "$site/bypass,bypass6" >> $OUTPUT_FILE
    done < $LOCAL_FILE
else
    echo "Локальный файл $LOCAL_FILE не найден"
fi

# Обрабатываем второй локальный файл и добавляем /local,local6
if [ -f "$LOCAL_FILE_UNIQUE" ]; then
    while IFS= read -r site || [ -n "$site" ]; do
        # Удаляем символы CR (возврат каретки)
        site=$(echo "$site" | tr -d '\r')
        # Пропускаем пустые строки
        [ -z "$site" ] && continue
        echo "$site/local,local6" >> $OUTPUT_FILE
    done < $LOCAL_FILE_UNIQUE
else
    echo "Локальный файл $LOCAL_FILE_UNIQUE не найден"
fi

# Создаем временный файл для хранения уникальных сайтов из второго локального списка
TEMP_LOCAL_DOMAINS=$(mktemp)

# Заполняем временный файл уникальными доменами из второго локального списка
if [ -f "$LOCAL_FILE_UNIQUE" ]; then
    while IFS= read -r site || [ -n "$site" ]; do
        clean_site=$(echo "$site" | tr -d '\r' | cut -d'/' -f1)
        [ -z "$clean_site" ] && continue
        echo "$clean_site" >> $TEMP_LOCAL_DOMAINS
    done < $LOCAL_FILE_UNIQUE
fi

# Затем обрабатываем загруженный файл и добавляем /bypass,bypass6, если сайт не содержится во втором локальном списке
while IFS= read -r site || [ -n "$site" ]; do
    # Удаляем символы CR (возврат каретки) и постфиксы
    clean_site=$(echo "$site" | tr -d '\r' | cut -d'/' -f1)
    # Пропускаем пустые строки
    [ -z "$clean_site" ] && continue

    # Проверяем, содержится ли сайт во втором локальном списке
    if ! grep -Fxq "$clean_site" $TEMP_LOCAL_DOMAINS; then
        echo "$site/bypass,bypass6" >> $OUTPUT_FILE
    fi
done < $TEMP_FILE

# Удаляем временные файлы
rm -f $TEMP_FILE
rm -f $TEMP_LOCAL_DOMAINS

echo "Файл $OUTPUT_FILE успешно обновлен"
```

Скрипт поддерживает использование [[Заметки/Список дополнительных сайтов для обхода блокировок Keenetic\|дополнительного списка своих сайтов]] которые должны быть размещены в файле `/opt/etc/AdGuardHome/my-domains-list.conf`

Создайте файл `/opt/etc/AdGuardHome/my-domains-list.conf` и `/opt/etc/AdGuardHome/my-domains-local-list.conf`

В первом списке будут содержаться сайты для маршрута bypass во втором local.

Для работы необходимо разместить скрипт по пути `/opt/etc/AdGuardHome/update_sites.sh`

И сделать его исполняемым:
```shell
chmod +x update_sites.sh
```

Для ручного запуска:
```shell
./update_sites.sh
```

---
### Настройка автоматического обновления

Для автоматического обновления каждый день в 6 утра добавим событие в cron, но сначала установим его:

```shell
opkg update
opkg install cron

/opt/etc/init.d/S10cron start

nano /opt/etc/crontab
```

Добавить строку:
```
0 6 * * * root /opt/etc/AdGuardHome/update_sites.sh
```

___
### Диагностика проблем:

Убедитесь в том, что набор ipset создан и наполняется в процессе работы:

```
# Проверка  ipv4
ipset --list bypass
# Проверка  ipv6
ipset --list bypass6
# Проверка  ipv4
ipset --list local
# Проверка  ipv6
ipset --list local6
```

Вывод должен быть не пустой.

Посмотрите, существуют ли правила netfilter для пометки пакетов:

```
# Проверка  ipv4
iptables-save | grep bypass
# Проверка  ipv6
ip6tables-save | grep bypass6
# Проверка  ipv4
iptables-save | grep local
# Проверка  ipv6
ip6tables-save | grep local6
```



---
> [!urls]- Упоминания:
> - 