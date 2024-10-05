---
{"dg-publish":true,"dg-path":"Команды и настройки/Установка node exporter как служба.md","permalink":"/komandy-i-nastrojki/ustanovka-node-exporter-kak-sluzhba/","updated":"2024-10-03T17:48:29+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Система сбора данных мониторинга node exporter может быть установлена как служба для этого необходимо выполнить следующие действия.

> [!info]
> Данная инструкция применима не только к proxmox

Загрузка актуальной версии бинарного файла:
```shell
wget https://github.com/prometheus/node_exporter/releases/download/v1.8.2/node_exporter-1.8.2.linux-amd64.tar.gz
tar xvfz node_exporter-*.*-amd64.tar.gz
cd node_exporter-*.*-amd64
```

> [!note]
> Изменить 1.8.2 на актуальную версию из [репозитория](https://github.com/prometheus/node_exporter?tab=readme-ov-file)

Переместим файл в правильную папку и проверим:
```sh
mv ./node_exporter /usr/local/bin/
ls /usr/local/bin/node_exporter
```

Создадим пользователя и установим права:
```sh
useradd --no-create-home --shell /bin/false node_exporter
chown node_exporter:node_exporter /usr/local/bin/node_exporter
```

Создадим файл службы:
```sh
nano /etc/systemd/system/node_exporter.service
```
Со следующим содержимым:
```sh
# /etc/systemd/system/node_exporter.service
[Unit]
Description=Node Exporter
After=network.target

[Service]
User=node_exporter
Group=node_exporter
Type=simple
ExecStart=/usr/local/bin/node_exporter
Nice=-10
#CPUShares=512
#MemoryLimit=100M
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

Выполним перезагрузку служб для обнаружения новой и запустим ее с добавлением в автозапуск:
```sh
systemctl daemon-reload
systemctl start node_exporter
systemctl enable node_exporter
systemctl status node_exporter
```
