---
{"dg-publish":true,"permalink":"/zametki/nastrojka-dostupa-k-repozitoriyam-gitea-po-ssh/","created":"2025-05-05 02:22","updated":"2025-05-05T19:36:35+03:00"}
---

Преимуществом подключения по ssh к репозиторию является то что не надо проходить авторизацию по паролю или использовать в адреса в явном виде токен доступа что обеспечивает дополнительную безопасность.

### Настройка gitea

Подробная актуальная документация доступна на сайте: [Gitea Documentation](https://docs.gitea.com/next/installation/install-with-docker#understanding-ssh-access-to-gitea-without-passthrough)

1. Создать отдельного пользователя git без установки пароля
```shell
sudo useradd -m -s /bin/bash git
sudo mkdir -p /home/git/.ssh
sudo touch /home/git/.ssh/authorized_keys
sudo chmod 700 /home/git/.ssh
sudo chmod 600 /home/git/.ssh/authorized_keys
sudo chown -R git:git /home/deniom/.ssh
```
2. Внести изменения в [[Хобби/Docker compose/Gitea\|docker compose]]
```yaml
ports:  
# [...]  
	- "127.0.0.1:2222:22"

environment:  
	- USER_UID=1001  
	- USER_GID=1001
```
Где USER_UID и USER_GID определить командой
```sh
id git
```
3. Если раньше уже запускался gitea и есть репозитории то передать права на папку с данными на нового пользователя командой ниже, вся дальнейшая работа с репозиториями гит будет идти от данного пользователя
```sh
sudo chown -R git:git /home/deniom/docker/gitea/data
```
4. Создаем ключ для связи с gitea, и передаем его новому пользователю
```sh
sudo -u git ssh-keygen -t rsa -b 4096 -C "Gitea Host Key"
sudo -u git cat /home/git/.ssh/id_rsa.pub | sudo -u git tee -a /home/git/.ssh/authorized_keys  
sudo -u git chmod 600 /home/git/.ssh/authorized_keys
```
5. Создаем скрипт для подмены команды SSH
```sh
cat <<"EOF" | sudo tee /usr/local/bin/gitea
#!/bin/sh
ssh -p 2222 -o StrictHostKeyChecking=no git@127.0.0.1 "SSH_ORIGINAL_COMMAND=\"$SSH_ORIGINAL_COMMAND\" $0 $@"
EOF
```
```sh
sudo chmod +x /usr/local/bin/gitea
```
При помощи данного скрипта будет выполнятся переадресация в контейнер gitea но при этом доступ к самому серверу так же останется по 22 порту. Подробнее в документации gitea.
6. Перезапустить docker контейнер gitea с новыми настройками
7. Добавим новый ключ в интерфейсе gitea. Для этого идем в `Профиль -> Ключи SSH -> Добавить ключ`
8. Проверим работу (пока только по ip)
```sh
git clone git@192.168.0.132:Deniom\testrepa.git
```

### Настройка проксирования

Для обращения к gitea по ssh с использованием домена необходимо выполнить проектирование tcp трафика по 22 порту. Для этого будет использован traefik.

> [!note] Примечание
> Принципе можно проксировать на любой другой порт но тогда ссылки будет иметь вид отличный от стандартного и выглядить так `ssh://git@gitea.mydomain.com:2222/Deniom/Test.git`

1. Перенести подключение по ssh на сервере с traefik с 22 порта (см. [[Хобби/Команды и настройки/Изменение порта доступа по SSH\|Изменение порта доступа по SSH]])
2. Внести изменения в [[Хобби/Docker compose/Traefik\|Traefik]] открыв 22 порт
```yml
    ports:
      - 22:22
```
3. В статическую конфигурацию `traefik.yml` добавить точку входа ssh
```yml
entryPoints:
  ssh:
    address: ":22"
```
4. Изменить динамическую конфигурацию для gitea добавив секцию проксирования tcp
```yml
tcp:
  routers:
    gitea-ssh:
      entryPoints:
        - ssh
      rule: "HostSNI(`*`)"
      service: gitea-ssh
  
  services:
    gitea-ssh:
      loadBalancer:
        servers:
          - address: 192.168.0.132:22
```

> [!hint] Важно
> Секция tcp должна быть отдельно представлена а не встроена в уже существующий http, можно разместить в конце файла.

5. Проверка работы по домену
```sh
git clone git@gitea.domein.ru:Deniom\testrepa.git
```

> [!attention]
> Для доступа из вне необходимо обязательно прокидывать 22 порт на traefik иначе работать нечего не будет, не забываем изменить настройки роутера.

> [!bug]
> В случае ошибки ssh про атаку в середине как на скрине ниже необходимо очистить файлы в /home/git/.ssh/ и выполнить пункт 4 повторно.

![Настройка доступа к репозиториям gitea по ssh.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B4%D0%BE%D1%81%D1%82%D1%83%D0%BF%D0%B0%20%D0%BA%20%D1%80%D0%B5%D0%BF%D0%BE%D0%B7%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D1%8F%D0%BC%20gitea%20%D0%BF%D0%BE%20ssh.png)

---
> [!urls]- Упоминания:
> - 