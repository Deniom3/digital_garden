---
{"dg-publish":true,"permalink":"/zametki/sbor-dannyh-o-doker-kontejnerah-dlya-monitoringa-s-ispolzovaniem-cadvisor/","created":"2024-09-10 23:59","updated":"2024-09-14T23:54:23+03:00"}
---

Сadvisor программа для сбора данных о контейнерах виртуализации (в основном докер но поддерживает и альтернативные варианты) которая может легко интегрироваться с Prometheus. Программа предоставляет полноценный веб интерфейс для просмотра всех данных мониторинга.

> [!danger]
> Разработкой занимается Google и образ размещен на их серверах. Из за санкций возможно потребуется доступ к сайту gcr.io через VPN.

Репозиторий: [GitHub - google/cadvisor: Analyzes resource usage and performance characteristics of running containers.](https://github.com/google/cadvisor)

Пример развертывания docker compose: 

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/monitoring-node/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Стек предназначен для запуска на удаленной машине для сбора данных мониторинга о хост системе и докер контейнерах:

```yaml
services:
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
    network_mode: host
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
      - /var/lib/docker/:/var/lib/docker:ro
      - /cgroup:/cgroup:ro
    restart: unless-stopped
    network_mode: host
networks: {}
```

> [!bug] Внимание
> Сadvisor запускается по умолчанию на порту 8080 который может быть занят в системе, для работы его может быть необходимо переопределить. В режиме bridge с указанием  `ports:  - 8082:8080`

</div></div>


Для визуализации (при развертывании стека с [[Заметки/Данные о системе с использованием node exporter\|Node exporter]]) можно использовать панели Grafana:
![[docker_containers.json]]
![[docker_host.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Программа визуализации статистики Grafana\|Grafana]]
> - [[Заметки/Данные о системе с использованием node exporter\|node exporter]]