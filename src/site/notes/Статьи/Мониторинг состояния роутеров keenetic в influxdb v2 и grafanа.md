---
{"dg-publish":true,"permalink":"/stati/monitoring-sostoyaniya-routerov-keenetic-v-influxdb-v2-i-grafana/","updated":"2024-10-01T12:18:44+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]  [[Статьи/Оглавление моих статей\|мои статьи]] 
> [!urls]- Заметки по статье
>  [[Хобби/Docker compose/Keenetic-monitoring\|Keenetic-monitoring]]
>  [[Заметки/Self-hosting. Keenetic monitoring\|Self-hosting. Keenetic monitoring]]

---
Это третья часть о мониторинге домашней системы с использованием influxdb и grafana первая часть про общую настройку в [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]] и вторая часть про мониторинг proxmox [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]].
## Вступление

Продолжая вопрос мониторинга состояния системы необходимо обсудить еще не маловажный вопрос мониторинга состояния роутеров в вашей системе как один из самых важных и критических элементов системы.

В данной заметке рассмотрим вопрос мониторинга роутеров keenetic а в следующей мониторинг роутеров openwrt. Решения для других систем рассматриваться не будут так как я являюсь приверженцем именно этих двух систем и других устройств у меня нет.

Все настройки будут выполняться на актуально на текущий момент времени версии KeeneticOS 4.1.3

У кинетика есть достаточно удобное и хорошее API у роутеров из коробки но по умолчанию он закрыт файрволлом, именно используя данное restfull api можно получить всю необходимую нам информацию даже для роутеров без entware.

Для решения задачи мониторинга я буду использовать проект https://github.com/vitaliy-sk/keenetic-grafana-monitoring

Подробная инструкция по созданию новых корзин и api токенов было разобрано в первой и второй части, так что в данной публикации будет крайний минимум побочной информации.

## Настройка мониторинга роутера

В рассматриваемом решение применяется подход обращения к API или авторизации и использования cli запросов. Я не вижу смысла использовать авторизацию с запросами так как api работает постоянно и не нагружает дополнительно систему в отличии от постоянных запросов через cli.

Первым этапом откроем доступ к API на роутере, для подключения из локальной сети не требуется авторизация.

Идем в настройки нашего роутера в раздел переадресация портов

![Pasted image 20240620122750.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122750.png)

И создаем новое правило переадресации портов

![Pasted image 20240620122759.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122759.png)

Обратите внимание на подсеть, в моем случае это 192.168.0.1 у вас она может отличаться.

Фактически на стороне роутера настройка закончена теперь нам доступно api по адресу http://192.168.0.1:81/rci/show/system

Настройка сбора данных в influxDB

Теперь приступим к разворачиванию системы, которая будет обращаться к нашему api и пересылать в influxdb.

В первую очередь создадим новую корзину данных для хранения в моем случае это keenetic. И создадим custom api токен на запись данных.

Особенность сборщика не позволяет разделить данные от нескольких роутеров в одной корзине по hostname по этому для каждого роутера который необходимо мониторить делаем отдельную корзину данных.

![Pasted image 20240620122812.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122812.png)

При копировании API токена проверяем, кнопка может отработать не корректно.

Мониторинг запускается в отдельном докер контейнере на любом сервере в сети, в моем случае я буду запускать его на той же виртуальной машине, где у меня запущен influxdb.

Я буду размещать файл конфигурации по пути /docker/keentic-monitoring/config.ini для этого создам советующие папки и файл.

```comand
mkdir /docker
mkdir /docker/keentic-monitoring
nano /docker/keentic-monitoring/config.ini
```
Заполняем данные в шаблон конфигурационного файла. Указываем адрес базы influxdb, токен, организацию и bucket.

```conf
[influx2]
# If you are using docker-compose it should be http://influxdb:8086
url=http://192.168.0.135:8086
# For influx v1.x please use "-" as a value
org=influxdb
# For influx v1.x please use "username:password" as a token
# See DOCKER_INFLUXDB_INIT_ADMIN_TOKEN in docker-compose.yml
token=<Token>
timeout=6000
# For influx v1.x DB name
bucket=keenetic
[keenetic]
admin_endpoint=http://192.168.0.1:81
skip_auth=true
login=admin
password=
[collector]
interval_sec=30
```
Запускаем докер контейнер со следующими параметрами

Я использую docker compose из интерфейса portainer по этому привожу только файл запуска docker compose. Подробную инструкцию как запустить docker compose из файловой системы легко найти на просторах интернета.

```yaml
version: '3.7'
services:
  keenetic-monitoring:
    image: techh/keenetic-grafana-monitoring:latest
    container_name: keenetic-monitoring
    environment:
      - TZ=Europe/Moscow
    volumes:
      - /docker/keentic-monitoring/config.ini:/home/config/config.ini:ro
      # Optionally you can override metrics
      #- ./config/metrics.json:/home/config/metrics.json:ro
    restart: always
```
Проверяем что данные о системе начали поступать.

![Pasted image 20240620122909.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122909.png)

Для тек у кого что то пошло не так...

Если данные не поступают, то необходимо проверить логи вашего контейнера, если есть предупреждение на подобии такого, это значит что универсальный файл с описанием метрик вам не подходит и необходимо добавить еще одну volumes.

![Pasted image 20240620122919.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122919.png)

Должно получиться так:

```yaml
    volumes:
      - /docker/keenetic- monitoring/config.ini:/home/config/config.ini:ro
      # Optionally you can override metrics
      - /docker/keenetic- monitoring/metrics.json:/home/config/metrics.json:ro
```
А на сервере по пути указано как конфиг метрик необходимо добавить файл из гит репозитория автора в котором удаляем секцию на которую получаем ошибку, в моем случае это media. https://github.com/vitaliy-sk/keenetic-grafana-monitoring/blob/master/config/metrics.json

![Pasted image 20240620122939.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122939.png)

Внимательно следите за запятыми и кавычками, надо удалить блок целиком не сломав json.

## Визуализация данных в Grafana

Фактически сбор данных на этом полностью закончен, теперь нам необходимо подключить Grafana к новой корзине данных и найти\составить красивый интерфейс.

Я у себя использую интерфейс предложенный автором системы сбора данных из кинетика https://grafana.com/grafana/dashboards/12723-keenetic/

Обратите внимание что данная панель составлена под версию influxdb v1 для нее настройки Grafana должны быть выполнены следующим образом

![Pasted image 20240620122958.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122958.png)

В поле HTTP headers value указываем: Token %ТокенНаЧтениеКорзины%

Подробнее про настройку смотри первую часть

![Pasted image 20240620123005.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620123005.png)

В итоге получаем красивую панель мониторинга состояния роутера

![Pasted image 20240620122434.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620122434.png)

P.S. Следующая часть серии публикаций будет посвящена сбору данных с роутеров под управление OpenWRT