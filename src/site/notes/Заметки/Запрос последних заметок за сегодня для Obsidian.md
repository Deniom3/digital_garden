---
{"dg-publish":true,"permalink":"/zametki/zapros-poslednih-zametok-za-segodnya-dlya-obsidian/","updated":"2024-09-03T16:33:00+03:00"}
---

dataviewjs
```js
const today = moment().format("YYYY-MM-DD");
const currentWeek = moment().format("YYYY-[W]WW");

// Получить сегодняшнюю ежедневную заметку
const todaysDailyNote = dv.page(`type: dailynote and data = "${today}"`);

// Получить еженедельную заметку на этой неделе
const thisWeeksWeeklyNote = dv.page(`type: dailynote and data = "${currentWeek}"`);

// Отобразить результаты
if (!todaysDailyNote || todaysDailyNote.length === 0) {
  dv.header(3, "Сегодня нет заметок");
} else {
  dv.header(3, "Планы на сегодня");
  dv.paragraph(`- [[${todaysDailyNote[0].file.link}]]`); // Выводим ссылку на файл
}

if (!thisWeeksWeeklyNote || thisWeeksWeeklyNote.length === 0) {
  dv.header(3, "На этой неделе нет заметок");
} else {
  dv.header(3, "Планы на этой неделе");
  dv.paragraph(`- [[${thisWeeksWeeklyNote[0].file.link}]]`); // Выводим ссылку на файл
}

// Получить все ежедневные заметки
const allDailyNotes = dv.pages().where(p => p.type === "dailynote");

// Фильтровать все ежедневные заметки, чтобы показать только сегодняшние и текущей недели, используя частичные совпадения
const filteredDailyNotes = allDailyNotes.filter(note => {
  // Проверяем, является ли data строкой и используем includes, иначе преобразуем в строку и затем используем includes
  if (typeof note.data === "string") {
    return note.data.includes(today) || note.data.includes(currentWeek);
  } else {
    return String(note.data).includes(today) || String(note.data).includes(currentWeek);
  }
});

// Отобразить отфильтрованные ежедневные заметки
if (filteredDailyNotes.length > 0) {
  dv.header(3, "Планы на сегодня и текущей неделе");
  filteredDailyNotes.forEach(note => {
    dv.paragraph(`- [[${note.file.link}]]`); // Выводим ссылки на файлы
  });
}


```
