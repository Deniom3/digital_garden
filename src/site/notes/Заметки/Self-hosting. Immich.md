---
{"dg-publish":true,"permalink":"/zametki/self-hosting-immich/","created":"2024-07-05 00:37","updated":"2025-06-10T01:54:52+03:00"}
---

Программа для хранения фотографий и их обработки. Имеет возможно преобразования фотографий и видео, распознание лиц и разметка по геолокации.
По дизайну очень похожа на google фото.

Репозиторий: [GitHub - immich-app/immich: High performance self-hosted photo and video management solution.](https://github.com/immich-app/immich)

Документация: [Introduction | Immich](https://immich.app/docs/overview/introduction)

> [!bug]
> В последних версиях проксирование авторизации через aithentik приводит к проблемам авторизации для мобильных приложений. Можно использовать только authentik для внешней авторизации без прослойки в traefik.

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
#
# WARNING: Make sure to use the docker-compose.yml of the current release:
#
# https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml
#
# The compose file on main may not be compatible with the latest release.
#

name: immich

services:
  immich-server:
    container_name: immich_server
    image: ghcr.io/immich-app/immich-server:release
    # extends:
    #   file: hwaccel.transcoding.yml
    #   service: cpu # set to one of [nvenc, quicksync, rkmpp, vaapi, vaapi-wsl] for accelerated transcoding
    volumes:
      - ${UPLOAD_LOCATION}:/usr/src/app/upload
      - /etc/localtime:/etc/localtime:ro
    env_file:
      - stack.env
    environment:
      - PUID=1000
      - PGID=1000
  
    ports:
      - 2283:3001
    depends_on:
      - redis
      - database
    restart: always

  immich-machine-learning:
    container_name: immich_machine_learning
    # For hardware acceleration, add one of -[armnn, cuda, openvino] to the image tag.
    # Example tag: ${IMMICH_VERSION:-release}-cuda
    image: ghcr.io/immich-app/immich-machine-learning:release
    # extends: # uncomment this section for hardware acceleration - see https://immich.app/docs/features/ml-hardware-acceleration
    #   file: hwaccel.ml.yml
    #   service: cpu # set to one of [armnn, cuda, openvino, openvino-wsl] for accelerated inference - use the `-wsl` version for WSL2 where applicable
    volumes:
      - model-cache:/cache
    env_file:
      - stack.env
    restart: always

  redis:
    container_name: immich_redis
    image: docker.io/redis:6.2-alpine@sha256:d6c2911ac51b289db208767581a5d154544f2b2fe4914ea5056443f62dc6e900
    labels:
      - "com.centurylinklabs.watchtower.enable=false"
    healthcheck:
      test: redis-cli ping || exit 1
    restart: always

  database:
    container_name: immich_postgres
    image: docker.io/tensorchord/pgvecto-rs:pg14-v0.2.0@sha256:90724186f0a3517cf6914295b5ab410db9ce23190a2d9d0b9dd6463e3fa298f0
    labels:
      - "com.centurylinklabs.watchtower.enable=false"
    environment:
      POSTGRES_PASSWORD: ${DB_PASSWORD}
      POSTGRES_USER: ${DB_USERNAME}
      POSTGRES_DB: ${DB_DATABASE_NAME}
      POSTGRES_INITDB_ARGS: '--data-checksums'
    volumes:
      - ${DB_DATA_LOCATION}:/var/lib/postgresql/data
    healthcheck:
      test: pg_isready --dbname='${DB_DATABASE_NAME}' || exit 1; Chksum="$(psql --dbname='${DB_DATABASE_NAME}' --username='${DB_USERNAME}' --tuples-only --no-align --command='SELECT COALESCE(SUM(checksum_failures), 0) FROM pg_stat_database')"; echo "checksum failure count is $Chksum"; [ "$Chksum" = '0' ] || exit 1
      interval: 5m
      start_interval: 30s
      start_period: 5m
    command: ["postgres", "-c" ,"shared_preload_libraries=vectors.so", "-c", 'search_path="$user", public, vectors', "-c", "logging_collector=on", "-c", "max_wal_size=2GB", "-c", "shared_buffers=512MB", "-c", "wal_compression=on"]
    restart: always

volumes:
  model-cache:

```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Immich\|Сервер Immich]]

> [!description]- Примечание
> Примечание:: Синхронизация, хранение и обработка фотографий