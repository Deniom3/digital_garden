---
{"dg-publish":true,"dg-path":"Команды и настройки/Объединение local и local-lvm proxmox.md","permalink":"/komandy-i-nastrojki/obedinenie-local-i-local-lvm-proxmox/","tags":[""],"updated":"2025-12-09T03:07:40+03:00"}
---


1. Изменить настройки для local добавив поддержку томов lxc и виртуальных дисков
2. Если в local-lvm есть диски перенести на local или любой другой диск
3. Удалить local-lvm в интерфейсе proxmox хранилища
4. Выполнить в proxmox команды по очереди

```shell
lvremove /dev/pve/data
lvresize -l +100%FREE /dev/pve/root
resize2fs /dev/mapper/pve-root
```

> [!note]
> Позволяет решить проблему нехватки места при создании резервной копии в режиме снимок.
