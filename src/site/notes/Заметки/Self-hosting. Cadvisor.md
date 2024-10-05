---
{"dg-publish":true,"permalink":"/zametki/self-hosting-cadvisor/","created":"2024-09-10 23:59","updated":"2024-10-01T20:40:00+03:00"}
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
> Сadvisor запускается по умолчанию на порту 8080 который может быть занят в системе, для работы его может быть необходимо переопределить. В режиме bridge с указанием  `ports:  - 8090:8080`

</div></div>


## Настройка Prometheus

Добавить в конфигурационный файл Prometheus новую задачу сбора данных:
```yaml
  - job_name: 'nodeexporter'
    scrape_interval: 5s
    static_configs:
      - targets: ['monitoring.local:9100', 'gateway.local:9100', 'main.local:9100', 'nextcloud.local:9100', 'media.local:9100', 'immich.local:9100', 'armbian.local:9100']

  - job_name: 'cadvisor'
    scrape_interval: 5s
    static_configs:
      - targets: ['monitoring.local:8090', 'gateway.local:8090', 'main.local:8090', 'nextcloud.local:8090', 'media.local:8090', 'immich.local:8090', 'armbian.local:8090']

```

## Мониторинг в Grafana

Для визуализации (при развертывании стека с [[Заметки/Self-hosting. Nodeexpoeter\|Node exporter]]) можно использовать панели Grafana:
![[docker_containers.json]]
![[docker_host.json]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Заметки/Self-hosting. Nodeexpoeter\|node exporter]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]
> - [[Хобби/Домашняя лаборатория/Сервер Media\|Сервер Media]]
> - [[Хобби/Домашняя лаборатория/Сервер Immich\|Сервер Immich]]
> - [[Хобби/Домашняя лаборатория/Сервер Nextcloud\|Сервер Nextcloud]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]
> - [[Хобби/Домашняя лаборатория/Сервер Armbian\|Сервер Armbian]]

> [!info]-
> Примечание:: Сбор данных мониторинга docker контейнеров
