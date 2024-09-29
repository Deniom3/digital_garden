---
{"dg-publish":true,"permalink":"/stati/monitoring-routerov-openwrt-v-influxdb-v2-i-grafana/","updated":"2024-09-29T00:09:10+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]  [[Статьи/Оглавление моих статей\|мои статьи]] 
> [!urls]- Заметки по статье
>  

---
## **Вступление**

Это четвертая часть о мониторинге домашней системы с использованием influxdb и grafana первая часть про общую настройку [[Статьи/Мониторинг роутеров openwrt в influxdb v2 и grafana\|Мониторинг роутеров openwrt в influxdb v2 и grafana]] и вторая часть про мониторинг proxmox [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]], третья часть про мониторинг роутеров Keenetic [[Статьи/Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа\|Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа]] . Теперь заключительная часть про мониторинг openwrt.

Продолжая обсуждение мониторинга состояния системы, необходимо уделить внимание важному аспекту – мониторингу состояния роутеров. Они являются одними из ключевых и критически важных элементов в вашей системе.

В данной публикации я рассмотрю вопрос мониторинга состояния роутеров под управление системы openwrt с использованием пакета collectd.

Настройка выполнялась на базе версий openwrt 22.03 и 23.05

Глобально у нас есть несколько вариантов настройки сбора статистики с роутеров:

1.  Сбор с помощью collectd и передача через telegraf установленный на роутере
2.  Сбор с помощью collectd и передача через telegraf установленный на отдельном сервере
3.  Сбор и отправка данных через telegraf

Первый и третий вариант требуют установки на каждый роутер достаточно крупного пакета telegraf (урезанная версия весит 12 Mb), а место на многих роутерах очень ограничено. По этой причине я выбрал вариант 2. В общей суме нам потребуется около 1.5-2Мб памяти на роутере для пакетов статистики и одна установка telegraf для агрегации всех данных с нескольких роутеров.

## **Настройка сбора статистики на роутере**

В первую очередь нам необходимо установить пакеты сбора статистики и collectd а так же модуля для них. Это можно сделать через графический интерфейс, или подключившись по ssh.

Команды установки:

```comand
opkg update
opkg install luci-app-statistics collectd collectd-mod-cpu collectd-mod-conntrack collectd-mod-df collectd-mod-dhcpleases collectd-mod-interface collectd-mod-iwinfo collectd-mod-load collectd-mod-memory collectd-mod-network collectd-mod-uptime collectd-mod-rrdtool 
/etc/init.d/luci_statistics enable
/etc/init.d/collectd enable
```

После установки повторно заходим в интерфейс роутера в новый раздел меню статистика – настройки.

В основных настройках обязательно указываем уникальное имя хоста (под этим именем данные будут собираться в системе influxdb)

![Мониторинг роутеров openwrt в influxdb v2 и grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana.png)

Нам необходимо включить все компоненты сбора статистики. Все настройки плагинов можно оставлять в режиме по умолчанию.

При открытии окна настроек плагина может сбиваться состояние настроек и полей.

![Мониторинг роутеров openwrt в influxdb v2 и grafana-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-1.png)

![Мониторинг роутеров openwrt в influxdb v2 и grafana-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-2.png)
Переходим к настройке плагинов вывода, перед настройкой плагинов вывода обязательно примените настройки.

![Мониторинг роутеров openwrt в influxdb v2 и grafana-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-3.png)

Выключаем плагин RRDTool, он отвечает за хранение статистики на устройстве для вывода в виде графиков. И переходим к настройке плагина network.

![Мониторинг роутеров openwrt в influxdb v2 и grafana-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-4.png)

Нам необходимо заполнить только параметры интерфейса telegraf, который будет принимать данные collectd. В моем случае это 192.168.0.137.

Сам telegraf может находиться на любом устройстве сети не зависимо от роутеров и Influxdb.

## **Настройка сбора данных в telegraf**

Для сбора информации с роутеров будем использовать вариант запуска программы telegraf в роли прокси ретранслятора, на вход будет поступать информация от роутеров, а на выходе формироваться запись для передачи в influxdb.

Но перед запуском докер контейнера telegraf нам необходимо забрать с роутера файл для сопоставления наборов данных collectd.

![Мониторинг роутеров openwrt в influxdb v2 и grafana-5.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-5.png)

По умолчанию файл размещен по пути /usr/share/collectd/types.db

Скачиваем его себе на сервер где будет запускаться telegraf, в моем примере это /docker/telegraf-openwrt/types.db

Переходим к настройке telegraf. Заходим в influxBD и создаем новую корзину openwrt и конфигурацию telegraf. Нам нужен источник данных socket listener

![Мониторинг роутеров openwrt в influxdb v2 и grafana-6.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-6.png)

Заменяем шаблонный конфиг, и даем понятное название

```config
# Generic socket listener capable of handling multiple socket types.
[[inputs.socket_listener]]
  ## URL to listen on
  # service_address = "tcp://:8094"
  # service_address = "tcp://127.0.0.1:http"
  # service_address = "tcp4://:8094"
  # service_address = "tcp6://:8094"
  # service_address = "tcp6://[2001:db8::1]:8094"
  service_address = "udp://0.0.0.0:8094"
  # service_address = "udp4://:8094"
  # service_address = "udp6://:8094"
  # service_address = "unix:///tmp/telegraf.sock"
  # service_address = "unixgram:///tmp/telegraf.sock"

  ## Change the file mode bits on unix sockets.  These permissions may not be
  ## respected by some platforms, to safely restrict write permissions it is best
  ## to place the socket into a directory that has previously been created
  ## with the desired permissions.
  ##   ex: socket_mode = "777"
  # socket_mode = ""

  ## Maximum number of concurrent connections.
  ## Only applies to stream sockets (e.g. TCP).
  ## 0 (default) is unlimited.
  # max_connections = 1024

  ## Read timeout.
  ## Only applies to stream sockets (e.g. TCP).
  ## 0 (default) is unlimited.
  # read_timeout = "30s"

  ## Optional TLS configuration.
  ## Only applies to stream sockets (e.g. TCP).
  # tls_cert = "/etc/telegraf/cert.pem"
  # tls_key  = "/etc/telegraf/key.pem"
  ## Enables client authentication if set.
  # tls_allowed_cacerts = ["/etc/telegraf/clientca.pem"]

  ## Maximum socket buffer size (in bytes when no unit specified).
  ## For stream sockets, once the buffer fills up, the sender will start backing up.
  ## For datagram sockets, once the buffer fills up, metrics will start dropping.
  ## Defaults to the OS default.
  # read_buffer_size = "64KiB"

  ## Period between keep alive probes.
  ## Only applies to TCP sockets.
  ## 0 disables keep alive probes.
  ## Defaults to the OS configuration.
  # keep_alive_period = "5m"

  ## Data format to consume.
  ## Each data format has its own unique set of configuration options, read
  ## more about them here:
  ## https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md
  data_format = "collectd"
  collectd_typesdb = ["/usr/share/collectd/types.db"]

  ## Content encoding for message payloads, can be set to "gzip" to or
  ## "identity" to apply no encoding.
  # content_encoding = "identity"
```

Копируем полученный токен, он нам понадобиться для модификации конфигурации. Host изменять не надо, устанавливаем только токен в место параметра.

![Мониторинг роутеров openwrt в influxdb v2 и grafana-7.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-7.png)

Более подробно этот момент разбирался в первой части [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]].

Скачиваем файл конфигурации и размещаем на сервере рядом с types.db

Переходим к запуску докер контейнер telegraf со следующим конфигурационным файлом

```yaml
version: "3"
services:
  telegraf-openwrt:
    image: telegraf:latest
    container_name: telegraf-openwrt
    environment:
      - TZ=Europe/Moscow
    volumes:
      - /docker/telegraf-openwrt/telegraf.conf:/etc/telegraf/telegraf.conf:ro
      - /docker/telegraf-openwrt/types.db:/usr/share/collectd/types.db
    ports:
      - "8094:8094/udp"
```

Отдельно стоит обратить внимание что обмен данными идет именно по протоколу udp, если у вас что-то не работает и данные не появляются проверяйте в первую очередь фаервол.

Проверяем что данные появились в базе

![Мониторинг роутеров openwrt в influxdb v2 и grafana-8.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-8.png)

## **Подключение** **Grafana**

Работа с Grafana уже является стандартной и подробно разбирать я ее не буду, приведу пример как настроено у меня. Создан API токен на чтение корзины openwrt, и выполнено подключение в режиме Flux.

![Мониторинг роутеров openwrt в influxdb v2 и grafana-9.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-9.png)

![Мониторинг роутеров openwrt в influxdb v2 и grafana-10.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-10.png)

В моем примере я использовал шаблон [https://grafana.com/grafana/dashboards/18565-openwrt-collectd-flux/](https://grafana.com/grafana/dashboards/18565-openwrt-collectd-flux/)

В результате имеем такой дашборд

![Мониторинг роутеров openwrt в influxdb v2 и grafana-11.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20%D1%80%D0%BE%D1%83%D1%82%D0%B5%D1%80%D0%BE%D0%B2%20openwrt%20%D0%B2%20influxdb%20v2%20%D0%B8%20grafana-11.png)

## **Заключение**

Это последняя в ближайшей перспективе публикация на тему мониторинга спасибо всем кто ознакомился с этим материалом. Это далеко не все что стоит осветить на тему мониторинга, как минимум необходимо разобрать тему уведомлений по событиям и пересылку в другие коллекторы данных, но пока я не готов погружаться в эту тему. Но в любом случае буду признателен за советы и комментарии по теме.

Отдельно акцентирую внимание я не являюсь профессиональным системным администратором, я только самоучка который решил погрузить в тему умного дома и домашних серверов. Так что мои решения могут быть не самые оптимальные и эффективные но они работают и покрывают поставленные задачи. Я всегда рад обсудить более эффективные варианты и возможно даже что то скорректировать в своей системе.

Спасибо за внимание, меня можно достаточно часто найти в телеграмм чате сообщества готов по возможности ответить на вопросы.

Всем удачи, и надежной работы вашим системам.