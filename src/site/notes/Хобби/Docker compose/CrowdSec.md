---
{"dg-publish":true,"dg-path":"Docker compose/CrowdSec.md","permalink":"/docker-compose/crowd-sec/","updated":"2024-09-24T22:58:39+03:00"}
---

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
      COLLECTIONS: "crowdsecurity/linux crowdsecurity/traefik"
    volumes:
      - ./acquis.yaml:/etc/crowdsec/acquis.yaml
      - ./db:/var/lib/crowdsec/data/
      - ./config:/etc/crowdsec/
      - /home/deniom/docker/traefik/logs:/var/log/traefik/:ro
    ports:
      - 6060:6060
    networks:
      - proxy
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped

  bouncer-traefik:
    image: docker.io/fbonalair/traefik-crowdsec-bouncer:latest
    container_name: bouncer-traefik
    environment:
      CROWDSEC_BOUNCER_API_KEY: <ApiKey>
      CROWDSEC_AGENT_HOST: crowdsec:8080
    networks:
      - proxy
    depends_on:
      - crowdsec
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true

networks:
  proxy:
    external: true
```

