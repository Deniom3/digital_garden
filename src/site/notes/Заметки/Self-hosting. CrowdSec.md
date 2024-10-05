---
{"dg-publish":true,"permalink":"/zametki/self-hosting-crowd-sec/","created":"2024-07-31 22:40","updated":"2024-10-06T00:53:32+03:00"}
---

Файрволл для защиты серверов, альтернатива [[Заметки/Self-hosting. Fail2Ban\|Fail2Ban]]. Наиболее простой способ интеграции в связке с [[Заметки/Self-hosting. Traefik\|Traefik]] необходимо использовать конфигурацию [[Хобби/Конфиги/Traefik Crowdsek\|Traefik Crowdsek]].

Сайт:  [CrowdSec | Curated Threat Intelligence Powered by the Crowd](https://www.crowdsec.net/)

Репозиторий проекта: [GitHub - crowdsecurity/crowdsec: CrowdSec - the open-source and participative security solution offering crowdsourced protection against malicious IPs and access to the most advanced real-world CTI.](https://github.com/crowdsecurity/crowdsec)

## Основные команды для работы

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/komandy-i-nastrojki/nastrojka-crowd-sec-v-dokere/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для получения статуса системы:
```shell
docker exec crowdsec cscli metrics
```

Список заблокированных
```shell
docker exec crowdsec cscli decisions list
```

Ручная блокировка по IP
```shell
docker exec crowdsec cscli decisions add --ip <ip>
```

Снятие блокировки
```shell
docker exec crowdsec cscli decisions delete --ip <ip>
```

</div></div>

## Настройка в связке с traefik

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/crowd-sec/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  crowdsec:
    image: crowdsecurity/crowdsec:latest
    container_name: crowdsec
    environment:
      GID: "${GID-1000}"
      COLLECTIONS: "crowdsecurity/linux crowdsecurity/traefik crowdsecurity/appsec-virtual-patching crowdsecurity/appsec-generic-rules firix/authentik Dominic-Wagner/vaultwarden gauth-fr/immich aidalinfo/couchdb LePresidente/gitea"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./acquis.yaml:/etc/crowdsec/acquis.yaml
      - ./db:/var/lib/crowdsec/data/
      - ./config:/etc/crowdsec/
      - /home/deniom/docker/traefik/logs:/var/log/traefik/:ro
    ports:
      - 6060:6060
      - 8081:8080
    networks:
      - proxy
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped

  dashboard:
    image: metabase/metabase:latest
    container_name: crowdsec-dashboard
    restart: always
    mem_limit: 512m
    ports:
      - 3000:3000
    environment:
      MB_DB_FILE: /data/metabase.db
      MGID: "${GID-1000}"
      JAVA_OPTS: "-Xmx256m"
    depends_on:
      - 'crowdsec'
    volumes:
      - ./db:/metabase-data/
      - ./metabase:/data/
    networks:
      - proxy

networks:
  proxy:
    external: true
```



</div></div>


Для работы с [[Заметки/Self-hosting. Traefik\|Traefik]] можно использовать вариант настройки как через внешний bouncer контейнер так и с использованием плагина traefik. Вариант с плагином является более предпочтительным так как этот вариант регулярно обновляется и развивается.

Плагин: [Plugin](https://plugins.traefik.io/plugins/6335346ca4caa9ddeffda116/crowdsec-bouncer-traefik-plugin)

Для настройки необходимо дополнить конфигурацию блоком:
```
---
listen_addr: 0.0.0.0:7422
appsec_config: crowdsecurity/virtual-patching
name: Traefik
source: appsec
labels:
  type: appsec
```
### Пример итоговой конфигурации

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/crowdsek-acquis/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
filenames:
  - /var/log/traefik/*
labels:
  type: traefik

---
listen_addr: 0.0.0.0:7422
appsec_config: crowdsecurity/virtual-patching
name: Traefik
source: appsec
labels:
  type: appsec

---
source: docker
container_name:
 - authentik-server-1
labels:
  type: authentik
docker_host: tcp://192.168.0.131:2375

---
source: docker
container_name:
  - traefik
labels:
  type: traefik
docker_host: tcp://192.168.0.131:2375

---
source: docker
container_name:
  - vaultwarden
labels:
  type: Vaultwarden
docker_host: tcp://192.168.0.132:2375

---
source: docker
container_name:
  - immich_server
labels:
  type: immich
docker_host: tcp://192.168.0.136:2375

---
source: docker
container_name:
  - couchdb
labels:
  type: couchdb
docker_host: tcp://192.168.0.132:2375

---
source: docker
container_name:
  - gitea
labels:
  type: gitea
docker_host: tcp://192.168.0.132:2375
```


</div></div>


А так же установить плагин в traefik путем добавления в статическую конфигурацию запись:
```yaml
experimental:
  plugins:
    crowdsec-bouncer-traefik-plugin:
      moduleName: "github.com/maxlerebourg/crowdsec-bouncer-traefik-plugin"
      version: "v1.3.4-beta1"
```

`version` - необходимо изменять руками, нет возможности использовать latest.

Для подключения к Traefik bouncer необходимо сгенерировать LAPI_KEY командой:
```shell
docker exec crowdsec cscli bouncers add bouncer-traefik
```

И добавить промежуточную обработку:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/traefik-crowdsek/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Конфигурация traefik для crowdsek

</div>



Позволяет использовать [[Заметки/Self-hosting. CrowdSec\|crowdsek]] вместе с [[Заметки/Self-hosting. Traefik\|Traefik]]. Необходимо создать отдельный файл в папке `traefik/dynamic`

```yml
http:
    middlewares:
        crowdsec:
            plugin:
                crowdsec-bouncer-traefik-plugin:
                    enabled: true
                    logLevel: INFO
                    updateIntervalSeconds: 15
                    updateMaxFailure: 0
                    defaultDecisionSeconds: 15
                    httpTimeoutSeconds: 10
                    crowdsecMode: stream
                    crowdsecAppsecEnabled: true
                    crowdsecAppsecHost: crowdsec:7422
                    crowdsecAppsecFailureBlock: true
                    crowdsecAppsecUnreachableBlock: true
                    crowdsecLapiKey: <LAPI_KEY>     
                    crowdsecLapiHost: crowdsec:8080
                    crowdsecLapiScheme: http
                    forwardedHeadersTrustedIPs:
                      - 172.21.0.0/16                                                            # Reverse Proxy IP address 
                    clientTrustedIPs:
                      - 192.168.0.0/24
```

А так же добавить обращение к middlewares в [[Хобби/Конфиги/Traefik\|статическую конфигурацию]]
```
...
entryPoints:
  http:
    address: ":80"
    http:
      middlewares:
        - crowdsec@file
      redirections:
        entryPoint:
          to: https
          scheme: https
  https:
    address: ":443"
    http:
      middlewares:
        - crowdsec@file
...
```

</div></div>

## Защита отдельных программ

Кроме общего мониторинга угроз и уязвимостей которые могут успешно проверяться и блокироваться на реверс прокси, существуют так же угрозы жесткой силы по перебору паролей или уязвимости самих систем. Для анализа таких уязвимостей необходимо выполнять подключение непосредственно к защищаемой программе и анализировать ее лог файлы.

Анализ и защита выполняется при помощи: [Curated Security Scenarios & Parsers | CrowdSec Collections](https://app.crowdsec.net/hub/collections)

Для анализа необходимо настроить доступ к docker контейнерам для анализа логов. Самый правильный вариант это организовать доступ с использованием [[Заметки/Self-hosting. Dockerproxy\|Dockerproxy]] для работы должна быть обязательно установлена переменная окружения в docker compose фале dockerproxy: 

```yml
...
	environment:
      - INFO=1 # Для работы Crowdsek
...
```

Подключение программы для анализа логов выполняется в двух местах конфигурации crowdsec. В файл запуска crowdsec docker compose необходимо добавить `COLLECTIONS`.
### Изменение docker compose

Название COLLECTIONS можно найти в [перечне всех анализаторов](https://app.crowdsec.net/hub/collections), команда добавления приведена в виде: 
```
cscli collections install <Collection_Name>
```

Для docker необходимо использовать только `<Collection_Name>`

### Изменение файла конфигурации

В файл `acquis.yaml` добавить запись следующего вида:
```yaml
---
source: docker
container_name:
 - authentik-server-1
labels:
  type: authentik
docker_host: tcp://192.168.0.131:2375
```
где `type` - берется из документации к компоненте анализа
## Использование в Home Assistant OS

Самый простой вариант настройки это использовать как дополнение Home Assistant.

Репозиторий: [GitHub - crowdsecurity/home-assistant-addons: Home Assistant Crowdsec Addons](https://github.com/crowdsecurity/home-assistant-addons)

В рамках настройки необходимо получить ключ для подключения bouncer используя команду в консоли дополнения Crowdsec:
```
cscli bouncers add home-assistant
```

Полученный ключ необходимо установить в параметры Crowdsec Firewall Bouncer.

## Обновление базы данных угроз

Обновление базы данных выполняется командами:
```sh
docker exec crowdsec cscli hub update && docker exec crowdsec cscli hub upgrade
```

Данный процесс можно автоматизировать добавив в cron запись `crontab -e`:
```
* 3 * * * docker exec crowdsec cscli hub update && docker exec crowdsec cscli hub upgrade
```
При такой настройке обновление будет выполняться каждые три часа.

## Настройка панели мониторинга

Для организации панели мониторинга используется [[Заметки/Self-hosting. Metabase\|Metabase]] с заготовленным схемой базы данных и дашбордов от компании Crowdsek.

Пример панели
![Self-hosting. CrowdSec.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/Self-hosting.%20CrowdSec.png)

Источник опорных материалов: [example-docker-compose/basic at main · crowdsecurity/example-docker-compose · GitHub](https://github.com/crowdsecurity/example-docker-compose/tree/main/basic)

Metabase использует базу данных и файлы crowdsek из папки `./db` которая соответствует `/var/lib/crowdsec/data/` в контейнере, они формуются в процессе работы. Все настройки сохраняются в папке `./metabase`

Для настройки необходимо выполнить предварительную загрузку схемы находясь в папке docker контейнера:
```sh
mkdir metabase 
wget https://crowdsec-statics-assets.s3-eu-west-1.amazonaws.com/metabase_sqlite.zip
unzip metabase_sqlite.zip -d metabase
```

Файл настроек:
![[metabase_sqlite.zip]]

Загруженная схема базы имеет пред настроенные дашборды и отчеты по аналитике crowdsec. 

> [!attention] Данные авторизации по умолчанию
> Логин: `crowdsec@crowdsec.net`
> Пароль: `!!Cr0wdS3c_M3t4b4s3??`

Metabase основан на java что приводит к ряду не очень приятных с точки зрения оптимизации проблемам. Основная из которых высокое потребление оперативной памяти даже в режиме простоя. Фактически программа съест все что дадут и нечего не вернет. 

> [!error]
> В базовой настройке metabase может потреблять до 4Гб оперативной памяти что вообще не оправдано.

Для исправления ситуации необходимо ограничить потребление памяти параметрами `JAVA_OPTS` и `mem_limit`, минимально рабочая конфигурация выявленная в рамках экспериментов 256Мб на java машину в параметре JAVA_OPTS и 512Мб для всего контейнера в таком виде потребление ОЗУ в простое будет минимальное 200-300Мб и при запросе до 500.

> [!caution] Предупреждение
> При такой настройке запросы и открытие панелей может выполняться очень долго. Оптимальным с точки зрения запуска будет 0.75-1Гб ОЗУ, но как по мне это слишком неоправдано.

## Мониторинг с использование prometheus и grafana

Подробно разобрано в [[Заметки/Мониторинг Crowdsec с использованием Prometheus\|Мониторинг Crowdsec с использованием Prometheus]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]
> - [[Заметки/Self-hosting. Metabase\|Metabase]]
> - [[Заметки/Self-hosting. Traefik\|Traefik]]
> - [[Home Assistant\|Home Assistant]]

> [!info]-
> Примечание:: Сетевой фаервол для реверс прокси

> [!todo]-
> - [x] Настроить сбор статистики для CrowdSek ✅ 2024-09-12
>- [x] Изучить вопрос настройки плагинов под каждый сайт типа Home Assistant или Gitea ➕ 2024-09-14 ✅ 2024-10-06
