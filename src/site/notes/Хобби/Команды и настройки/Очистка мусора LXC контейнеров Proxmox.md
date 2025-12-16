---
{"dg-publish":true,"dg-path":"Команды и настройки/Очистка мусора LXC контейнеров Proxmox.md","permalink":"/komandy-i-nastrojki/ochistka-musora-lxc-kontejnerov-proxmox/","updated":"2025-12-13T02:33:48+03:00"}
---

Для сокращения занимаемого места (без фактического сжатия дисков) LXC контейнеров [[Заметки/Self-hosting. Proxmox Virtual Environment\|Proxmox]] можно воспользоваться скриптом:

```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/community-scripts/ProxmoxVE/main/tools/pve/fstrim.sh)"
```

> [!important]
> Запускать на хосте Proxmox

Так же можно добавить в cron задачу по очистке:

```
crontab -e
```

```
0 5 * * 0 PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin /bin/bash -c 'pct list | awk "/^[0-9]/ {print \$1}" | while read ct; do pct fstrim $ct; done >>/var/log/fstrim-cron.log 2>/dev/null'
```

> [!note]
> Данная команда будет запускать в 5:00 в воскресенье очистку дисков операцией fstrim