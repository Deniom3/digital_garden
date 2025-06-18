---
{"dg-publish":true,"permalink":"/zametki/self-hosting-grafana-loki/","created":"2024-09-14 02:48","updated":"2025-06-10T01:58:00+03:00"}
---

Позволяет выполнять сбор данных логов для дальнейшего анализа с различных систем.

Официальный сайт: [Grafana Loki OSS | Log aggregation system](https://grafana.com/oss/loki/)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/loki/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  loki:
    image: grafana/loki:latest
    container_name: loki
    ports:
      - 3100:3100
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./loki-config.yml:/etc/loki/loki-config.yaml
      - loki_data:/loki
    networks:
      - monitoring
    labels:
      org.label-schema.group: monitoring
    restart: unless-stopped
    
volumes:
  loki_data:
networks:
  monitoring:
    name: monitoring
    external: true
```


</div></div>


Файл конфигурации: 

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/loki/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Loki

</div>




```shell
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9095

ingester:
  wal:
    dir: "/tmp/wal"
  lifecycler:
    address: 127.0.0.1
    ring:
      kvstore:
        store: inmemory
      replication_factor: 1
  chunk_idle_period: 5m
  chunk_retain_period: 30s
  chunk_target_size: 1536000
  chunk_block_size: 262144

schema_config:
  configs:
    - from: 2023-01-01
      store: tsdb
      object_store: filesystem
      schema: v13
      index:
        prefix: index_
        period: 24h

storage_config:
  tsdb_shipper:
    active_index_directory: /loki/index
    cache_location: /loki/index_cache
    cache_ttl: 24h

compactor:
  working_directory: /loki/compactor

limits_config:
  reject_old_samples: true
  reject_old_samples_max_age: 168h

chunk_store_config:

table_manager:
  retention_deletes_enabled: true
  retention_period: 168h

ruler:
  storage:
    type: local
    local:
      directory: /loki/rules
  rule_path: /loki/rules
  ring:
    kvstore:
      store: inmemory
  enable_api: true
```

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Grafana Loki\|Self-hosting. Grafana Loki]]

> [!description]- Примечание
> Примечание::  Сбор логов для Grafana


</div></div>


Настройка:
- [[Заметки/Сбор логов с роутеров Keenetic и OpenWrt для Grafana\|Сбор логов с роутеров Keenetic и OpenWrt для Grafana]]
- [[Заметки/Сбор логов linux и proxmox для Grafana\|Сбор логов linux и proxmox для Grafana]]
- [[Заметки/Сбор логов docker контейнеров для Grafana\|Сбор логов docker контейнеров для Grafana]]

---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Заметки/Self-hosting. Grafana\|Grafana]]
> - [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]
> - [[Заметки/Self-hosting. Alloy\|Alloy]]

> [!description]- Примечание
> Примечание:: Сбор и обработка логов

> [!todo]-
> - [-] Настроить Loki для сборов логов Crowdsec ➕ 2024-10-09 ❌ 2024-10-09
> - [x] Настроить Loki для сборов логов Keenetik ✅ 2024-10-08
> - [x] Настроить Loki для сборов логов docker ➕ 2024-09-14 ✅ 2024-10-09
> - [x] Изучить настройки Loki для сборов логов ➕ 2024-09-14 ✅ 2024-10-09
> - [x] Настроить Loki для сборов логов OpenWRT ➕ 2024-09-14 ✅ 2024-10-10
> - [x] Настроить Loki для сборов proxmox ➕ 2024-09-14 ✅ 2024-10-10
