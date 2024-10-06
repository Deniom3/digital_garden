---
{"dg-publish":true,"dg-path":"Команды и настройки/Обновление LXC контейнеров Proxmox.md","permalink":"/komandy-i-nastrojki/obnovlenie-lxc-kontejnerov-proxmox/","updated":"2024-10-06T02:52:36+03:00"}
---

Скрипт выполняет обновление всех выбранных контейнеров LXC. 

```shell
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/update-lxcs.sh)"
```

> [!important]
> Запускать на хосте Proxmox