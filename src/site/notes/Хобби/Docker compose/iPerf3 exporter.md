---
{"dg-publish":true,"dg-path":"Docker compose/iPerf3 exporter.md","permalink":"/docker-compose/i-perf3-exporter/","tags":[""]}
---


```yaml
services:
  iperf3_exporter:
    container_name: iperf3-exporter
    image: ghcr.io/edgard/iperf3_exporter:latest
    ports:
      - '9579:9579'
```
