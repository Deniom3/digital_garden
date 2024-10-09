---
{"dg-publish":true,"permalink":"/zametki/self-hosting-portainer/","created":"2024-10-01 21:07","updated":"2024-10-09T19:53:30+03:00"}
---

Многофункциональная система управления docker контейнерами. Система позволяет работать в варианте клиент серверной архитектуры, на машину клиент устанавливается специальный агент.

В программе возможно выполнять полный цикл работы с docker от создания до удаления. Так же имеется удобный интерфейс для контроля образов и просмотра хранилищ и логов контейнеров.

Репозиторий: https://github.com/portainer/portainer

Документация: [Install Portainer CE | Portainer Documentation](https://docs.portainer.io/start/install-ce)

> [!attention]
> Существует в двух версиях, CE предоставляется бесплатно

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
version: '3.9'
services:
  portainer:
    container_name: portainer
    image: portainer/portainer-ce:latest
    ports:
      - 8000:8000
      - 9000:9000
      - 9443:9443
    restart: unless-stopped
    volumes:
      - /DATA/AppData/portainer:/data
      - /var/run/docker.sock:/var/run/docker.sock
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Интерфейс управления docker контейнерами
