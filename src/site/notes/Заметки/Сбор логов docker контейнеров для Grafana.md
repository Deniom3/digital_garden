---
{"dg-publish":true,"permalink":"/zametki/sbor-logov-docker-kontejnerov-dlya-grafana/","created":"2024-10-10 00:27","updated":"2024-10-13T11:08:59+03:00"}
---

Сбор логов с docker контейнеров выполняется с использованием [[Заметки/Self-hosting. Alloy\|Alloy]] и [[Заметки/Self-hosting. Dockerproxy\|Dockerproxy]]  с дальнейшей обработкой и передачей в [[Заметки/Self-hosting. Grafana Loki\|Loki]]. 

### Настройка сбора логов через Alloy
Общий файл конфигурации:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/alloy-docker/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```shell
loki.relabel "docker" {
  forward_to = []

  rule {
    source_labels = ["__meta_docker_container_name"]
    regex         = "/(.*)"
    target_label  = "container_name"
  }

  rule {
    source_labels = ["__meta_docker_container_name"]
    regex         = "/(portainer_agent|loki|alloy)"
    action        = "drop"
  }

  rule {
    source_labels = ["__meta_docker_container_label_com_docker_compose_project"]
    regex         = "(monitoring|dockerproxy|dockge)"
    action        = "drop"
  } 
}

discovery.docker "gateway" {
  host = "tcp://192.168.0.131:2375"

}

loki.source.docker "gateway" {
  host = "tcp://192.168.0.131:2375"
  targets    = discovery.docker.gateway.targets
  labels     = {"app" = "docker", host = "gateway.local" }
  
  relabel_rules = loki.relabel.docker.rules
  forward_to = [loki.write.local.receiver]
}

loki.write "local" {
    endpoint {
        url = "http://loki:3100/loki/api/v1/push"
    }
    external_labels = {}
}
```

---
> [!urls]- Упоминания:
> [[Заметки/Сбор логов docker контейнеров для Grafana\|Сбор логов docker контейнеров для Grafana]]

> [!description]- Примечание
> Примечание::  Настройки Alloy для сбора логов докера


</div></div>


Документация:
- [discovery.docker | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/discovery/discovery.docker/)
- [loki.relabel | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/loki/loki.relabel/)
- [loki.write | Grafana Alloy documentation](https://grafana.com/docs/alloy/latest/reference/components/loki/loki.write/)

### Фильтрация и обработка логов
#### Установка метки имени контейнера

Для задания в метках имени контейнера необходим блок в секции `loki.relabe`:
```
rule { 
	source_labels = ["__meta_docker_container_name"] 
	regex = "/(.*)" 
	target_label = "container_name" 
	}
```

#### Отбор по имени контейнера

Для исключения контейнера по имени необходим блок в секции `loki.relabe`:
```
rule { 
	source_labels = ["__meta_docker_container_name"]
	regex = "/(<name1>|<name2>)" 
	action = "drop"
	}
```
где `<name1>` - имя исключаемого контейнера

#### Отбор по имени docker compose стека

Для исключения контейнера по имени необходим блок в секции `loki.relabe`:
```
rule { 
	source_labels = ["__meta_docker_container_label_com_docker_compose_project"]
	regex = "(<name1>|<name2>)" 
	action = "drop"
	}
```
где `<name1>` - имя исключаемого контейнера

### Настройка хостов docker

Для сбора данных с хостов используется [[Заметки/Self-hosting. Dockerproxy\|Dockerproxy]] для работы необходимо в environment в docker compose обязательно включить параметр `NETWORKS`:
```
...
	environment:
      - INFO=1 # Для работы Crowdsek
      - CONTAINERS=1
      - SERVICES=1
      - TASKS=1
      - POST=0 # Disallow any POST operations (effectively read-only)
      - NETWORKS=1 # Alloy
...
```

На этом все настройки на хосте для мониторинга закончены.

### Панель мониторинга Grafana

Мониторинг логов добавлен в существующую [[Заметки/Self-hosting. Cadvisor#Мониторинг в Grafana\|панель]].

![Сбор логов docker контенейров для Grafana.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%A1%D0%B1%D0%BE%D1%80%20%D0%BB%D0%BE%D0%B3%D0%BE%D0%B2%20docker%20%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%BD%D0%B5%D0%B9%D1%80%D0%BE%D0%B2%20%D0%B4%D0%BB%D1%8F%20Grafana.png)

Шаблон панели:
![[Docker Containers and Logs.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Заметки/Self-hosting. Grafana Loki\|Grafana Loki]]
> - [[Заметки/Self-hosting. Alloy\|Alloy]]