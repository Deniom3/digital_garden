---
{"dg-publish":true,"permalink":"/zametki/self-hosting-metabase/","created":"2024-10-05 17:04","updated":"2024-10-05T17:08:10+03:00"}
---

Система гибкой визуализации на основе баз данных с легкой и гибкой настройкой.

Репозиторий: https://github.com/metabase/metabase

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/crowd-sec/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
Предназначен для использования в связке с [[Заметки/Self-hosting. Traefik\|Traefik]]

Для подключения к Traefik bouncer необходимо сгенерировать токен ключ командой:
```shell
docker exec crowdsec cscli bouncers add bouncer-traefik
```

```yaml
services:
  crowdsec:
    image: crowdsecurity/crowdsec:latest
    container_name: crowdsec
    environment:
      GID: "${GID-1000}"
      COLLECTIONS: "crowdsecurity/linux crowdsecurity/traefik crowdsecurity/appsec-virtual-patching crowdsecurity/appsec-generic-rules firix/authentik Dominic-Wagner/vaultwarden gauth-fr/immich aidalinfo/couchdb LePresidente/gitea"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./acquis.yaml:/etc/crowdsec/acquis.yaml
      - ./db:/var/lib/crowdsec/data/
      - ./config:/etc/crowdsec/
      - /home/deniom/docker/traefik/logs:/var/log/traefik/:ro
    ports:
      - 6060:6060
      - 8081:8080
    networks:
      - proxy
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped

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

networks:
  proxy:
    external: true
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Визуализация баз данных в отчеты
