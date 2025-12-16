---
{"dg-publish":true,"dg-path":"Домашняя лаборатория/PC Proxmox.md","permalink":"/domashnyaya-laboratoriya/pc-proxmox/","updated":"2024-10-09T19:48:08+03:00"}
---

Основной сервер домашней лаборатории, на базе мини PC Beelink Mini S.
На данном сервере запущены все остальные виртуальные сервера и службы.
### Комплектация:
- Процессор: Intel Celeron N5095A 4 Core 2.00MHz
- SSD: Sata 3 500Gb
- HDD: 1Tb
- ОЗУ: DDR4 16Gb
### Установленные программы:
<div><table class="dataview table-view-table"><thead class="table-view-thead"><tr class="table-view-tr-header"><th class="table-view-th"><span>Название</span><span class="dataview small-text">4</span></th><th class="table-view-th"><span>Примечание</span></th></tr></thead><tbody class="table-view-tbody"><tr><td><span><a data-tooltip-position="top" aria-label="Заметки/Self-hosting. Nodeexpoeter.md" data-href="Заметки/Self-hosting. Nodeexpoeter.md" href="Заметки/Self-hosting. Nodeexpoeter.md" class="internal-link data-link-icon data-link-icon-after data-link-text" target="_blank" rel="noopener nofollow" data-link-tags="" data-link-type="note" data-link-path="Заметки/Self-hosting. Nodeexpoeter.md" style="--data-link-type: note; --data-link-path: Заметки/Self-hosting. Nodeexpoeter.md;">Nodeexpoeter</a></span></td><td><span>Сбор показателей системы</span></td></tr><tr><td><span><a data-tooltip-position="top" aria-label="Заметки/Self-hosting. Proxmox Datacenter Manager.md" data-href="Заметки/Self-hosting. Proxmox Datacenter Manager.md" href="Заметки/Self-hosting. Proxmox Datacenter Manager.md" class="internal-link data-link-icon data-link-icon-after data-link-text" target="_blank" rel="noopener nofollow" data-link-tags="" data-link-type="note" data-link-path="Заметки/Self-hosting. Proxmox Datacenter Manager.md" style="--data-link-type: note; --data-link-path: Заметки/Self-hosting. Proxmox Datacenter Manager.md;">Proxmox Datacenter Manager</a></span></td><td><span>Единый интерфейс управления нодами proxmox</span></td></tr><tr><td><span><a data-tooltip-position="top" aria-label="Заметки/Self-hosting. Proxmox Virtual Environment.md" data-href="Заметки/Self-hosting. Proxmox Virtual Environment.md" href="Заметки/Self-hosting. Proxmox Virtual Environment.md" class="internal-link data-link-icon data-link-icon-after data-link-text" target="_blank" rel="noopener nofollow" data-link-tags="" data-link-type="note" data-link-path="Заметки/Self-hosting. Proxmox Virtual Environment.md" style="--data-link-type: note; --data-link-path: Заметки/Self-hosting. Proxmox Virtual Environment.md;">Proxmox Virtual Environment</a></span></td><td><span>Система виртуализации</span></td></tr><tr><td><span><a data-tooltip-position="top" aria-label="Заметки/Self-hosting. Proxmox Backup Server (PBS).md" data-href="Заметки/Self-hosting. Proxmox Backup Server (PBS).md" href="Заметки/Self-hosting. Proxmox Backup Server (PBS).md" class="internal-link data-link-icon data-link-icon-after data-link-text" target="_blank" rel="noopener nofollow" data-link-tags="" data-link-type="note" data-link-path="Заметки/Self-hosting. Proxmox Backup Server (PBS).md" style="--data-link-type: note; --data-link-path: Заметки/Self-hosting. Proxmox Backup Server (PBS).md;">Proxmox Backup Server (PBS)</a></span></td><td><span>Система резервного копирования VM для Proxmox</span></td></tr></tbody></table></div>

> [!note]
> Для доступа к серверу не используются proxy, организован прямой доступ и автономное получение ssl сертификатов на самом сервере proxmox.

---
> [!urls]- Упоминания:
> - [[Хобби/Домашняя лаборатория/Структура домашней лаборатории\|Структура домашней лаборатории]]

> [!description]- Примечание
> Примечание:: Основной сервер домашней лаборатории