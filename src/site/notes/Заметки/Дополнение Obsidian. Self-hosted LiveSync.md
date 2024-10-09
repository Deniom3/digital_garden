---
{"dg-publish":true,"permalink":"/zametki/dopolnenie-obsidian-self-hosted-live-sync/","created":"2024-09-14 03:00","updated":"2024-10-09T19:50:52+03:00"}
---

Дополнение предназначено для синхронизации заметок Obsidian в реальном времени с размещением на собственном сервере.

Репозиторий: [GitHub - vrtmrz/obsidian-livesync](https://github.com/vrtmrz/obsidian-livesync)

Решение основано на базе данных [[Заметки/Self-hosting. CouchDB\|couchdb]]

Инструкция по установке: [[Статьи/Настройка синхронизации Obsidian с использованием дополнения liveSync\|Настройка синхронизации Obsidian с использованием дополнения liveSync]]

Инструкции по настройке:
- [Customization Sync Documentation · vrtmrz/obsidian-livesync · Discussion #394 · GitHub](https://github.com/vrtmrz/obsidian-livesync/discussions/394) - настройка выборочной синхронизации
- [obsidian-livesync/docs/settings.md at main · vrtmrz/obsidian-livesync · GitHub](https://github.com/vrtmrz/obsidian-livesync/blob/main/docs/settings.md)

Интересные смежные решения:
- [GitHub - vrtmrz/livesync-subscribe-publish-kit](https://github.com/vrtmrz/livesync-subscribe-publish-kit) - публикация заметок как блог (нет отбора заметок на публикацию)
- [GitHub - vrtmrz/livesync-bridge](https://github.com/vrtmrz/livesync-bridge) - обмен заметками между хранилищами
- [GitHub - vrtmrz/livesync-subscribe-publish](https://github.com/vrtmrz/livesync-subscribe-publish) - отслеживание изменений в БД и выполнение скриптов
## Закрытие доступа с использованием [[Заметки/Self-hosting. Authentik\|Authentik]]:

Для закрытия доступа к базе данных с помощью Authentik необходимо создать стандартную настройку проксирования и добавить в исключения конкретную базу:
```
^/.*<DATA_BASE_NAME>/.*
```

---
> [!urls]- Упоминания:
> - [[Заметки/Дополнения Obsidian\|Дополнения Obsidian]]

> [!description]- Примечание
> Примечание:: Динамическая синхронизация заметок