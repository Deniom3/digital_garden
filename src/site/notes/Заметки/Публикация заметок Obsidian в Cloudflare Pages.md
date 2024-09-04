---
{"dg-publish":true,"permalink":"/zametki/publikacziya-zametok-obsidian-v-cloudflare-pages/","created":"2024-09-05 01:25","updated":"2024-09-05T01:49:41+03:00"}
---

Для публикации используется плагин [[Заметки/Дополнение Digital Garden\|Digital Garden]]:
1. Загрузить и установить плагин Digital Garden в Obsidian
2. Создать новый репозиторий на основании шаблона [GitHub - oleeskild/digitalgarden](https://github.com/oleeskild/digitalgarden)  (репозиторий может быть приватным)

> [!warning]
> В репозитории будет размещенн исходный код который нужен для сборки сайта и только те заметки которые выбраны для публикации.

3. Создать токен доступа как приведено на картинке
 ![Публикация заметок Obsidian в Cloudflare Pages.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%83%D0%B1%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F%20%D0%B7%D0%B0%D0%BC%D0%B5%D1%82%D0%BE%D0%BA%20Obsidian%20%D0%B2%20Cloudflare%20Pages.png)
4. Настроить первичную публикацию как приведено в документации по дополнению
5. Перейти в Cloudflare Dashboard в раздел Workers & Pages и выполнить следующие настройки:
![Публикация заметок Obsidian в Cloudflare Pages-1.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%83%D0%B1%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F%20%D0%B7%D0%B0%D0%BC%D0%B5%D1%82%D0%BE%D0%BA%20Obsidian%20%D0%B2%20Cloudflare%20Pages-1.png)
![Публикация заметок Obsidian в Cloudflare Pages-2.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%83%D0%B1%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F%20%D0%B7%D0%B0%D0%BC%D0%B5%D1%82%D0%BE%D0%BA%20Obsidian%20%D0%B2%20Cloudflare%20Pages-2.png)
![Публикация заметок Obsidian в Cloudflare Pages-3.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%83%D0%B1%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F%20%D0%B7%D0%B0%D0%BC%D0%B5%D1%82%D0%BE%D0%BA%20Obsidian%20%D0%B2%20Cloudflare%20Pages-3.png)
![Публикация заметок Obsidian в Cloudflare Pages-4.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9F%D1%83%D0%B1%D0%BB%D0%B8%D0%BA%D0%B0%D1%86%D0%B8%D1%8F%20%D0%B7%D0%B0%D0%BC%D0%B5%D1%82%D0%BE%D0%BA%20Obsidian%20%D0%B2%20Cloudflare%20Pages-4.png)
Команда настройки `npm run build` выходная директория `dist`

На этом настройка закончена сайт будет собран и опубликован на внутреннем домене cloudflare. Сборка сайта будет выполняться автоматически на push изменений в git.

> [!note]
> Если в cloudflare так же настроен dns то можно сразу выполнить привязку домена к сайту.

> [!important]
> Каждая версия сборки публикуется по умолчанию под своим служебным доменом и может быть просмотрена в любой момент. Ассоциативный домен работает на самую последнюю версию.

---
> [!urls]- Упоминания:
> - 