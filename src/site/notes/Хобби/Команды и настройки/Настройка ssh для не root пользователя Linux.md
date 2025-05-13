---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка ssh для не root пользователя Linux.md","permalink":"/komandy-i-nastrojki/nastrojka-ssh-dlya-ne-root-polzovatelya-linux/","updated":"2025-05-12T19:40:22+03:00"}
---


```shell
sudo mkdir -p /home/deniom/.ssh
```
```sh
sudo touch /home/deniom/.ssh/authorized_keys
```
```sh
sudo chmod 700 /home/deniom/.ssh 
```
```sh
sudo chmod 600 /home/deniom/.ssh/authorized_keys 
```
```sh
sudo chown -R deniom:deniom /home/deniom/.ssh 
```