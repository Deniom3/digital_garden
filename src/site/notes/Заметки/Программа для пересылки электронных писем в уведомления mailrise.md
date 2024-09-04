---
{"dg-publish":true,"permalink":"/zametki/programma-dlya-peresylki-elektronnyh-pisem-v-uvedomleniya-mailrise/","created":"2024-08-25 22:26","updated":"2024-09-03T16:39:01+03:00"}
---

Является более функциональной альтернативой [[Заметки/Пересылка писем в telegram\|SmtpToTelegram]] основана на [[Заметки/Система отправки уведомлений Apprise\|Apprise]]. На вход принимает сообщения по smtp и переформирует их в уведомления на базе Apprise.

Имеет большое количество вариантов пересылки и легкие настройки.

> [!bug]
> Не корректно работает с proxmox так как не может обрабатывать большие письма и имя отправителя.
> 
>Ошибка:  [when attempting to configure proxmox to use mailrise I get a wierd error · Issue #123 · YoRyan/mailrise · GitHub](https://github.com/YoRyan/mailrise/issues/123)

---
> [!urls]- Упоминания:
> - 