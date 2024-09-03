---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/izmenenie-porta-dostupa-po-ssh/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Для изменения порта доступа (по умолчанию 22) для SSH необходимо скорректировать файл
```shell
nano /etc/ssh/sshd_config
```

Необходимо закомментировать или добавить строку, заменив 22 на нужный порт.
```shell
Port 22
```

После перезагрузить SSH сервер
```shell
service sshd restart
```