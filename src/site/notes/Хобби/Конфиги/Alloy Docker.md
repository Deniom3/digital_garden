---
{"dg-publish":true,"dg-path":"Конфиги/Alloy Docker.md","permalink":"/konfigi/alloy-docker/","tags":[""],"updated":"2024-10-10T00:30:15+03:00"}
---


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
