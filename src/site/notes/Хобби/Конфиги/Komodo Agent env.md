---
{"dg-publish":true,"dg-path":"Конфиги/Komodo Agent env.md","permalink":"/konfigi/komodo-agent-env/","tags":[""],"updated":"2025-11-26T02:02:13+03:00"}
---


```shell
# Версия образа Komodo Periphery
COMPOSE_KOMODO_IMAGE_TAG=latest

# Имя сервера (любой понятный тебе идентификатор)
SERVER_NAME=monitoring

# первый UID это пользовательй - определить командой id
# второй DOCKER_GID группы docker - определить командой getent group docker
UID=1000
DOCKER_GID=999

# Каталог с данными docker-compose
PERIPHERY_DATA_DIR=/home/deniom/docker

# SSL-папка для Periphery
PERIPHERY_SSL_DIR=/home/deniom/docker/komodo-agent/ssl

# Корневой каталог перефирии (может быть изменён)
PERIPHERY_ROOT_DIRECTORY=/etc/komodo

# Ключ подключения из свойства KOMODO_PASSKEY komodo server
PERIPHERY_PASSKEYS=<ключ>

# Включить SSL
PERIPHERY_SSL_ENABLED=true
# Разрешить терминалы/доступ к shell
PERIPHERY_DISABLE_TERMINALS=false
# Какие точки монтирования учитывать при подсчёте дисков
PERIPHERY_INCLUDE_DISK_MOUNTS=/etc/hostname

```

Параметры:
	`SERVER_NAME` - имя сервера в среде komodo
	`UID` - id пользователя от чьего имени будут создаваться файлы
	`DOCKER_GID` - id группы docker для запуска под ограниченными правами
	`PERIPHERY_DATA_DIR` - основная папка хранения docker compose файлов
	`PERIPHERY_SSL_DIR` - папка хранения самовыпущенных сертификатов ssl, необходимо создавать под пользователем из UID до первого запуска контейнера, иначе будет ошибка.
	`PERIPHERY_PASSKEYS` - ключ авторизации между сервером и агентом, смотри параметр `KOMODO_PASSKEY`

---
> [!urls]- Упоминания:
> [[Заметки/Self-hosting. Komodo\|Self-hosting. Komodo]]

> [!description]- Примечание
> Примечание::  Параметры env для komodo agent
