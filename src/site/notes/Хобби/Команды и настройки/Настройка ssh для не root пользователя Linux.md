---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка ssh для не root пользователя Linux.md","permalink":"/komandy-i-nastrojki/nastrojka-ssh-dlya-ne-root-polzovatelya-linux/","updated":"2025-02-03T15:56:25+03:00"}
---

После [[Хобби/Команды и настройки/Создание нового пользователя Linux\|создания нового пользователя]] необходимо настроить ssh.

```shell
sudo mkdir -p /home/deniom/.ssh
sudo touch /home/deniom/.ssh/authorized_keys
sudo chmod 700 /home/deniom/.ssh
sudo chmod 600 /home/deniom/.ssh/authorized_keys
sudo chown -R deniom:deniom /home/deniom/.ssh
```