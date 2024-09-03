---
{"dg-publish":true,"permalink":"/stati/monitoring-proxmox-s-ispolzovaniem-influx-db-v2-i-grafana/"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]
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

[![image.thumb.png.b5170f7f72c14e6e433c674a8631774e.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/ebLKMweht4S9izkSYXEyC4.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.09582208e1b7a8d1e3baf92288d64fa5.png)

При создании есть возможность выставить как долго хранить данные, я выбрал вариант never так как не вижу проблем в разрастании базы на текущий момент.

[![image.png.14b4dbbb3e1bf628060486bf4e5289d6.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/6grzGVEK9hcvECgtibFnfC.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.14b4dbbb3e1bf628060486bf4e5289d6.png "Увеличить изображение")

Теперь необходимо создать токен доступа для корзины, который будет давать права на запись в базу.

Переходим в меню управления API токенами и создаем новый custom token

[![image.png.cf38fa480f4242c16cef6c23995d97e0.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/Sn8nHojUroLnTMVEeNLZkb.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.cf38fa480f4242c16cef6c23995d97e0.png "Увеличить изображение")

Даем права на запись для созданной корзины данных.

[![image.png.a9b031c88647746176341e9a52441412.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/njXpJEBQBwYmM7qgJjs355.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.a9b031c88647746176341e9a52441412.png "Увеличить изображение")

Сохраняем себе полученный токен

Внимание кнопка копирования токена может не сработать, проверяйте перед закрытием.

Переходим в proxmox, нас интересует раздел сервер метрик.

[![image.png.8fe23319defbb3de1e4229483f9c9321.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/ThHvc5WekQ2aYmh7H4QnPk.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.8fe23319defbb3de1e4229483f9c9321.png "Увеличить изображение")

Создаем новую запись influxDB. Указываем в поле база данных имя корзины, а в поле маркер полученный токен записи.

[![image.png.6776e7cbbad079831f06548db337e1b9.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/hZivATHGT3zv2Fpkf5JbbJ.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.6776e7cbbad079831f06548db337e1b9.png "Увеличить изображение")

Проверяем что в ifluxBD появились данные.

[![image.thumb.png.6bab575557b0910e184095355e99499f.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/M3zU9BQvTht3MVH5uwYFXp.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.24869f926f7f7ad200949fa008a1b93d.png)

Теперь нам надо еще собирать данные о температуре сервера proxmox не зависимо от api proxmox. Я для этого буду использовать шаблон созданный в прошлый раз для system\-monitoring, он доступен в разделе telegraf.

[![image.thumb.png.c13f04225ea446f34d069d3be0765e0f.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/HL2jGisSXKpoHob7JRsE3H.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.c35af1985bfded044db1dc57b3891097.png)

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

[![image.png.949e2b4ebb50ae17a19c370956ba3cdf.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/MbLKsio2y2iVq9hz3YhpCq.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.949e2b4ebb50ae17a19c370956ba3cdf.png "Увеличить изображение")

Закрываем это окно сам файл нам не понадобится, нам нужна инструкция по установке.

[![image.png.69ea0c21ade7752598e7238d70b2b189.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/SXU4e9KyhFQZdmC8zNQJGV.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.69ea0c21ade7752598e7238d70b2b189.png "Увеличить изображение")

[![image.png.3e96e28f5b8e85aea46718c70edd2c2a.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/JGFwbTEXGLe273hRtrKfjy.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.3e96e28f5b8e85aea46718c70edd2c2a.png "Увеличить изображение")

Возвращаемся в оболочку proxmox и выполняем две команды как расписано в инструкции заменив <InfluxToken\> на скопированный ранее токен.

Обязательно надо передать ключ сначала отдельной командой для получения доступа к самому шаблону настроек. Но в принципе можно и просто скачать файл и отдать его telegraf как это было с docker вариантом.

Проверяем что данные поступают в корзину и Grafana

[![image.png.5ed6a73fbc82513c9f989d8a11bd4ffc.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/fCaQ9X8JkRHEfujtAfhPtC.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.5ed6a73fbc82513c9f989d8a11bd4ffc.png "Увеличить изображение")

[![image.png.21bf9af490543dbd94b310f904f5f8a4.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/MND6fijTEEo9J3bYowA3ut.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.21bf9af490543dbd94b310f904f5f8a4.png "Увеличить изображение")

Обратите внимание что Grafana дополнительно настраивать для данной корзины не надо, а для корзины proxmox надо.

Для этого переходим в управления токенами API и создаем токен на чтение корзины proxmox для Grafana.

[![image.png.3dd6352ac57ecdae79767ff937adfc26.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/Y9zavyavUcrCEy7BCtcGC8.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.3dd6352ac57ecdae79767ff937adfc26.png "Увеличить изображение")

Подробно настройку Grafa я разбирал в прошлой публикации, тут приведу только результат настроек.

[![image.png.4eeb0182869d6bb9f9947eaed972766b.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/VWsfaAoosdHJqzoyx3p78T.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.4eeb0182869d6bb9f9947eaed972766b.png "Увеличить изображение")

[![image.png.9851a1a8876c9eb73f1232d0cccadb5f.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/DPsA6VEsvxokdoEjdRFJnb.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.9851a1a8876c9eb73f1232d0cccadb5f.png "Увеличить изображение")

Пример панели proxmox в Grafana

[![image.thumb.png.15e54aa45531fbce262160e714229621.png](https://readeck.deniom.ru/bm/Qq/QqHt6FkhspTbCDyRFRqcQE/_resources/4aYQzaMTHpfsHarZ6YqsCo.png)](https://openode.xyz/uploads/monthly_2024_04/image.png.3d6a03a6d552044affdf2095d43bb421.png)

Я использовал шаблон: [https://grafana.com/grafana/dashboards/15356-proxmox-cluster-flux/](https://grafana.com/grafana/dashboards/15356-proxmox-cluster-flux/)

## **Вместо заключения**

Спасибо за внимание, в следующих публикациях я разберу так же способы мониторинга состояния роутеров keenetic и openwrt.