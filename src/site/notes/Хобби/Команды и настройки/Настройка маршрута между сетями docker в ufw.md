---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка маршрута между сетями docker в ufw.md","permalink":"/komandy-i-nastrojki/nastrojka-marshruta-mezhdu-setyami-docker-v-ufw/","tags":[""],"updated":"2024-10-13T21:04:44+03:00"}
---


```sh
# Включение ufw для docker
sudo ufw default allow routed
sudo ufw allow in on docker0
sudo ufw allow out on docker0

# Открытия доступа между сетями
sudo ufw allow from 10.2.0.0/24 to 10.2.1.0/24
sudo ufw allow from 10.2.1.0/24 to 10.2.0.0/24
```