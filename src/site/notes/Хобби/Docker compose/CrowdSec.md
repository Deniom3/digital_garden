---
{"dg-publish":true,"dg-path":"Docker compose/CrowdSec.md","permalink":"/docker-compose/crowd-sec/","updated":"2024-10-09T18:14:11+03:00"}
---


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

networks:
  proxy:
    external: true
```

