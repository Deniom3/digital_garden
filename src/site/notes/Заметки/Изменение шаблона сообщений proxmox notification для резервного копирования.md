---
{"dg-publish":true,"permalink":"/zametki/izmenenie-shablona-soobshhenij-proxmox-notification-dlya-rezervnogo-kopirovaniya/","created":"2025-03-16 18:50","updated":"2025-03-16T18:58:01+03:00"}
---

Шаблон сообщения используемый для отправки сообщения о резервном копировании хранится в файле по пути: 
```
/usr/share/pve-manager/templates/default/vzdump-body.txt.hbs
```

Стандартный шаблон:
```
{{error-message}}
Details
=======
{{table guest-table}}
Total running time: {{duration total-time}}
Total size: {{human-bytes total-size}}

Logs
====
{{logs}}
```

Для сокращения сообщения можно удалить раздел Logs.

> [!important] Важно
> Данный файл может быть перезаписан после обновления proxmox

---
> [!urls]- Упоминания:
> - 