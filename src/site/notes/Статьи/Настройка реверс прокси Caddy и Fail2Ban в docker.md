---
{"dg-publish":true,"permalink":"/stati/nastrojka-revers-proksi-caddy-i-fail2-ban-v-docker/"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]
> [!urls]- Заметки по статье

---
## **Вступление, или немного о том зачем и почему**

В моей домашней системе за последнее время накопилось значительное число сервисов,  для того что бы их использовать в какой то момент стало банально не удобно и не рационально использовать доступ по http и порту. Кроме того существует ряд сервисов которое в принципе не будут работать без использования шифрования. Первоначально в моей системе использовался реверс прокси nginx proxy manager с бесплатными duckdns адресами. Со временем я перешел на использование своего домена.

Важной особенностью моего сценария использования являлось то что реверс прокси использовалось только внутри локальной\\впн сети и не имело внешнего доступа на 80/443 порту в сеть. Для получения сертификата обычными средствами необходимо что бы сервер мог оставить запись на 80 порту, но такой сценарий не выполним если у вас нет белого ip или возможности пробросить порты. Сценарий со временным открытием портов для обновления так же мной был признан не самым удобным по причине того что можно банально забыть. Благо для такого есть решение под названием dns challenge. При использовании такого метода подтверждения подлинности сайта выполняется путем изменения dns записи с использованием api. Такой сервис предоставляет ряд провайдеров dns, из подходящих под мои задачи это cloudflare и duckdns.

Для использования многих сервисов cloudflare таких как api и защита от ddos не обязательно покупать у них домен, достаточно просто перенести управление в cloudflare.

Все бы было хорошо система работала с использованием nginx proxy manager, а для внешнего доступа к ресурсам использовалась встроенная публикация сервисов keenetic. Но в один не особо прекрасный момент использования реверс прокси я столкнулся с проблемой что перестали обновляться автоматически сертификаты. Проблема как оказалось достаточна старая и исправления к ней полноценного не было, да и сам npm как по мне казался несколько избыточным и перегруженным для моего сценария использования.

Таким образом началось изучение альтернативных решений для организации реверс прокси. Две самые популярные альтернативы из доступных решений это traefik и caddy.

traefik - это достаточно интересное и универсальное решение, но оно в первую очередь делает упор на динамически изменяемые системы. Очень хорошо работает с докер контейнерами путем установки настроек в метках контейнеров. Но в моем кейсе использования динамическое масштабирование не планировалось и существовало много сервисов которые были запущены без использования докера.

caddy в свою очередь является очень легким в настройке сервисом с большим количеством различных модулей на все случаи жизни. Единственным минусом можно выделить отсутствие интерфейса для настройки, все настройки задаются в файле (но об этом дальше).

В данной статье я не планирую проводить сравнение возможностей traefik и caddy подобных сравнений на просторах сети достаточно, для моей задачи больше подошел caddy.

Все дальнейшее развертывание будет показано с использованием docker-compose. Запуск контейнеров и сборка контейнеров выполнялась в portainer, но вы можете просто создать свой docker-compose файл и запустить без использования portainer.

## **Базовый вариант настройки Сaddy**

Отдельно стоит отметить что сам по себе caddy крайне легковесный но и ограниченный в функционале, значительная часть возможностей предложена в дополнительных [модулях](https://caddyserver.com/docs/modules/). Эти модули можно как добавлять самому при сборке докер контейнера или при запуске apt пакета так и использовать готовые сборки docker с включенными модулями.

На самом деле настройка реверс прокси в Caddy это крайне простая задача. Запустим докер контейнер с использованием следующего docker-compose

```yaml
version: '3.8'
services:
  caddy:
    image: 'slothcroissant/caddy-cloudflaredns:latest'
    container_name: caddy
    restart: unless-stopped
    ports:
      - '80:80'
      - '443:443'
    environment:
      ACME_AGREE: true
      CLOUDFLARE_API_TOKEN: "aaaaaaaaadssssssssssssssssss"
      CLOUDFLARE_EMAIL: "asdasdasd@gmail.com"
    volumes:
      - /docker/caddy/data:/data
      - /docker/caddy/config:/config
      - /docker/caddy/Caddyfile:/etc/caddy/Caddyfile
```


В данном примере использован готовый caddy контейнер для получения сертификатов с использованием dns challenge cloudflare. Так же существуют готовые сборки для других провайдеров например вот [тут](https://github.com/serfriz/caddy-custom-builds) предложена неплохая подборка самых распространенных сборок. 

Для получения с сертификата через dns chelenge необходимо создать api ключ на cloudflare, подробней об этом приведено [тут](https://github.com/caddy-dns/cloudflare).

Теперь создадим файл конфигурации на основании которого будет работать наш сервер. Этот файл называется Caddyfile он не имеет расширения и должен располагаться по пути /docker/caddy/Caddyfile (для данного варианта docker-compose).

```Caddyfile
nextcloud.mydomain.ru {
  tls myemail@gmail.com { 
    dns cloudflare {env.CLOUDFLARE_API_TOKEN}
  }
  reverse_proxy 192.168.0.101:1234
}
```


Фактически у нас есть всего две секции tls и reverse_proxy. 

Секция tls отвечает за получение сертификата, email можно не указывать он необходим для получения напоминаний от acme о продлении сертификатов.

Секция reverse_proxy описывает сами правила переадресации, для подавляющего большинства сервисов такого варианта настройки будет достаточно, но бывают исключения например vaultwarden.
```Caddyfile
vaultwarden.mydomain.ru {
  tls myemail@gmail.com { 
    dns cloudflare {env.CLOUDFLARE_API_TOKEN}
  }
  reverse_proxy 192.168.0.132:10380 {
	header_up X-Real-IP {remote_host}
  }
}
```

Обычно если сервис требует особых настроек для реверс прокси в документации приводятся примеры для самых популярных решений, в том числе и caddy.

Для применения изменений необходимо перезапустить докер контейнер.

На этом простой вариант настройки caddy закончен, если вы не планируете использовать его для публикации в интернет то можно использовать как есть и наслаждаться. Главное не забыть правильно указать dns записи.

Если в настройках доменов в cloudflare указать внутренний ip например 192.168.0.132 тогда при использовании их dns можно обойтись без перезаписи и ходить только внутри своей локальной сети по https.

**А теперь все становиться чуточку сложнее... настройка Caddy для публикации в интернет**

Для использования fail2ban нам необходимо настроить правильное и удобное логирование в caddy. Сам по себе caddy формирует логи в формате json которые в принципе достаточно удобно парсятся но с ними не очень удобно работать для fail2ban, в таком случаем нам на помощь приходит модуль [transform-encoder](https://github.com/caddyserver/transform-encoder) данный модуль позволяет указать шаблон форматирования для логов.

Готового решения докер контейнера caddy-dns/cloudflare и caddyserver/transform-encoder увы найти мне не удалось так что будем создавать свой докер образ. Для этого нам понадобиться следующий Dockerfile.

```Dockerfile
FROM caddy:builder AS builder

RUN xcaddy build \
    --with github.com/caddy-dns/cloudflare \
    --with github.com/caddyserver/transform-encoder \
    --with github.com/abiosoft/caddy-exec

FROM caddy:latest

COPY --from=builder /usr/bin/caddy /usr/bin/caddy
```

Dockerfile размещаем в той же папке где и наш docker-compose файл со следующим содержимым

```yaml
version: '3.9'
services:
  caddy:
    image: caddy-cloudflare-transform:latest
    build:
     context: .
     dockerfile: ./Dockerfile
    container_name: caddy
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
    ports:
      - 80:80
      - 443:443 
    environment:
      ACME_AGREE: true
      CLOUDFLARE_API_TOKEN: "aaaaaaaaassdsadfasfasf"
      CLOUDFLARE_EMAIL: "myemail@gmail.com"
      DOMAIN: mydomain.ru
    volumes:
      - /docker/caddy/data:/data
      - /docker/caddy/config:/config
      - /docker/caddy/logs:/logs
      - /docker/caddy/Caddyfile:/etc/caddy/Caddyfile 
```

Или можно воспользоваться моим собранным образом размещенным в [репозитории](https://github.com/Deniom3/caddy-cloudflare-transform)

Тогда docker-compose файл будет иметь вид

```yaml
version: '3.9'
services:
  caddy:
    image: ghcr.io/deniom3/caddy-cloudflare-transform:latest
    container_name: caddy
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
    ports:
      - 80:80
      - 443:443 
    environment:
      ACME_AGREE: true
      CLOUDFLARE_API_TOKEN: "aaaaaaaaassdsadfasfasf"
      CLOUDFLARE_EMAIL: "myemail@gmail.com"
      DOMAIN: mydomain.ru
    volumes:
      - /docker/caddy/data:/data
      - /docker/caddy/config:/config
      - /docker/caddy/logs:/logs
      - /docker/caddy/Caddyfile:/etc/caddy/Caddyfile
```

Отдельно обращаю внимание на environment  DOMAIN она нам нужна для более простого (и безопасного) формирования Caddyfile.

Создадим Caddyfile с такой структурой:

```Caddyfile

(common) {
	header /* {
	-Server
	}
}

(cloudflare) {
	import common
	tls { 
		dns cloudflare {env.CLOUDFLARE_API_TOKEN}
	}
}

(local) {
	@allowed remote_ip 192.168.0.0/24
	handle {
		respond 401
	}
}

{
	log access-log {
		include http.log.access
		output file /logs/access.log {
		  roll_keep_for 48h
		}
		format transform `{ts} {request>headers>X-Forwarded-For>[0]:request>remote_ip} {request>host} {request>method} {request>uri} {status}` {
			time_format "02/Jan/2006:15:04:05"
		}
	}
}

jackett.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.134:9117
	}
}

home-assistant.{env.DOMAIN} {
	import cloudflare
	
	log
	
	reverse_proxy 192.168.0.125:8123
}

vaultwarden.{env.DOMAIN} {
	import cloudflare
	
	log
	
	reverse_proxy 192.168.0.132:10380 {
		header_up X-Real-IP {remote_host}
	}
}
```
Пройдемся немного по блокам что бы понимать для чего каждый из них нужен.

Секции которые объявляются в скобках () являются импортируемым, это настройки которые мы определяем один раз и можем использовать для наших остальных настроек путем вызова через import. При этом каждый такой блок может импортировать в себя другие, тем самым создавая сложные структуры.

Секция common отвечает за удаление из ответа сервера имени сервера в нашем случае Caddy, это небольшая перестраховка для того что бы злоумышленники не могли по виду сервера использовать его уязвимости.

Секция cloudflare отвечает за настройки получения сертификата с использованием dns challenge.

Секция local служит для ограничения доступности к опубликованному сайту. В remote_ip указываются подсети или ip для которых доступ должен быть РАЗРЕШЕН, все остальные будут блокироваться и получать ответ указанный в handle respond в данном случае 401. Эта секция позволяет нам разделить и использовать один реверс прокси для внешней публикации и внутренней локальной сети.

Еще одна важная секция необходимая для работы f2b это секция описания логов и их преобразования. Переопределение настроек логирования без указания конкретных hostname применяет эту настройку на все.

В данном примере файл храниться в /logs/access.log внутри контейнера который соответствует на сервере /docker/caddy/logs Логи будут храниться 48 часов.

Теперь рассмотрим более подробно добавление конкретных сайтов, если нам надо использовать  реверс прокси только для локальной сети используем import local и запись как на примере с jackett, при этом домен можно подставлять путем автозамены как в примере, так как мы его передавали в переменные окружения при запуске контейнера.

Если нам надо что бы наш реверс прокси пускал не только с ограниченного списка сетей то используем запись как в примере home-assistant, тут у нас нет секции local и простая запись reverse_proxy без проверки допустимых сетей.

Если для сайта необходимо включить fail2ban обязательно указываем что должны вестись логи путем добавления записи log.

На этом основная настройка закончена, прокидываем 443 порт с роутера на наш сервер через переадресацию портов и наслаждаемся нашествием китайских ботов.

## **Кыш ботнет, или настройка f2b в докере для caddy**

Для своей работы f2b использует логи которые предоставляют другие сервисы, на предыдущем этапе мы настроили логи caddy для их использования с f2b теперь запустим сам docker контейнер с f2b.

```yaml
version: "3.9"
 
services:
  fail2ban:
    image: crazymax/fail2ban:latest
    container_name: fail2ban
    network_mode: host
    cap_add:
      - NET_ADMIN
      - NET_RAW
    environment:
      F2B_LOG_LEVEL: INFO
      F2B_LOG_TARGET: STDOUT
      F2B_DB_PURGE_AGE: 1d
    volumes:
      - /docker/fail2ban/data:/data
      - /docker/caddy/logs/access.log:/docker/caddy/logs/access.log:ro
    restart: unless-stopped
```

Обязательно даем право на чтение файла логов доступа caddy 

- /docker/caddy/logs/access.log:/docker/caddy/logs/access.log:ro

Если данного файла еще нет то может возникнуть ошибка в работе так как docker создаст вместо файла папку с именем access.log Для исправления необходимо зайти на несколько сайтов через реверс прокси и проверить что логи создаются.

Теперь настроим параметры работы f2b

В папке /docker/fail2ban/data/filter.d размещаем файл caddy-custom.conf со следующими настройками. При такой настройке все обращения которые получили ответ 401 будут попадать в бан лист.

```conf
# /docker/fail2ban/data/filter.d
 
[Definition]
# catch all lines that terminate with an unauthorized error
failregex = <HOST> \S+ \S+ \S+ (401)$
ignoreregex =
```

В папке /docker/fail2ban/data/jail.d файл caddy.conf

```conf
#/docker/fail2ban/data/jail.d/caddy.conf

[caddy]
backend = auto
enabled = true
chain = FORWARD
protocol = tcp
port = http,https
filter = caddy-custom
maxretry = 3
bantime = 86400
findtime = 43200
logpath = /docker/caddy/logs/access.log
ignoreip = 192.168.0.0/24
```

Запись ignoreip служит для того что бы наша локальная сеть в случае каких то ошибок с нашей стороны не улетела в бан.

Перезапускаем контейнер и наслаждаемся спокойной жизнью. Но это не точно....

## **Подсказки и заметки на полях**

Некогда не удаляет файл логов при работе caddy в некоторых случаях система может повести себя крайне не адекватно и файл больше не создастся. Перед удалением обязательно останавливайте контейнер.

Для того что бы разбанить кого то в f2b при использовании в докере нам необходимо для стандартных команд добавлять команды по обращению внутрь контейнера:

Выводим статус fail2ban

```
docker exec -it fail2ban fail2ban-client status
```

 Выводим статус конкретной цепочки (имя цепочки берётся из Jail list предыдущей команды)

```
docker exec -it fail2ban fail2ban-client status ИМЯ_ИЗ_JAIL_LIST
```

Разблокируем требуемый IP-адрес из соответствующей цепочки

```
docker exec -it fail2ban fail2ban-client set ИМЯ_ИЗ_JAIL_LIST unbanip IP_ДЛЯ_РАЗБЛОКИРОВКИ
```

Для случая с использованием caddy команда разбана будет выглядеть так

```
docker exec -it fail2ban fail2ban-client set caddy unbanip IP_ДЛЯ_РАЗБЛОКИРОВКИ
```

Спасибо за внимание