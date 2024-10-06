---
{"dg-publish":true,"dg-path":"Команды и настройки/Изменение размера диска для LXC контейнера Proxmox.md","permalink":"/komandy-i-nastrojki/izmenenie-razmera-diska-dlya-lxc-kontejnera-proxmox/","updated":"2024-10-06T02:54:44+03:00"}
---

## Уменьшение диска

Уменьшение размера диска опасная операция и может привести к потери данных на ужимаемом диске. Перед началом операции обязательно надо сделать бэкап.

Находим место хранения файла диска командой:
```shell
lvdisplay | grep "LV Path\|LV Size"
```

Ужимаем размер файловой системы:
```shell
resize2fs /dev/pve/vm-<ID_VM>-disk-0 <Size>G

lvreduce -L <Size>G /dev/pve/vm-<ID_VM>-disk-0
```

Изменяем описание диска в файле
```shell
nano /etc/pve/lxc/<ID_VM>.conf
```
Где заменяем `rootfs: local-lvm:vm-<ID_VM>-disk-0,size=<SizeOld>G` на `rootfs: local-lvm:vm-<ID_VM>-disk-0,size=<Size>G`

Где `<ID_VM> `- номер контейнера/виртуальной машины в proxmox
    `<Size>` - целевой размер диска
    `<SizeOld>` - старый размер диска

После изменения размера необходимо выполнить бэкап системы, удалить и восстановить из бэкапа виртуальную машину для корректной работы proxmox.
## Увеличение диска

Для увеличения размера диска можно воспользоваться веб интерфейсом proxmox.
![Изменение размера системного диска для LXC контейнера Proxmox.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%98%D0%B7%D0%BC%D0%B5%D0%BD%D0%B5%D0%BD%D0%B8%D0%B5%20%D1%80%D0%B0%D0%B7%D0%BC%D0%B5%D1%80%D0%B0%20%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%BD%D0%BE%D0%B3%D0%BE%20%D0%B4%D0%B8%D1%81%D0%BA%D0%B0%20%D0%B4%D0%BB%D1%8F%20LXC%20%D0%BA%D0%BE%D0%BD%D1%82%D0%B5%D0%B9%D0%BD%D0%B5%D1%80%D0%B0%20Proxmox.png)
