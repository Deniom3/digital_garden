---
{"dg-publish":true,"permalink":"/zametki/shablon-dlya-youtube-template/","created":"2024-09-03 20:01","updated":"2024-10-06T02:41:03+03:00"}
---

Шаблон заметки для плагина по обработке видео с ютуба в обсидиан:
```
<%"---"%>
type: youtube
tags:
aliases: 
- "~ {{title}}"
status: 🟩
Канал: {{channelName}}
Продолжительность: {{length}}
Ссылка: {{youtubeUrl}}
Пересказ:
data: {{noteCreated}}
<%"---"%>
![[{{thumbnail}}]]

---

> [!video]-
> <iframe title="{{title}}" src="https://www.youtube.com/embed/{{id}}?feature=oembed" height="113" width="200" style="aspect-ratio: 1.76991 / 1; width: 100%; height: 100%;" allowfullscreen="" allow="fullscreen"></iframe>

> [!summary]- Описание
> {{description}}

> [!abstract]- Пересказ нейросети
 
---
> [!urls]- Заметки
>  - 
```

---
> [!urls]- Упоминания:
> - 