---
{"dg-publish":true,"dg-path":"Команды и настройки/Проброс портов на удаленный сервер через SSH.md","permalink":"/komandy-i-nastrojki/probros-portov-na-udalennyj-server-cherez-ssh/","tags":[""],"updated":"2024-10-09T19:47:49+03:00"}
---

## Базовый проброс выполняется командой: 
```shell
ssh -R <Local_Port_Service>:localhost:<Remote_Port> <User>@<Server_IP> -i /home/path_to_home_dir/.ssh/private_key -N
```
В результате:
- Порт `Local_Port_Service` локальной машины пробрасывается на `Remote_Port` удаленной машины
## Автоматическое восстановление проброса:
Если проброс необходимо автоматически восстанавливать при разрыве можно использовать `autossh`:
```shell
autossh -NR <Local_Port_Service>:localhost:<Remote_Port> -M 0 -o "ServerAliveInterval 10" -o "ServerAliveCountMax 3" <User>@<Server_IP> -i /home/path_to_home_dir/.ssh/private_key
```

> [!note]- Пояснение параметров
> 1. **autossh**: Это утилита, которая автоматически перезапускает SSH-соединение, если оно разрывается. Это полезно для поддержания постоянного соединения, особенно для туннелирования.
> 
> 2. **-NR <Local_Port_Service>:localhost:<Remote_Port>**: 
>    - **-N**: Указывает, что не нужно запускать удаленные команды (только туннелирование).
>    - **-R**: Указывает на создание удаленного перенаправления порта.
>    - `<Local_Port_Service>`: Порт на удаленном сервере, который будет перенаправлен.
>    - `localhost`: Указывает, что перенаправление будет происходить на локальный адрес (то есть на самом удаленном сервере).
>    - `<Remote_Port>`: Порт на локальном сервере, к которому будет перенаправлен трафик.
> 
>    В итоге, эта часть команды создает туннель, который позволяет доступ к локальному сервису на удаленном сервере через указанный порт.
> 
> 3. **-M 0**: Отключает мониторинг соединения. Обычно `autossh` использует специальный порт для проверки состояния соединения, но с `-M 0` этот функционал отключается.
> 
> 4. **-o "ServerAliveInterval 10"**: Эта опция задает интервал (в секундах) для отправки пакетов "keep-alive" на сервер. В данном случае, каждые 10 секунд будет отправляться сигнал, чтобы поддерживать соединение активным.
> 
> 5. **-o "ServerAliveCountMax 3"**: Эта опция указывает максимальное количество неудачных попыток получения ответа от сервера. Если сервер не отвечает на 3 пакета "keep-alive", соединение будет разорвано.
> 
> 6. `<User>@<Server_IP>`: Указывает пользователя и IP-адрес удаленного сервера, к которому вы подключаетесь. Замените `<User>` на имя пользователя и `<Server_IP>` на IP-адрес сервера.
> 
> 7. **-i /home/path_to_home_dir/.ssh/private_key**: Указывает путь к приватному ключу SSH, который будет использоваться для аутентификации при подключении к удаленному серверу.

> [!danger]
> Такой проброс будет восстанавливаться в случае сетевого разрыва но не после перезагрузки сервера источника (локального сервера)
## Проброс как сервис:
Для восстановления после перезагрузки создадим сервис
```shell
nano /etc/systemd/system/autossh.service
```
следующего содержимого:
```
[Unit]
Description=AutoSSH to My Server
After=network.target

[Service]
Environment="AUTOSSH_GATETIME=0"
Environment="AUTOSSH_LOGFILE=/var/log/autossh"
ExecStart=autossh -NR  <Local_Port_Service>:localhost:<Remote_Port> -M 0 -o "ExitOnForwardFailure=yes" -o "ServerAliveInterval=180" -o "ServerAliveCountMax=3" -o "PubkeyAuthentication=yes" -o "PasswordAuthentication=no" -i /home/path_to_home_dir/.ssh/private_key <User>@<Server_IP> -p 22
Restart=always

[Install]
WantedBy=multi-user.target

```
запустить сервис:
```shell
systemctl daemon-reload
systemctl enable autossh
```

---
> [!urls]- Упоминания:

> [!description]- Примечание
> Примечание::  Проброс портов через SSH