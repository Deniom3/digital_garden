---
{"dg-publish":true,"dg-path":"Команды и настройки/Модификация интерефейса Proxmox VE для вывода температуры.md","permalink":"/komandy-i-nastrojki/modifikacziya-interefejsa-proxmox-ve-dlya-vyvoda-temperatury/","updated":"2024-12-15T14:22:12+03:00"}
---

Для вывода информации о температуре процессора и дисков в интерфейс proxmox можно воспользоваться следующей модификацией:

```shell
apt-get install lm-sensors
#Для включения данных о дисках
modprobe drivetemp sensors
echo drivetemp | tee -a /etc/modules
#Получение скрипта
wget https://raw.githubusercontent.com/Meliox/PVE-mods/main/pve-mod-gui-sensors.sh
bash pve-mod-gui-sensors.sh install
```

Для удаления модификации необходимо запустить с командой `uninstall`

```sh
bash pve-mod-gui-sensors.sh uninstall
```

После запуска скрипта перезагрузить окно браузера комбинацией `Ctrl + F5` в результате добавиться новая информация:

![Модификация интерефейса Proxmox VE для вывода температуры.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%B4%D0%B8%D1%84%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F%20%D0%B8%D0%BD%D1%82%D0%B5%D1%80%D0%B5%D1%84%D0%B5%D0%B9%D1%81%D0%B0%20Proxmox%20VE%20%D0%B4%D0%BB%D1%8F%20%D0%B2%D1%8B%D0%B2%D0%BE%D0%B4%D0%B0%20%D1%82%D0%B5%D0%BC%D0%BF%D0%B5%D1%80%D0%B0%D1%82%D1%83%D1%80%D1%8B.png)

Репозиторий мода: [GitHub - Meliox/PVE-mods: Proxmox modifications](https://github.com/Meliox/PVE-mods)