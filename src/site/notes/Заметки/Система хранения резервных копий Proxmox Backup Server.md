---
{"dg-publish":true,"permalink":"/zametki/sistema-hraneniya-rezervnyh-kopij-proxmox-backup-server/","created":"2024-07-03 20:03","updated":"2024-09-03T16:36:07+03:00"}
---

Это система организации хранения резервных копий виртуальных машин и LXC контейнеров для системы виртуализации [[Заметки/Система виртуализации Proxmox Virtual Environment\|proxmox]]. Позволяет удобно и компактно хранить резервные копии в виде чанков в которых сохраняется изменение данных а не создается полная копия.

Может быть установлен несколькими методами: как отдельный пакет debian рядом с сервером [[Заметки/Система виртуализации Proxmox Virtual Environment\|proxmox]], на чистой системе или как докер контейнер.

Есть несколько репозиториев для обновления, по подписке и открытые.

Официальная документация: [Proxmox Backup Server - Open-Source Enterprise Backup Solution](https://www.proxmox.com/en/proxmox-backup-server/overview)

Докер вариант (неофициальный вариант установки): [GitHub - ayufan/pve-backup-server-dockerfiles: Unofficial, and unmaintained build of proxmox-backup-server](https://github.com/ayufan/pve-backup-server-dockerfiles) 

---
#### Упоминания:
- [[Статьи/Запуск Proxmox Backup Server (PBS) на Windows или NAS. Организация синхронизации бэкапов между PBS\|Запуск Proxmox Backup Server (PBS) на Windows или NAS. Организация синхронизации бэкапов между PBS]]
- [[Статьи/Резервное копирование с помощью Proxmox Backup Server (PBS). Делаем правильные бэкапы\|Резервное копирование с помощью Proxmox Backup Server (PBS). Делаем правильные бэкапы]]
- [[Хобби/Docker compose/Proxmox-backup-server\|Докер файл для запуска в контейнере]]