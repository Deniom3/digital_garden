---
{"dg-publish":true,"permalink":"/stati/nastrojka-razdelenie-trafika-s-ispolzovanie-wireguard/","updated":"2024-09-23T23:49:02+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]] [[Статьи/Оглавление моих статей\|мои статьи]]
> [!urls]- Заметки по статье

---
## **Вступление или зачем нам вообще это надо?**

В наше интересное время все чаше приходиться использовать VPN, но тут начинается другая проблема часть местных интернет ресурсов может быть недоступна через сервер VPN. И начинается постоянное передергивание включения выключения VPN соединения.

Существует много вариантов концепции разделения трафика и обхода региональных блокировок я решил рассмотреть вариант реализации с использованием wireguard. Да это скорее всего (даже наверняка) не самый лучший инструмент для организации разделения трафика но во первых уже был работающий сервер wg во вторых заинтересовала сама идея реализации.

Практически все материалы по разделению трафика были заимствованы и доработаны из статьи с хабр [Укрощаем одноглазого змея. Разбираемся с WireGuard и делаем свой умный VPN](https://habr.com/ru/companies/xakep/articles/699000/) я не претендую на авторство метода, скриптов или любых материалов из оригинальной статьи. А так же настоятельно рекомендую ознакомиться с оригиналом статьи для лучшего понимания идеи.

Цель данной публикации познакомить с вариантом разделения трафика, предоставить сжатую инструкцию по настройке. Так же в отличии от оригинальной статьи настройка выполнялась с установкой веб интерфейса в виде wg-easy так как ручная генерация новых клиентов описанная автором мне кажется мягко говоря неудобной и не оправданной.

Для реализации надо:

1.       Удаленный сервер с настроенным wg сервером

2.       Локальная машина Linux (желательно виртуальная отдельно от других приложений) для работы достаточно 256 мб ОЗУ но зависит от количества клиентов и объема трафика.

**Подготовка сервера**

Выполним первоначальную настройку сервера для установки. Основная настройка будет выполнятся с использованием скрипта, но для создания клиентских подключений используется wg-easy установленным с использованием docker-compose.

Обновляем репозитории и пакеты:

```comand
apt update -y &&
apt upgrade -y
```

Устанавливаем Docker:

```comand
apt install curl && curl -fsSL https://get.docker.com -o get-docker.sh  && sh get-docker.sh
```

Запускаем и включаем службу Docker

```comand
systemctl start docker  &amp;&amp; systemctl enable docker
```

Устанавливаем Docker Compose:

```comand
curl -L --fail https://raw.githubusercontent.com/linuxserver/docker-docker-compose/master/run.sh -o /usr/local/bin/docker-compose  && chmod +x /usr/local/bin/docker-compose
```

Так же устанавливает пакеты необходимые для работы wg и скрипта настройки маршрутов.

apt install -y wireguard iptables ipcalc qrencode curl jq traceroute net-tools netscript

**Настройка соединения с внешним сервером**

Наш внутренний сервер подключается как клиент к внешнему серверу с настроенным wireguard. Для этого на внешнем сервере разворачиваем серверную часть wg (рекомендую использовать скрипты dwg) или заходим на ранее созданный и генерируем нового клиента.

Нам необходимо получить файл конфигурации для подключения клиента вида:

```conf
<span>[Interface]
PrivateKey = <Seckret>
Address = 10.10.10.14/24
DNS = 10.2.0.100
MTU = 1280

[Peer]
PublicKey = <Seckret>
PresharedKey = <Seckret>
AllowedIPs = 0.0.0.0/0, ::/0
PersistentKeepalive = 25
Endpoint = <Seckret>:51820
```

Данные этого файла конфигурации необходимо в неизменном виде разместить в wg-external.conf

```comand
cd /etc/wireguard
nano wg-external.conf
```

Запускаем туннель до внешнего сервера

```comand
wg-quick up wg-external
```

Для проверки работы туннеля команда wg

![Pasted image 20240620153459.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620153459.png)

На текущий момент мы получили такую схему подключения

Теперь нам надо обеспечить возможность конечным пользователям ходить в интернет через наш внутренний сервер.

## **Настройка клиентской части внутреннего сервера**

Для настройки клиентской части был выбран [WG-EASY](https://github.com/wg-easy/wg-easy) но можно использовать любой вариант настройки wg от ручного как предлагает автор оригинальной статьи так и различные графические интерфейсы.

Создадим каталог с docker-compose файлом для запуска wg-easy

```comand
mkdir wg-easy && cd wg-easy
nano docker-compose.yml
```

Вставляем в файл следующее содержимое

```yaml
version: "3"
services:
  wg-easy:
    environment:
      - WG_HOST=192.168.0.132
      - PASSWORD=MySecretPassword
      - WG_PORT=51820
      - WG_DEFAULT_ADDRESS=10.10.9.x
      - WG_DEFAULT_DNS=192.168.0.1
      - WG_ALLOWED_IPS=0.0.0.0/0, ::/0
      - WG_MTU=1280
    image: ditek/wg-easy
    container_name: wg-easy
    volumes:
      - /wg-easy:/etc/wireguard
    ports:
      - "51820:51820/udp"
      - "51821:51821/tcp"
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
      - SYS_MODULE
    sysctls:
      - net.ipv4.ip_forward=1
      - net.ipv4.conf.all.src_valid_mark=1
    dns:
      - 192.168.0.1
```

Для корректной работы необходимо скорректировать некоторые параметры с учетом вашей сети.

В параметр WG\_HOST указываем IP адрес в вашей сети по которому можно будет обратиться к серверу WG. Сервер разделения трафика должен иметь фиксированный IP адрес.

В параметр PASSWORD указываем пароль от веб интерфейса.

Параметр WG\_DEFAULT\_ADDRESS можно оставить без изменений, но если планируете использовать на одном устройстве несколько подключений WG в активном состоянии одновременно необходимо что бы сети не пересекались (актуально для роутеров kenetik). Для этого достаточно изменить на 10.10.8.x или любую другую не используемую подсеть.

Параметр WG\_DEFAULT\_DNS если мы используем только внутри локальной сети то указываем наш роутер или ip на котором установлен локальный DNS. Так же указываем его в секции dns.

Запускаем сборку и установку контейнера

```comand
docker-compose up -d
```

Выходим в корневой каталог

```comand
cd
```

Проверяем работу контейнера

```comand
docker ps
```

![Pasted image 20240620153709.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620153709.png)
Теперь нам доступно создание клиентов на локальном сервере через веб интерфейс по адресу

http://<SerwerIP/>:51821

Заходим с паролем указанным в докер файле и создаем клиента.

Подключаемся к локальному серверу с любого устройства и проверяем работу сети.

Проверяем IP адрес и видим наш адрес удаленного сервера.

Теперь схема подключения выглядит так:

![Pasted image 20240620153715.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620153715.png)

Фактически мы сделали туннель до внешнего сервера через промежуточную точку, осталось сделать разделение трафика на промежуточном сервере.

## **Настройка скрипта разделения**

Идея как и сам скрипт позаимствован из оригинальной статьи автора (рекомендую ознакомится для более точного понимания), Я же перескажу основную идею скрипта. Мы хотим что бы наш локальный интернет не ходил на удаленный сервер а выходил через обычный интернет.

Создадим скрипт в отдельной директории

```console
mkdir exclude_routes
nano update_exclude_routes.sh
```

И вставляем сюда скрипт

```bash
#!/bin/bash
#To crontab (export EDITOR=nano; crontab -e)
#@reboot sleep 30 &amp;&amp; bash /root/update_exclude_routes.sh &gt; /root/update_routes_log.txt 2&gt;&amp;1
#0 3 * * mon bash /root/update_exclude_routes.sh &gt; /root/update_routes_log.txt 2&gt;&amp;1

function ProgressBar {
  let _progress=(${1}*100/${2}*100)/100
  let _done=(${_progress}*4)/10
  let _left=40-$_done
  _fill=$(printf "%${_done}s")
  _empty=$(printf "%${_left}s")
  printf "\rAdd routes to route table (${1}/${2}): [${_fill// /#}${_empty// /-}] ${_progress}%%"
}

#Variables
file_raw="russian_subnets_list_raw.txt"
file_user="subnets_user_list.txt"
file_user_hostnames="hosts_user_list.txt"
file_for_calc="russian_subnets_list_raw_for_calc.txt"
file_processed="russian_subnets_list_processed.txt"
gateway_for_internal_ip=`ip route | awk '/default/ {print $3; exit}'`
interface=` ip link show | awk -F ': ' '/state UP/ &amp;&amp; !/docker/ &amp;&amp; !/veth/ {print $2}' | head -n 1 | sed 's/@.*//' `
#interface="eth0"

#Get addresses RU segment
echo "Download RU subnets..."
curl --progress-bar "https://stat.ripe.net/data/country-resource-list/data.json?resource=ru" | jq -r ".data.resources.ipv4[]" &gt; $file_raw

echo "Deaggregate subnets..."
cat $file_raw |grep "-" &gt; $file_for_calc
cat $file_raw |grep -v "-" &gt; $file_processed
for line in $(cat $file_for_calc); do ipcalc $line |grep -v "deaggregate" &gt;&gt; $file_processed; done

if [ -e $file_user  ]
then echo "Add user subnets..."
  cat $file_user |grep -v "#" &gt;&gt; $file_processed
fi

if [ -e $file_user_hostnames  ]
then echo "Add user hostnames..."
  for line in $(cat $file_user_hostnames); do nslookup line |grep "Address" |grep -v "#" |awk '{print $2"/32"}' &gt;&gt; $file_processed; done
fi

#Flush route table
echo "Flush route table (down interface $interface)..."
ifdown $interface &gt; /dev/null 2&gt;&amp;1
echo "Up interface $interface..."
ifup $interface &gt; /dev/null 2&gt;&amp;1

#Add route
routes_count_in_file=`wc -l $file_processed`
routes_count_current=0
for line in $(cat $file_processed); do ip route add $line via $gateway_for_internal_ip dev $interface; let "routes_count_current+=1" ; ProgressBar ${routes_count_current} ${routes_count_in_file}; done
echo ""

echo "Remove temp files..."
rm $file_raw $file_processed $file_json $file_for_calc

routes_count=`ip r | wc -l`
echo "Routes in routing table: $routes_count"</span>
```

Сохраняем и проверяем работу:

```console
bash update_exclude_routes.sh
```

Данный скрипт можно использовать не только для разделения на Ru и не RU сегмент интернета. Можно использовать любые списки сетей, фактически скрипт делает автоматическое добавление маршрутов.

На данном этапе разделение по подсетям уже готово, все ip адреса которые относятся к RU сегменту будут идти через основной интернет.

Дополнительно мы можем положить в данную папку файлы со списком сайтов и подстей которые мы хотим тоже пустить по короткому маршруту через основной интеренет.

subnets\_user\_list.txt – для подсетей

hosts\_user\_list.txt – для сайтов, адреса определяются через nslookup

Отдельно хочу обратить внимание на параметр interface он должен указывать на ваш основной интерфейс по которому сервер подключен к интернету. Скрипт должен автоматически определить данный интерфейс, но если вы столкнулись с ошибкой вида «Cannot find device "eth0@if27"» при запуске необходимо скорректировать скрипт и вписать руками конкретный интерфейс.

Для этого нам надо определить основной интерфейс подключения. Это можно сделать командой ifconfig

![Pasted image 20240620153950.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620153950.png)

Нас интересует имя того интерфейса который получил адрес в нашей локальной сети.

Корректируем в файле скрипта параметр:

interface="eth0"

И запускаем скрипт

```console
bash update_exclude_routes.sh
```

Фактически мы получили нашу целевую схему:

![Pasted image 20240620154025.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Pasted%20image%2020240620154025.png)

Нам осталось только настроить автоматический запуск при перезагрузке и периодическое обновление таблицы маршрутов.

Добавим авто запуск wg туннеля до удаленного сервера

```console
systemctl enable wg-quick@wg-external.service
```

И добавим запуск скрипта при перезапуске в крон

```console
export EDITOR=nano; crontab -e
```

Добавляем в конец следующие строчки

```console
@reboot sleep 30 && bash exclude_routes/update_exclude_routes.sh > exclude_routes/update_routes_log.txt 2>&1
0 3 * * mon bash exclude_routes/update_exclude_routes.sh > exclude_routes/update_routes_log.txt 2>&1
```

Перезапускаем сервер и проверяем.

Для проверки наличия маршрутов можно воспользоваться командой:

```console
ip r | wc -l
```

Команда возвращает количество прописанных маршрутов, по моим наблюдениям количество маршрутов должно быть более 10000 при корректной работе скрипта.

Подключаем наших конечных пользователей (рекомендую напрямую роутер) и пользуемся.

## **Заключение**

Скрипт не совершенен и в некоторых ситуациях выпадает с ошибками по типу блокировки записей маршрутов и т.д. Я не являюсь автором данного скрипта, моя задача была применить схему и показать другим как ее можно использовать. На текущий момент на моей системе происходит обкатка работы такой схемы разделения. Буду благодарен за идеи как можно доработать скрипт или другие варианты. Возможно кто то предложит телеграмм бота для управления скриптом или еще какие то идеи по улучшению.

P.S. В оригинальной статье разобран так же разобран кейс добавления конкретных маршрутов на удаленный сервер которые мы должны были направить на обычный доступ в интернет. Я разбирал работу этой части скрипта но она полностью еще не адаптирована для использования с WG-EASY, по этому возможно позднее дополню публикацию.