---
{"dg-publish":true,"permalink":"/zametki/vosstanovlenie-docker-v-proxmox-lxc-posle-obnovleniya-app-armor/","tags":["📥"],"created":"2025-11-08 15:00","updated":"2025-11-08T15:12:36+03:00"}
---

Обновление безопасности пакета runc на версии 1.3.2+ и 1.2.7+ изменило взаимодействие docker с ядром системы в lxc контейнерах.  Это приводит к ошибке работы docker:

```
Error response from daemon: failed to create task for container: failed to create shim task: OCI runtime create failed: runc create failed: unable to start container process: error during container init: open sysctl net.ipv4.ip_unprivileged_port_start file: reopen fd 8: permission denied: unknown
```

Подробное обсуждение по ошибке: [CVE-2025-52881: fd reopening causes issues with AppArmor profiles (\`open sysctl net.ipv4.ip\_unprivileged\_port\_start file: reopen fd 8: permission denied\`) · Issue #4968 · opencontainers/runc](https://github.com/opencontainers/runc/issues/4968)
И ветка обсуждения proxmox: [Docker inside LXC (net.ipv4.ip\_unprivileged\_port\_start error) \| Proxmox Support Forum](https://forum.proxmox.com/threads/docker-inside-lxc-net-ipv4-ip_unprivileged_port_start-error.175437/)

## Обходной путь решения от сообщества

> [!danger]
> Решение временное и на постоянной основе не рекомендуется, необходимо ждать патча исправления от proxmox\docker\runc

В Proxmox используется `lxc.apparmor.profile = generated`, а это значит, что изменение профилей в `/etc/apparmor.d` ничего не даёт, потому что новый профиль генерируется автоматически на основе жестко заданных строк в LXC. 

Вместо этого вам нужно добавить в `/etc/pve/lxc/$ctr.conf`  на хосте proxmox следующую конфигурацию:

```
lxc.apparmor.profile: unconfined
lxc.mount.entry: /dev/null sys/module/apparmor/parameters/enabled none bind 0 0
```

И перезапустите lxc.

`/dev/null` bind-mount нужен для того, чтобы Docker думал, что в системе отключён AppArmor. Если вы не добавите эту строку, то можете столкнуться с ошибкой `Error response from daemon: Could not check if docker-default AppArmor profile was loaded: open /sys/kernel/security/apparmor/profiles: permission denied`. Это вызвано не конфигурируемой политикой безопасности в AppArmor, связанной с вложенностью пространств имён. Единственное реальное решение этой проблемы — исправить сгенерированный LXC профиль.

---
> [!urls]- Упоминания:
> - 