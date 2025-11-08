---
{"dg-publish":true,"permalink":"/zametki/self-hosting-rclone/","tags":[""],"created":"2024-10-26 18:38","updated":"2025-11-07T10:44:00+03:00"}
---

Rclone _("rsync для облачного хранилища")_ — это программа командной строки для синхронизации файлов и каталогов с различными облачными хранилищами и обратно.

Репозиторий: [GitHub - rclone/rclone: "rsync for cloud storage" - Google Drive, S3, Dropbox, Backblaze B2, One Drive, Swift, Hubic, Wasabi, Google Cloud Storage, Azure Blob, Azure Files, Yandex Files](https://github.com/rclone/rclone)


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/zametki/nastrojka-rclone-dlya-nextcloud/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Программа [[Заметки/Self-hosting. Rclone\|rclone]] позволяет удобно взаимодействовать с сервером [[Заметки/Self-hosting. Nextcloud\|Nextcloud]]  используя протокол webdav.

Установка:
```
curl https://rclone.org/install.sh | sudo bash
```

Запуск настройки:
```
rclone config 
```

Параметры настройки:
url должна иметь вид `https://nextcloud.domein.ru/remote.php/dav/files/user` где `user` имя пользователя nextcloud в каталоге (обычно имя пользователя с маленькой буквы).
Пароль - это пароль приложения созданный в настройках раздел безопасность.

Мастер настройки:
```
e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> n

Enter name for new remote.
name> nextcloud

Option Storage.
Type of storage to configure.
Choose a number from below, or type in your own value.
Storage> 58

Option url.
URL of http host to connect to.
E.g. https://example.com.
Enter a value.
url> https://nextcloud.domein.ru/remote.php/dav/files/user

Option vendor.
Name of the WebDAV site/service/software you are using.
Choose a number from below, or type in your own value.
Press Enter to leave empty.
vendor> 2

Option user.
User name.
In case NTLM authentication is used, the username should be in the format 'Domain\User'.
Enter a value. Press Enter to leave empty.
user> Username

Option pass.
Password.
Choose an alternative below. Press Enter for the default (n).
y) Yes, type in my own password
g) Generate random password
n) No, leave this optional password blank (default)
y/g/n> y
Enter the password:
password:

Option bearer_token.
Bearer token instead of user/pass (e.g. a Macaroon).
Enter a value. Press Enter to leave empty.
bearer_token> 

Edit advanced config?
y) Yes
n) No (default)
y/n> n

Configuration complete.
Options:
- type: webdav
- url: https://nextcloud.domein.ru/remote.php/dav/files/user
- vendor: nextcloud
- user: Username
- pass: *** ENCRYPTED ***
Keep this "nextcloud" remote?
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d>y 
```

---
> [!urls]- Упоминания:
> - 

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 
> [!description]- Примечание
> Примечание:: Синхронизация файлов и каталогов с разными облачными провайдерами
