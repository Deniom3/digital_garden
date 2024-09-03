---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/aktivacziya-parolya-microsoft-dlya-avtorizaczii-rdp-ili-samba/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---

Для того что бы была возможность авторизоваться на компьютере при подключении по RDP или SAMBA необходимо запустить под именем аккаунта любую программу что захешировать пароль на устройстве.

Команда для сохранения пароля:
```cmd
runas /u:MicrosoftAccount\<mail>@gmail.com cmd.exe
```