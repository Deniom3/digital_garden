---
{"dg-publish":true,"permalink":"/zametki/self-hosting-dockge/","created":"2024-09-08 02:23","updated":"2024-09-24T23:10:43+03:00"}
---

Удобный менеджер для запуска и управления docker compose из веб интерфейса. Имеет функции агента с возможностью подключения одной панели к другой и возможность преобразования команд docker run.

Есть возможность обновления контейнеров.

> [!bug]
> Запуск новых стеков выполняется под root пользователем из за чего созданные файлы тоже имеет права root.

Репозиторий: [GitHub - louislam/dockge: A fancy, easy-to-use and reactive self-hosted docker compose.yaml stack-oriented manager](https://github.com/louislam/dockge)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">




Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
```yaml
services:
  dockge:
    image: louislam/dockge:latest
    restart: unless-stopped
    container_name: dockge
    ports:
      - 5001:5001
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./data:/app/data
      # Stacks Directory
      # ⚠️ READ IT CAREFULLY. If you did it wrong, your data could end up writing into a WRONG PATH.
      # ⚠️ 1. FULL path only. No relative path (MUST)
      # ⚠️ 2. Left Stacks Path === Right Stacks Path (MUST)
      - /home/deniom/docker:/home/deniom/docker
    environment:
      # Tell Dockge where to find the stacks
      - DOCKGE_STACKS_DIR=/home/deniom/docker
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Управление docker compose стеками через web
