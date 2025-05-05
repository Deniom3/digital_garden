---
{"dg-publish":true,"permalink":"/zametki/self-hosting-gitea/","created":"2024-08-12 00:06","updated":"2025-05-05T18:29:51+03:00"}
---

Легковесный вариант гит репозитория для хранения проектов git и создание зеркал общедоступных проектов. Имеет возможность использовать OAuth2 авторизацию и создание исполнителей.

Сайт проекта: [Gitea Official Website](https://about.gitea.com/)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">





```yaml
services:
  gitea:
    image: gitea/gitea:latest
    container_name: gitea
    environment:
      - ENABLE_REVERSE_PROXY_AUTHENTICATION=true
      - DISABLE_REGISTRATION=true
      - REQUIRE_SIGNIN_VIEW=true
      - USER_UID=1001
      - USER_GID=1001
    restart: always
    volumes:
      - /home/deniom/docker/gitea/data:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
      - /home/git/.ssh/:/data/git/.ssh
    ports:
      - 3000:3000
      - "127.0.0.1:2222:22"
    networks:
      - gitea
```

</div></div>


Альтернативный форк: [Forgejo – Beyond coding. We forge.](https://forgejo.org/)

Настройка репозитория: [Customizing Gitea | Gitea Documentation](https://docs.gitea.com/administration/customizing-gitea)

> [!note]
>Настройка аутентификации через Authentik:
>- Основное руководство [Gitea | authentik](https://docs.goauthentik.io/integrations/services/gitea/)
>- Уточнения по настройке групп [Gitea groups documentation is outdated · Issue #6987 · goauthentik/authentik · GitHub](https://github.com/goauthentik/authentik/issues/6987)
>- [[Заметки/Переключение встроенной авторизации на Authentik для GitTea\|Переключение встроенной авторизации на Authentik для GitTea]]

### Авторизация gitea по токену

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/zametki/avtorizacziya-gitea-po-tokenu/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Для подключения к закрытому репозиторию gitea необходимо использовать вариант подключения с токеном или авторизацию по ssh.

Для подключения по токену доступа необходимо в настройках пользователя перейти в радел приложения и создать новый токен доступа:
![Авторизация gitea по токену.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%90%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D0%B8%D1%8F%20gitea%20%D0%BF%D0%BE%20%D1%82%D0%BE%D0%BA%D0%B5%D0%BD%D1%83.png)

Для получения данных из репозитория по токену доступа необходимо добавить ключ авторизации в адрес доступа репозитория вида:

```
https://<token>@gitea.domein.ru/User/repa.git
```

---
> [!urls]- Упоминания:
> - 

</div></div>


### Настройка доступа к репозиториям gitea по ssh

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/zametki/nastrojka-dostupa-k-repozitoriyam-gitea-po-ssh/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




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

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Main\|Сервер Main]]

> [!description]- Примечание
> Примечание:: Облегченный git репозиторий