---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка ssh для не root пользователя Linux.md","permalink":"/komandy-i-nastrojki/nastrojka-ssh-dlya-ne-root-polzovatelya-linux/","updated":"2024-09-24T22:29:54+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
После [[Хобби/Команды и настройки/Создание нового пользователя Linux\|создания нового пользователя]] необходимо настроить ssh.

```shell
sudo mkdir -p /home/deniom/.ssh
sudo touch /home/deniom/.ssh/authorized_keys
sudo chmod 700 /home/deniom/.ssh
sudo chmod 600 /home/deniom/.ssh/authorized_keys
sudo chown -R deniom:deniom /home/deniom/.ssh
```