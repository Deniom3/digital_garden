---
{"dg-publish":true,"permalink":"/zametki/self-hosting-proxmox-backup-server-pbs/","created":"2024-07-03 20:03","updated":"2024-09-24T22:55:52+03:00"}
---

Это система организации хранения резервных копий виртуальных машин и LXC контейнеров для системы виртуализации [[Заметки/Self-hosting. Proxmox Virtual Environment\|proxmox]]. Позволяет удобно и компактно хранить резервные копии в виде чанков в которых сохраняется изменение данных а не создается полная копия.

Может быть установлен несколькими методами: как отдельный пакет debian рядом с сервером [[Заметки/Self-hosting. Proxmox Virtual Environment\|proxmox]], на чистой системе или как докер контейнер.

Есть несколько репозиториев для обновления, по подписке и открытые.

Официальная документация: [Proxmox Backup Server - Open-Source Enterprise Backup Solution](https://www.proxmox.com/en/proxmox-backup-server/overview)

Докер вариант (неофициальный вариант установки): [GitHub - ayufan/pve-backup-server-dockerfiles: Unofficial, and unmaintained build of proxmox-backup-server](https://github.com/ayufan/pve-backup-server-dockerfiles) 

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Докер файл для запуска в контейнере

</div>



Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
Подробно разобрано в [[Статьи/Запуск Proxmox Backup Server (PBS) на Windows или NAS. Организация синхронизации бэкапов между PBS\|статье]]

```yaml
version: '2.1'

services:
  pbs:
    image: ayufan/proxmox-backup-server:latest
    container_name: pbs
    hostname: deniom-pc
    mem_limit: 128Mb
    environment:
      - TZ=Europe/Moscow
    volumes:
      - D:\Proxmox Backup Server\etc:/etc/proxmox-backup
      - D:\Proxmox Backup Server\logs:/var/log/proxmox-backup
      - D:\Proxmox Backup Server\lib:/var/lib/proxmox-backup
      - D:\Proxmox Backup Server\backups:/backups
    ports:
      - 8007:8007
    tmpfs:
      - /run
    restart: unless-stopped
    stop_signal: SIGHUP
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Статьи/Запуск Proxmox Backup Server (PBS) на Windows или NAS. Организация синхронизации бэкапов между PBS\|Запуск Proxmox Backup Server (PBS) на Windows или NAS. Организация синхронизации бэкапов между PBS]]
> - [[Статьи/Резервное копирование с помощью Proxmox Backup Server (PBS). Делаем правильные бэкапы\|Резервное копирование с помощью Proxmox Backup Server (PBS). Делаем правильные бэкапы]]

> [!info]-
> Примечание:: Система резервного копирования VM для Proxmox