---
{"dg-publish":true,"permalink":"/zametki/obertka-koda-s-ukazaniem-yazyka-cherez-shablon-obsidian/"}
---

Для того что бы комбинацией клавиш оборачивать выделенный текст в блок кода с определенным языком надо создать шаблон со следующим содержимым (в шаблоне полностью весь блок вместе с \` ):

```python
<% tp.file.selection() %>
```

IШаблон забиндить в templater и установить на кнопку.
