---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка мусора LXC контейнеров Proxmox.md","permalink":"/komandy-i-nastrojki/ochistka-musora-lxc-kontejnerov-proxmox/","updated":"2025-09-14T13:19:45+03:00"}
---

Для сокращения занимаемого места (без фактического сжатия дисков) LXC контейнеров можно воспользоваться скриптом:

```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/fstrim.sh)"
```

> [!important]
> Запускать на хосте Proxmox