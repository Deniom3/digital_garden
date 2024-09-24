---
{"dg-publish":true,"permalink":"/zametki/self-hosting-fail2-ban/","created":"2024-07-03 20:50","updated":"2024-09-24T23:31:01+03:00"}
---

Программа для мониторинга доступа к серверам и блокировки неавторизованного доступа. Работает на основе анализа логов других систем типа ssh или реверс прокси. Обязательный элемент безопасности внешнего сервера.

Репозиторий: [GitHub - fail2ban/fail2ban: Daemon to ban hosts that cause multiple authentication errors](https://github.com/fail2ban/fail2ban)
### Пример docker compose файла

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Fail2ban

</div>



Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
Подобно разобрано в [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|статье]]

Настройки для работы с Caddy приведены в конфиг [[Хобби/Конфиги/Fail2Ban\|файле]] 

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


</div></div>


### Конфигурация для защиты SSH 

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```shell
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
findtime = 300
backend = systemd
```

_\* где:_

-   _**sshd —**_ _название для правила;_
-   _**enabled**_ _позволяет быстро включать (true) или отключать (false) правило;_
-   _**port —**_ _порт целевого сервиса. Принимается буквенное или цифирное обозначение;_
-   _**filter —**_ _фильтр (критерий поиска), который будет использоваться для поиска подозрительных действий. По сути, это имя файла из каталога_ _**/etc/fail2ban/filter.d**_ _без .conf на конце;_
-   _**logpath —**_ _расположение лог-файла, в котором фильтр будет искать подозрительную активность на основе описанных критериев._
-   _**maxretry —**_ _количество действий, которые разрешено совершить до бана._
-   _**findtime —**_ _время в секундах, в течение которого учитывается maxretry;_

</div></div>

### Конфигурация для Caddy

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Fail2Ban

</div>




Пример настроек фильтрации для [[Хобби/Docker compose/Fail2ban\|Fail2ban]] в связке с [[Хобби/Docker compose/Caddy\|Caddy]] 

Подробное описание в [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|статье]].
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

```conf
# /docker/fail2ban/data/filter.d
 
[Definition]
# catch all lines that terminate with an unauthorized error
failregex = <HOST> \S+ \S+ \S+ (401)$
ignoreregex =
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Команды и настройки/Настройка исключений IP для Fail2Ban\|Настройка исключений IP для Fail2Ban]]
> - [[Хобби/Команды и настройки/Настройка Fail2Ban для защиты SSH\|Настройка Fail2Ban для защиты SSH]]
> - [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|Настройка реверс прокси Caddy и Fail2Ban в docker]]
> - [[Статьи/Памятка по безопасности VPS, VDS сервера Ubuntu, Debian\|Памятка по безопасности VPS, VDS сервера Ubuntu, Debian]]

> [!info]-
> Примечание:: Сетевой фаервол
