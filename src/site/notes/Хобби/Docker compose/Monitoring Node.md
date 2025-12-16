---
{"dg-publish":true,"dg-path":"Docker compose/Monitoring Node.md","permalink":"/docker-compose/monitoring-node/","updated":"2025-12-15T21:45:19+03:00"}
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
    image: ghcr.io/google/cadvisor:latest
    container_name: cadvisor
    privileged: true
    devices:
      - /dev/kmsg:/dev/kmsg
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /:/rootfs:ro
      - /var/run:/var/run:ro
      - /sys:/sys:ro
      - /var/lib/docker/:/var/lib/docker:ro
      - /dev/disk/:/dev/disk:ro
      - /cgroup:/cgroup:ro
      - /etc/machine-id:/etc/machine-id:ro
    restart: unless-stopped
    command:
	  - '-port=8090'
    network_mode: host

```

> [!bug] Внимание
> Сadvisor запускается по умолчанию на порту 8080 который может быть занят в системе, для работы его может быть необходимо переопределить. В режиме bridge с указанием  `ports:  - 8090:8080` или добавить блок изменения порта `command: - '-port=8090'`