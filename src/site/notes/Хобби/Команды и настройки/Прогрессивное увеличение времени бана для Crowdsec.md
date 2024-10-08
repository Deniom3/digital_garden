---
{"dg-publish":true,"dg-path":"Команды и настройки/Прогрессивное увеличение времени бана для Crowdsec.md","permalink":"/komandy-i-nastrojki/progressivnoe-uvelichenie-vremeni-bana-dlya-crowdsec/","tags":[""],"updated":"2024-10-09T00:45:06+03:00"}
---


Для динамического увеличения продолжительности бана угрозы обнажаемой  crowdsek необходимо изменить в файле `/crowdsek/config/profiles.yaml` строку параметра:
```shell
...
duration_expr: "Sprintf('%dh', (GetDecisionsCount(Alert.GetValue()) + 1) * 8)"
...
```

означает следующее:
- `GetDecisionsCount(Alert.GetValue())` — это функция, которая возвращает количество предыдущих решений (банов) для данного источника (например, IP-адреса).
- `+ 1` — добавляет 1 к количеству предыдущих решений, чтобы учитывать текущее решение.
- `* 8` — умножает общее количество решений на 8, что означает, что время бана будет увеличиваться на 8 часа за каждое предыдущее решение.
- `Sprintf('%dh', ...)` — форматирует результат в строку, представляющую количество часов.

Подробнее в документации: [Format | CrowdSec](https://docs.crowdsec.net/docs/next/profiles/format/#duration_expr)