---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка системы Linux.md","permalink":"/komandy-i-nastrojki/ochistka-sistemy-linux/","updated":"2025-12-04T10:21:38+03:00"}
---

Удалить неиспользуемые пакеты из кэша

```console
 apt autoclean
```

Очистка кэша в Ubuntu, утилиты apt:

```console
apt clean
```

Удаление ненужных зависимостей:

```console
apt autoremove
```

Удаление log

```console
journalctl --vacuum-size=1M
```