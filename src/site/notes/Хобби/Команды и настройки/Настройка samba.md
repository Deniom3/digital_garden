---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка samba.md","permalink":"/komandy-i-nastrojki/nastrojka-samba/","updated":"2024-10-06T02:55:13+03:00"}
---

Установка samba
```shell
sudo apt install samba -y
```

Включение службы
```shell
sudo systemctl enable smbd
sudo systemctl start smbd
```

Для проверки можно воспользоваться командой:
```shell
sudo systemctl status smbd
```

Конфигурация прописана в файле `/etc/samba/smb.conf`:
```shell
sudo nano /etc/samba/smb.conf
```

Пример конфигурации для доступа с авторизацией:
```shell
[immich]
path = /mnt/immich
valid users = @deniom
guest ok = no
browsable = yes
writable = yes
```

Установка samba пароля:
```shell
sudo smbpasswd -a deniom
```

После изменения параметров перезагрузить:
```shell
sudo systemctl restart smbd
```