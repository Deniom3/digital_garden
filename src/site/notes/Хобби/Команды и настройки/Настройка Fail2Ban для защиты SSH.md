---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/nastrojka-fail2-ban-dlya-zashhity-ssh/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Для установки и включения выполняем команды:

```shell
apt-get install fail2ban
systemctl enable fail2ban
systemctl start fail2ban
```

Вносим в файл настроек Fail2Ban расположенного по пути

```
nano /etc/fail2ban/jail.d/ssh.conf
```

Пример файла конфигурации приведен в [[Хобби/Конфиги/Fail2Ban SSH\|Fail2Ban SSH]]

Чтобы изменения вступили в силу, перезапускаем сервис:

```
systemctl restart fail2ban
```
