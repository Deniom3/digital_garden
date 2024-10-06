---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка системы Linux.md","permalink":"/komandy-i-nastrojki/ochistka-sistemy-linux/","updated":"2024-10-06T02:52:45+03:00"}
---

Удалить неиспользуемые пакеты из кэша

```console
 sudo apt autoclean
```

Очистка кэша в Ubuntu, утилиты apt:

```console
 sudo apt clean
```

Удаление ненужных зависимостей:

```console
 sudo apt autoremove
```

Удаление log

```console
sudo journalctl --vacuum-size=1M
```