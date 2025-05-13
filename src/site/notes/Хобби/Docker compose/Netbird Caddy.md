---
{"dg-publish":true,"dg-path":"Docker compose/Netbird Caddy.md","permalink":"/docker-compose/netbird-caddy/","tags":[""],"updated":"2025-05-13T22:00:16+03:00"}
---


```yaml
services:
  # UI dashboard
  dashboard:
    image: netbirdio/dashboard:latest
    restart: unless-stopped
    ports:
      - 8080:80
#      - 443:443
    environment:
      # Endpoints
      - NETBIRD_MGMT_API_ENDPOINT=https://netbird.<domein>.ru:443
      - NETBIRD_MGMT_GRPC_API_ENDPOINT=https://netbird.<domein>.ru:443
      # OIDC
      - AUTH_AUDIENCE=<secret_authentik>
      - AUTH_CLIENT_ID=<secret_authentik>
      - AUTH_CLIENT_SECRET=
      - AUTH_AUTHORITY=https://authentik.<domein>.ru/application/o/netbird/
      - USE_AUTH0=false
      - AUTH_SUPPORTED_SCOPES=openid profile email offline_access api
      - AUTH_REDIRECT_URI=
      - AUTH_SILENT_REDIRECT_URI=
      - NETBIRD_TOKEN_SOURCE=accessToken
      # SSL
      - NGINX_SSL_PORT=443
      # Letsencrypt
      - LETSENCRYPT_DOMAIN=
      - LETSENCRYPT_EMAIL=<email>
    volumes:
      - netbird-letsencrypt:/etc/letsencrypt/
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

  # Signal
  signal:
    image: netbirdio/signal:latest
    restart: unless-stopped
    volumes:
      - netbird-signal:/var/lib/netbird
    ports:
      - 10000:10000
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

  # Relay
  relay:
    image: netbirdio/relay:latest
    restart: unless-stopped
    environment:
    - NB_LOG_LEVEL=info
    - NB_LISTEN_ADDRESS=:33080
    - NB_EXPOSED_ADDRESS=netbird.<domein>.ru:33080
    # todo: change to a secure secret
    - NB_AUTH_SECRET=<secret>
    ports:
      - 33080:33080
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

  # Management
  management:
    image: netbirdio/management:latest
    restart: unless-stopped
    depends_on:
      - dashboard
    volumes:
      - netbird-mgmt:/var/lib/netbird
      - netbird-letsencrypt:/etc/letsencrypt:ro
      - ./management.json:/etc/netbird/management.json
    ports:
      - 33073:33073 #API port
  #    # command for Let's Encrypt validation without dashboard container
  #    command: ["--letsencrypt-domain", "", "--log-file", "console"]
    command: [
      "--port", "33073",
      "--log-file", "console",
      "--log-level", "info",
      "--disable-anonymous-metrics=false",
      "--single-account-mode-domain=spb.<domein>.ru",
      "--dns-domain=netbird.selfstil"
      ]
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"
    environment:
      - NETBIRD_STORE_ENGINE_POSTGRES_DSN=
      - NETBIRD_STORE_ENGINE_MYSQL_DSN=
      
  # Coturn
  coturn:
    image: coturn/coturn:latest
    restart: unless-stopped
    volumes:
      - ./turnserver.conf:/etc/turnserver.conf:ro
    network_mode: host
    command:
      - -c /etc/turnserver.conf
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"

volumes:
  netbird-mgmt:
  netbird-signal:
  netbird-letsencrypt:

```
