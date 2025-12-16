---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка времени очистки PBS.md","permalink":"/komandy-i-nastrojki/nastrojka-vremeni-ochistki-pbs/","tags":[""],"updated":"2025-12-14T03:51:37+03:00"}
---

В [[Заметки/Self-hosting. Proxmox Backup Server (PBS)\|Proxmox Backup Server]] по умолчанию задача по сбору мусора помечает файлы для последующей очистки с задержкой в 24 часа и 5 минут. По факту любое удаление устаревших файлов сразу не выполняется.

Для изменения этого поведения можно переопределить настройку `GC Access-Time Cutoff`  в разделе параметры хранилища, и выполнить повторный запуск операции очистки.

![Настройка времени очистки PBS.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B2%D1%80%D0%B5%D0%BC%D0%B5%D0%BD%D0%B8%20%D0%BE%D1%87%D0%B8%D1%81%D1%82%D0%BA%D0%B8%20PBS.png)

Подробнее: [Backup Storage — Proxmox Backup 4.1.0-1 documentation](https://pbs.proxmox.com/docs/storage.html#tuning)