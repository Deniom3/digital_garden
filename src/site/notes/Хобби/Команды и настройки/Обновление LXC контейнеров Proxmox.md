---
{"dg-publish":true,"dg-path":"Команды и настройки/Обновление LXC контейнеров Proxmox.md","permalink":"/komandy-i-nastrojki/obnovlenie-lxc-kontejnerov-proxmox/","updated":"2024-10-02T19:54:05+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Скрипт выполняет обновление всех выбранных контейнеров LXC. 

```shell
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/update-lxcs.sh)"
```

> [!important]
> Запускать на хосте Proxmox