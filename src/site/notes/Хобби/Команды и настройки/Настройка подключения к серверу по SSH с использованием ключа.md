---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка подключения к серверу по SSH с использованием ключа.md","permalink":"/komandy-i-nastrojki/nastrojka-podklyucheniya-k-serveru-po-ssh-s-ispolzovaniem-klyucha/","updated":"2024-09-03T16:04:39+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Для создания и отправки ключей так же можно [[Заметки/Создание и отправка SSH ключа в Termius\|воспользоваться программой Termius.]]

Для того чтобы сгенерировать пары ключей выполните команду на сервере:
```shell
ssh-keygen -t rsa -b 2048
```

Программа предложит указать каталог, в который будут будут сохранены файлы ключей и попросит ввести секретную фразу.

Нажмите Enter чтобы использовать параметры по умолчанию, тогда программа сохранит ключи в каталог .ssh в домашней директории пользователя.

В каталоге будут лежать два файла:
- id_rsa — секретный ключ.
- id_rsa.pub — публичный ключ.

Заносим сгенерированный открытый ключ в авторизованные ключи сервера. Для этого скопируйте содержимое id_rsa.pub в конец файла authorized_keys:

```shell
cat id_rsa.pub && ~/.ssh/authorized_keys
```

Настройте ssh авторизацию по ключу в конфиге OpenSSH сервера:

```
nano /etc/ssh/sshd_config
```

Приведите текущие настройки в соответствие параметрам ниже:

```
PubkeyAuthentication yes  
AuthorizedKeysFile %h/.ssh/authorized_keys  
RhostsRSAAuthentication no  
HostbasedAuthentication no  
PermitEmptyPasswords no
```

Далее выполните перезапуск ssh сервера.

```
service sshd restart
```
