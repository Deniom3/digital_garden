---
{"dg-publish":true,"permalink":"/zametki/dannye-o-sisteme-s-ispolzovaniem-node-exporter/","created":"2024-09-11 00:10","updated":"2024-09-14T23:53:32+03:00"}
---

Программа для сбора данных мониторинга хост системы с публикацией в web в формате prometheus. Является частью стека мониторинга prometheus.

Репозиторий: [GitHub - prometheus/node\_exporter: Exporter for machine metrics](https://github.com/prometheus/node_exporter)

Пример docekr compose:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/monitoring-node/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
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


Вариант установки как служба systemctl для proxmox: [[Хобби/Команды и настройки/Установка node exporter как служба\|инструкция]]

Для визуализации можно использовать панель Grafana:
![[Мониторинг систем.json]]

---
> [!urls]- Упоминания:
> - [[Заметки/Программа визуализации статистики Grafana\|Grafana]]
> - [[Заметки/Система мониторинга Prometheus\|Prometheus]]