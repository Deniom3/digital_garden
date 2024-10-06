---
{"dg-publish":true,"dg-path":"Docker compose/MetaBase.md","permalink":"/docker-compose/meta-base/","tags":[""],"updated":"2024-10-06T23:47:22+03:00"}
---


```yaml
services:
  dashboard:
    image: metabase/metabase:latest
    container_name: crowdsec-dashboard
    restart: always
    mem_limit: 512m
    ports:
      - 3000:3000
    environment:
      MB_DB_FILE: /data/metabase.db
      MGID: "${GID-1000}"
      JAVA_OPTS: "-Xmx256m"
    depends_on:
      - 'crowdsec'
    volumes:
      - ./db:/metabase-data/
      - ./metabase:/data/
    networks:
      - proxy
```
