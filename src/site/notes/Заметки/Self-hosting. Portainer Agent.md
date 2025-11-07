---
{"dg-publish":true,"permalink":"/zametki/self-hosting-portainer-agent/","tags":[""],"created":"2025-11-07 10:23","updated":"2025-11-07T10:24:58+03:00"}
---

Агент управления docker контейнерами с использованием [[Заметки/Self-hosting. Portainer\|Portainer]]

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/portainer-agent/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  agent:
    ports:
      - 9001:9001
    container_name: portainer_agent
    restart: always
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/lib/docker/volumes:/var/lib/docker/volumes
      - /:/host
    image: portainer/agent:latest
```


</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 > - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]

> [!description]- Примечание
> Примечание:: Агент для управления через portainer
