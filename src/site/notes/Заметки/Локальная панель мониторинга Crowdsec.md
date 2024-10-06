---
{"dg-publish":true,"permalink":"/zametki/lokalnaya-panel-monitoringa-crowdsec/","created":"2024-10-06 23:34","updated":"2024-10-06T23:52:50+03:00"}
---

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

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. CrowdSec\|Self-hosting. CrowdSec]]
> - [[Заметки/Self-hosting. Metabase\|Self-hosting. Metabase]]