---
{"dg-publish":true,"permalink":"/zametki/otpravka-uvedomlenij-iz-proxmox-v-telegram-s-ispolzovaniem-webhook/","created":"2025-03-03 16:05","updated":"2025-03-16T19:21:36+03:00"}
---

Для отправки уведомлений из [[Заметки/Self-hosting. Proxmox Virtual Environment\|Proxmox]] в телеграмм можно использовать функционал Webhook. Подробная документация приведена по [ссылке](https://proxmox.deniom.ru:8006/pve-docs/chapter-notifications.html#notification_targets_webhook). 

Пример настройки уведомления для отправки в телеграмм:

![Отправка уведомлений из proxmox в telegram.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9E%D1%82%D0%BF%D1%80%D0%B0%D0%B2%D0%BA%D0%B0%20%D1%83%D0%B2%D0%B5%D0%B4%D0%BE%D0%BC%D0%BB%D0%B5%D0%BD%D0%B8%D0%B9%20%D0%B8%D0%B7%20proxmox%20%D0%B2%20telegram.png)

Method: `POST`
URL: `https://api.telegram.org/bot{{secrets.api_key}}/sendMessage`
Заголовок: `content-type` со значением `application/json`
Тело:
```
{
  "chat_id": "{{secrets.chat_id}}",
  "message_thread_id": "{{secrets.thread_id}}",
  "text": "<b>{{ title }}</b>\n<pre>{{ escape message }}</pre>",
  "parse_mode": "HTML"
}
```
В секретах указать необходимые параметры.

> [!note]
> Пример приведен для отправки в супергруппу (форум) телеграмм, для отправки в обычный канал параметр `message_thread_id` в теле не нужен.

> [!bug]
> Из за особенностей ограничений апи телеграмм длинные сообщения могут не отправляться через вебхук. Для исправления необходимо убрать логи из сообщения.

Подробнее [[Заметки/Изменение шаблона сообщений proxmox notification для резервного копирования\|Изменение шаблона сообщений proxmox notification для резервного копирования]]

---
> [!urls]- Упоминания:
> - 