---
{"dg-publish":true,"dg-path":"Конфиги/Crowdsek acquis.md","permalink":"/konfigi/crowdsek-acquis/","tags":[""],"updated":"2024-10-06T00:25:39+03:00"}
---


```shell
filenames:
  - /var/log/traefik/*
labels:
  type: traefik

---
listen_addr: 0.0.0.0:7422
appsec_config: crowdsecurity/virtual-patching
name: Traefik
source: appsec
labels:
  type: appsec

---
source: docker
container_name:
 - authentik-server-1
labels:
  type: authentik
docker_host: tcp://192.168.0.131:2375

---
source: docker
container_name:
  - traefik
labels:
  type: traefik
docker_host: tcp://192.168.0.131:2375

---
source: docker
container_name:
  - vaultwarden
labels:
  type: Vaultwarden
docker_host: tcp://192.168.0.132:2375

---
source: docker
container_name:
  - immich_server
labels:
  type: immich
docker_host: tcp://192.168.0.136:2375

---
source: docker
container_name:
  - couchdb
labels:
  type: couchdb
docker_host: tcp://192.168.0.132:2375

---
source: docker
container_name:
  - gitea
labels:
  type: gitea
docker_host: tcp://192.168.0.132:2375
```
