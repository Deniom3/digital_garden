---
{"dg-publish":true,"dg-path":"Docker compose/Remote Server Monitoring.md","permalink":"/docker-compose/remote-server-monitoring/","tags":[""],"updated":"2025-05-20T17:47:15+03:00"}
---


```yml
services:
  # Мониторинг хоста
  nodeexporter:
    image: prom/node-exporter:latest
    container_name: nodeexporter
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:ro
    command:
      - --path.procfs=/host/proc
      - --path.rootfs=/rootfs
      - --path.sysfs=/host/sys
      - --collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($|/)
    restart: unless-stopped
    expose:
      - 9100
    networks:
      monitor-net:
        ipv4_address: 10.2.1.6
    labels:
      org.label-schema.group: monitoring
  # Мониторинг контейнеров
  cadvisor:
    image: gcr.io/cadvisor/cadvisor:latest
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
      - /cgroup:/cgroup:ro
    restart: unless-stopped
    expose:
      - 8080
    networks:
      monitor-net:
        ipv4_address: 10.2.1.2
    labels:
      org.label-schema.group: monitoring
  # Для Alloy и Uptime Kuma
  dockerproxy:
    image: ghcr.io/tecnativa/docker-socket-proxy:latest
    container_name: dockerproxy
    environment:
      - INFO=1
      - CONTAINERS=1 
      - SERVICES=1 
      - TASKS=1 
      - POST=1 # Для обновления должнно быть 1 и право на запись в сокет - /var/run/docker.sock:/var/run/docker.sock
      - NETWORKS=1 
      - VERSION=1 # wud
      - IMAGES=1 # wud
    expose:
      - 2375
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped
    networks:
      monitor-net:
        ipv4_address: 10.2.1.3
    labels:
      org.label-schema.group: monitoring
  # Отслеживания доступности сервисов
  uptime-kuma:
    image: louislam/uptime-kuma:latest
    container_name: uptime-kuma
    volumes:
      - ./uptime-kuma:/app/data
      - /etc/localtime:/etc/localtime:ro
    expose:
      - 3001
    restart: unless-stopped
    networks:
      monitor-net:
        ipv4_address: 10.2.1.4
    labels:
      org.label-schema.group: monitoring
  # Мониторинг vless
  xray-checker:
    image: kutovoys/xray-checker
    container_name: xray-checker
    restart: always
    environment:
      - SUBSCRIPTION_URL=
      - PROXY_CHECK_METHOD=status
      - PROXY_STATUS_CHECK_URL=http://cp.cloudflare.com/generate_204
    expose:
      - 2112
    networks:
      monitor-net:
        ipv4_address: 10.2.1.5
    labels:
      org.label-schema.group: monitoring
  # Прокси
  caddy:
    image: caddy:latest
    container_name: caddy-monitoring
    ports:
      - "3001:3001" # uptime-kuma
      - "8090:8090" # cadvisor
      - "9100:9100" # nodeexporter
      - "2375:2375" # dockerproxy
      - "2112:2112" # xray-checker
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./Caddyfile:/etc/caddy/Caddyfile 
    environment:
      - ADMIN_USER=${ADMIN_USER:-admin}
      - ADMIN_PASSWORD_HASH=${ADMIN_PASSWORD_HASH:-$2a$14$1l.IozJx7xQRVmlkEQ32OeEEfP5mRxTpbDTCTcXRqn19gXD8YK1pO}
    restart: unless-stopped
    labels:
      org.label-schema.group: monitoring
    networks:
      monitor-net:
        ipv4_address: 10.2.1.100

networks:
  monitor-net:
    name: monitoring
    ipam:
      driver: default
      config:
        - subnet: 10.2.1.0/24
```
