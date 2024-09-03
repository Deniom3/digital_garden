---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/ochistka-sistemy-linux/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

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