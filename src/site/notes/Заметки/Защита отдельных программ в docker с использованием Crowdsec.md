---
{"dg-publish":true,"permalink":"/zametki/zashhita-otdelnyh-programm-v-docker-s-ispolzovaniem-crowdsec/","created":"2024-10-06 23:42","updated":"2024-10-06T23:50:33+03:00"}
---

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

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. CrowdSec\|Self-hosting. CrowdSec]]
> - [[Заметки/Self-hosting. Dockerproxy\|Docker socket proxy]]