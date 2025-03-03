---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка мусора LXC контейнеров Proxmox.md","permalink":"/komandy-i-nastrojki/ochistka-musora-lxc-kontejnerov-proxmox/","updated":"2025-01-31T18:59:10+03:00"}
---

Для сокращения занимаемого места (без фактического сжатия дисков) LXC контейнеров можно воспользоваться скриптом:

```shell
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/fstrim.sh)"
```

> [!important]
> Запускать на хосте Proxmox