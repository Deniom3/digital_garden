---
{"dg-publish":true,"permalink":"/zametki/self-hosting-grafana/","created":"2024-07-04 00:36","updated":"2024-10-01T20:26:09+03:00"}
---

Программа с открытым исходным кодом для организации дашбордов и уведомлений по результатам мониторинга компьютерных систем. Имеет очень гибкую настройку выводимых данных и может использоваться с большим количеством различных баз данных. Сама по себе не является базой данных и предназначена только для вывода и анализа.

Официальный сайт: [Grafana: The open observability platform | Grafana Labs](https://grafana.com/)

Репозиторий: [GitHub - grafana/grafana: The open and composable observability and data visualization platform. Visualize metrics, logs, and traces from multiple sources like Prometheus, Loki, Elasticsearch, InfluxDB, Postgres and many more.](https://github.com/grafana/grafana)
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/monitoring/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Monitoring

</div>




```yaml
services:
  influxdb:
    image: influxdb:latest
    container_name: influxdb
    restart: always
    ports:
      - 8086:8086
    volumes:
      - ./influxdb/data:/var/lib/influxdb2
      - ./influxdb/config:/etc/influxdb2
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  grafana:
    image: grafana/grafana:latest
    container_name: grafana-server
    restart: always
    depends_on:
      - influxdb
    links:
      - influxdb
    ports:
      - 3000:3000
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning/dashboards:/etc/grafana/provisioning/dashboards
      - ./grafana/provisioning/datasources:/etc/grafana/provisioning/datasources
    environment:
      - GF_SERVER_ROOT_URL=${GF_SERVER_ROOT_URL}
      - GF_AUTH_GENERIC_OAUTH_ENABLED=false
      - GF_AUTH_GENERIC_OAUTH_NAME=${GF_AUTH_GENERIC_OAUTH_NAME}
      - GF_AUTH_GENERIC_OAUTH_CLIENT_ID=${GF_AUTH_GENERIC_OAUTH_CLIENT_ID}
      - GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET=${GF_AUTH_GENERIC_OAUTH_CLIENT_SECRET}
      - GF_AUTH_GENERIC_OAUTH_SCOPES=${GF_AUTH_GENERIC_OAUTH_SCOPES}
      - GF_AUTH_GENERIC_OAUTH_AUTH_URL=${GF_AUTH_GENERIC_OAUTH_AUTH_URL}
      - GF_AUTH_GENERIC_OAUTH_TOKEN_URL=${GF_AUTH_GENERIC_OAUTH_TOKEN_URL}
      - GF_AUTH_GENERIC_OAUTH_API_URL=${GF_AUTH_GENERIC_OAUTH_API_URL}
      - GF_AUTH_SIGNOUT_REDIRECT_URL=${GF_AUTH_SIGNOUT_REDIRECT_URL}
      - GF_AUTH_OAUTH_AUTO_LOGIN=false
      - GF_AUTH_GENERIC_OAUTH_ROLE_ATTRIBUTE_PATH="contains(groups, 'Grafana
        Admins') && 'Admin' || contains(groups, 'Grafana Editors') && 'Editor'
        || 'Viewer'"
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  prometheus:
    image: prom/prometheus:latest
    container_name: prometheus
    volumes:
      - ./prometheus:/etc/prometheus
      - prometheus_data:/prometheus
    ports:
      - 9090:9090
    command:
      - --config.file=/etc/prometheus/prometheus.yml
      - --storage.tsdb.path=/prometheus
      - --web.console.libraries=/etc/prometheus/console_libraries
      - --web.console.templates=/etc/prometheus/consoles
      - --storage.tsdb.retention.time=15d
      - --storage.tsdb.retention.size=10GB
      - --web.enable-lifecycle
    restart: unless-stopped
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  nodeexporter:
    image: prom/node-exporter:latest
    container_name: nodeexporter
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - --path.procfs=/host/proc
      - --path.rootfs=/rootfs
      - --path.sysfs=/host/sys
      - --collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($|/)
    restart: unless-stopped
    ports:
      - 9100:9100
    expose:
      - 9100
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
    container_name: cadvisor
    privileged: true
    devices:
      - /dev/kmsg:/dev/kmsg
    volumes:
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker:/var/lib/docker:ro
      - /cgroup:/cgroup:ro #doesn't work on MacOS only for Linux
    restart: unless-stopped
    ports:
      - 8090:8080
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
  pushgateway:
    image: prom/pushgateway:latest
    container_name: pushgateway
    restart: unless-stopped
    ports:
      - 9091:9091
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
volumes:
  grafana_data: {}
  prometheus_data: {}
networks:
  monitoring:
    name: monitoring
    driver: bridge
```

> [!note]
> Метка используется для отделения стека мониторинга в панелях мониторинга
> ```
>     labels:
>       org.label-schema.group: monitoring
> ```




</div></div>



<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/zametki/nastrojka-uvedomlenij-grafana/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Основным преимуществом настройки уведомлений из Grafana является агрегация нескольких источников данных. Можно построить запрос и уведомления агрегируя сразу данные из [[Заметки/Self-hosting. InfluxDB\|InfluxDB]] и [[Заметки/Self-hosting. Prometheus\|Prometheus]]. 

Основным минусом является достаточно сложная и путанная настройка уведомлений и не очень понятная документация.

При создании уведомлений можно применять шаблоны составленные по [[Заметки/Работа с шаблонами Grafana Alert\|правилам]].
### Основной порядок настройки уведомлений:

1. Создание события уведомления `Alert rule`. Событие может быть создано из панели или напрямую в интерфейсе.
2. Задать название событию.
![Программа визуализации статистики Grafana-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-2.png)
3. Добавить запрос данных из источника. Если была выбрана конкретная панель для создания используются запрос из панели (надо скорректировать отборы).
![Программа визуализации статистики Grafana-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-3.png)
> [!bug] Ограничение
> Судя по всему нет возможности на прямую соединить два разных запроса из разных источников в пост обработке.
5. Настройка пост обработки. Не обязательно можно сразу использовать результат запроса.
	1. Блок `Reduce` используется для сокращения запроса до одного значения на ряд.
	2. Блок `Threshold` устанавливает правила по которым формируется уведомления например значение больше N.
6. Установить источник уведомления. У одного из блоков должно быть прожато `Set as alert condition` этот блок будет отвечать за вызов уведомления.
![Программа визуализации статистики Grafana-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-4.png)
6.  Настройка проверки условий
	1. Для настройки надо создать папку
	2. В папке надо создать или выбрать группу. В группе устанавливается как часто будет выполняться проверка данных на вызов события `Evaluation interval`.
	3. Установить `Pending period` это время которое допускается срабатывания события без отправки уведомления. Грубо говоря задержка для перекрытия всплесков. Может быть установлено только кратно `Evaluation interval`.
	4. Для некоторых запросов важно настроить поведение когда нет данных в запросе в пункте `Alert state if no data or all values are null`  что бы избежать лишних срабатываний.
![Программа визуализации статистики Grafana-5.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-5.png)
7. Установка правил уведомления.
	1. Можно установить конкретную точку уведомления (не рекомендуется)
	2. Правильно установить метку по которой будет выполняться вызов уведомления.
![Программа визуализации статистики Grafana-6.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-6.png)
8. Задание описания и данных уведомления
	1. В основном я использую блок `Description` и там формирую [[Заметки/Работа с шаблонами Grafana Alert#Шаблоны для сообщений\|шаблонами]] уведомления для отправки.
	2. В пункте `Dashboard and panel` можно сменить привязку к конкретной панель. Эту панель можно будет вывести в сообщении и возле нее на дашборде появится значок уведомления и контроля события.
	3. Дополнительные данные можно передать через `Custom annotation` которые можно использовать в [[Заметки/Работа с шаблонами Grafana Alert#Шаблоны для сообщений\|шаблоне]].
![Программа визуализации статистики Grafana-7.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-7.png)
9. Основной этап создания события уведомления закончен.

### Настройка отправителей уведомлений:

Настройка отправки сообщений выполняется в разделе `Contact points` тут же определяются шаблоны сообщений в разделе `Notification Templates`. 

Contact Points может быть использован для любого количества правил уведомления. 

Задание шаблона сообщения выполняется в конкретной `Contact Points` в пункте `Message Template:`.

У шаблона обязательно должно быть задан заголовок в формате
```
{{ define "Уведомление о перегреве" }}
...
{{ end }}
```

При создании шаблона можно воспользоваться подбором данных уведомлений для проверки работы шаблона.
![Программа визуализации статистики Grafana-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana-1.png)
Список будет доступен только из тех уведомлений которые активны в текущий момент времени.

### Настройка правил уведомления:

Настройка правил по которым выполняется отправка уведомлений задается в разделе `Notification policies` основное разделение выполняется по меткам установленных при создании уведомления в пункте 7.

![Программа визуализации статистики Grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BC%D0%B0%20%D0%B2%D0%B8%D0%B7%D1%83%D0%B0%D0%BB%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D0%B8%20%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B8%20Grafana.png)
`Matching labels` - выполняет отбор уведомлений для применения правил;
`Repeat interval` - переопределяет правила повторной отправки уведомлений;
`Mute timings` - отвечает за правила когда уведомления не отправляются, правила создаются в разделе `Notification policies` -> `Mute Timings`.

Пример правил настройки событий: [[Хобби/Конфиги/Пример настроенных уведомлений для Grafana\|Пример настроенных уведомлений для Grafana]]
![[Настроенные правила уведомления.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Grafana\|Grafana]]

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Статьи/Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana\|Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana]]
> - [[Статьи/Мониторинг proxmox с использованием InfluxDB v2 и Grafana\|Мониторинг proxmox с использованием InfluxDB v2 и Grafana]]
> - [[Статьи/Мониторинг роутеров openwrt в influxdb v2 и grafana\|Мониторинг роутеров openwrt в influxdb v2 и grafana]]
> - [[Статьи/Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа\|Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа]]

> [!info]-
> Примечание:: Визуализация мониторинга и статистики

