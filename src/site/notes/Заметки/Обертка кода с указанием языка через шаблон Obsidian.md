---
{"dg-publish":true,"permalink":"/zametki/obertka-koda-s-ukazaniem-yazyka-cherez-shablon-obsidian/","updated":"2024-09-27T01:11:07+03:00"}
---

Для того что бы комбинацией клавиш оборачивать выделенный текст в блок кода с определенным языком надо создать шаблон со следующим содержимым (в шаблоне полностью весь блок вместе с \` ):

```python
<% tp.file.selection() %>
```

Шаблон забиндить в templater и установить на кнопку.
