---
{"dg-publish":true,"permalink":"/stati/monitoring-proxmox-s-ispolzovaniem-influx-db-v2-i-grafana/","updated":"2024-09-23T23:50:16+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]  [[Статьи/Оглавление моих статей\|мои статьи]] 
> [!urls]- Заметки по статье
>  

---
Первичную настройку я разбирал уже в первой части так что в этой части будет меньше моментов по настройки самой Grafana и InfluxDB. Первая часть: [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]]
## **Вступление, или варианты решения**

Для мониторинга состояния proxmox у нас есть несколько способов решения задачи.

1.  Использовать сам proxmox для отправки данных в influxdb
2.  Использовать telegraf для сбора данных через API proxmox и отправки их в influxdb
3.  Использовать telegraf для сбора данных хоста proxmox

Фактически первые два варианта равнозначны и используют API proxmox которого достаточно для большинства задач, но в нем нет одного важного для меня показателя температура процессора. Подробнее про API proxmox можете почитать тут: [https://pve.proxmox.com/wiki/Proxmox\_VE\_API](https://pve.proxmox.com/wiki/Proxmox_VE_API)

Обращаться по API из telegraf в моем случае я тоже не вижу смысла так как вся система у меня находится в одной локации, это было бы оправдано если нам необходимо контролировать разрыв связи или собирать данные сразу с нескольких источник.

Поэтому я буду использовать комбинацию первого и третьего варианта.

## **Настройка отправки данных из** **proxmox**

Для мониторинга состояния proxmox VE нам необходимо создать новую корзину для данных в influxDB для этого заходим в influxDB в раздел **Load Data -> Bucket**

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana.png)

При создании есть возможность выставить как долго хранить данные, я выбрал вариант never так как не вижу проблем в разрастании базы на текущий момент.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-1.png)

Теперь необходимо создать токен доступа для корзины, который будет давать права на запись в базу.

Переходим в меню управления API токенами и создаем новый custom token

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-2.png)

Даем права на запись для созданной корзины данных.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-3.png)

Сохраняем себе полученный токен

Внимание кнопка копирования токена может не сработать, проверяйте перед закрытием.

Переходим в proxmox, нас интересует раздел сервер метрик.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-4.png)

Создаем новую запись influxDB. Указываем в поле база данных имя корзины, а в поле маркер полученный токен записи.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-5.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-5.png)

Проверяем что в ifluxBD появились данные.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-6.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-6.png)

Теперь нам надо еще собирать данные о температуре сервера proxmox не зависимо от api proxmox. Я для этого буду использовать шаблон созданный в прошлый раз для system\-monitoring, он доступен в разделе telegraf.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-7.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-7.png)

Так как это хост система в данном случае я буду использовать пакетный вариант telegraf

Подключаемся по ssh или через оболочку proxmox в браузере и устанавливаем пакет.

Подробно все расписано в документации по установке тут: [https://docs.influxdata.com/telegraf/v1/install/](https://docs.influxdata.com/telegraf/v1/install/)

Обратите внимание что, работая в оболочке proxmox у нас не будет sudo если работать под root

Приведу команды установки из-под root пользователя:

```comand
curl -s https://repos.influxdata.com/influxdata-archive_compat.key &gt; influxdata-archive_compat.key

echo '393e8779c89ac8d958f81f942f9ad7fb82a25e133faddaf92e15b16e6ac9ce4c influxdata-archive_compat.key' | sha256sum -c &amp;&amp; cat influxdata-archive_compat.key | gpg --dearmor | tee /etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg &gt; /dev/null

echo 'deb [signed-by=/etc/apt/trusted.gpg.d/influxdata-archive_compat.gpg] https://repos.influxdata.com/debian stable main' | tee /etc/apt/sources.list.d/influxdata.list

apt-get update &amp;&amp; apt-get install telegraf
```

Актуальные команды всегда будут доступны в официальной документации, просто удалите sudo из команд.

Дальше нам необходимо получить нашу конфигурацию system\-monitoring, для этого переходим в шаблоны telegraf открываем наш шаблон и копируем от туда токен доступа

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-8.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-8.png)

Закрываем это окно сам файл нам не понадобится, нам нужна инструкция по установке.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-9.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-9.png)

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-10.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-10.png)

Возвращаемся в оболочку proxmox и выполняем две команды как расписано в инструкции заменив <InfluxToken\> на скопированный ранее токен.

Обязательно надо передать ключ сначала отдельной командой для получения доступа к самому шаблону настроек. Но в принципе можно и просто скачать файл и отдать его telegraf как это было с docker вариантом.

Проверяем что данные поступают в корзину и Grafana

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-11.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-11.png)

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-12.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-12.png)

Обратите внимание что Grafana дополнительно настраивать для данной корзины не надо, а для корзины proxmox надо.

Для этого переходим в управления токенами API и создаем токен на чтение корзины proxmox для Grafana.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-13.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-13.png)

Подробно настройку Grafa я разбирал в прошлой публикации, тут приведу только результат настроек.

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-14.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-14.png)

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-15.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-15.png)

Пример панели proxmox в Grafana

![Мониторинг proxmox с использованием InfluxDB v2 и Grafana-16.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20proxmox%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20InfluxDB%20v2%20%D0%B8%20Grafana-16.png)

Я использовал шаблон: [https://grafana.com/grafana/dashboards/15356-proxmox-cluster-flux/](https://grafana.com/grafana/dashboards/15356-proxmox-cluster-flux/)

## **Вместо заключения**

Спасибо за внимание, в следующих публикациях я разберу так же способы мониторинга состояния роутеров keenetic и openwrt.