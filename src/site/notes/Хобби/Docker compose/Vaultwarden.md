---
{"dg-publish":true,"dg-path":"Docker compose/Vaultwarden.md","permalink":"/docker-compose/vaultwarden/","updated":"2024-10-06T01:05:14+03:00"}
---


```yaml
services:
  vaultwarden:
    image: vaultwarden/server:latest
    container_name: vaultwarden
    network_mode: bridge
    environment:
      - TZ=Europe/Moscow
      - ADMIN_TOKEN=${VAULTWARDEN_ADMIN_TOKEN}
      - PUSH_ENABLED=true
      - PUSH_IDENTITY_URI=https://identity.bitwarden.eu
      - PUSH_INSTALLATION_ID=<PUSH_INSTALLATION_ID>
      - PUSH_INSTALLATION_KEY=<PUSH_INSTALLATION_KEY>
      - PUSH_RELAY_URI=https://push.bitwarden.eu
    volumes:
      - ./data:/data
    ports:
      - 10380:80
    restart: unless-stopped
```

