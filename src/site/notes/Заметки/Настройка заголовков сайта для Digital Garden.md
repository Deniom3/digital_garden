---
{"dg-publish":true,"permalink":"/zametki/nastrojka-zagolovkov-sajta-dlya-digital-garden/","created":"2024-09-05 01:50","updated":"2024-09-05T02:05:17+03:00"}
---

При публикации заметок на [[Заметки/Публикация заметок Obsidian в Cloudflare Pages\|Cloudflare]] есть возможность задать заголовки безопасности.

Создать файл в репозитории по пути `/src/site/_headers` следующего содержимого:
```
/*
  X-Frame-Options: SAMEORIGIN
  Permissions-Policy: document-domain=(self)
```

А так же скорректировать файл `/src/helpers/userSetup.js` добавив строчку в `function userEleventySetup(eleventyConfig)`

```
eleventyConfig.addPassthroughCopy("src/site/_headers");
```

![Настройка заголовков для Digital Garden.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9D%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0%20%D0%B7%D0%B0%D0%B3%D0%BE%D0%BB%D0%BE%D0%B2%D0%BA%D0%BE%D0%B2%20%D0%B4%D0%BB%D1%8F%20Digital%20Garden.png)

Подробнее про заголовки: [Headers | Cloudflare Pages docs](https://developers.cloudflare.com/pages/configuration/headers/)

---
> [!urls]- Упоминания:
> - 