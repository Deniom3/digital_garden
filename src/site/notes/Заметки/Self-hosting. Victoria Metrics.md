---
{"dg-publish":true,"permalink":"/zametki/self-hosting-victoria-metrics/","created":"2024-10-06 22:59","updated":"2024-10-06T23:53:51+03:00"}
---

Программа позволяет собирать данные мониторинга используя api и передавать их для дальнейшего использования в формате prometheus

Репозиторий: https://github.com/VictoriaMetrics/VictoriaMetrics

Документация: [403 Forbidden](https://docs.victoriametrics.com/#operation)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/victoria-metrics/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  victoria-metrics:
    image: victoriametrics/victoria-metrics
    container_name: victoria-metrics
    volumes:
      - ./victoria-metrics:/victoria-metrics-data
    ports:
      - 8428:8428
```


</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
 >- [[Хобби/Домашняя лаборатория/Сервер Monitoring\|Сервер Monitoring]]

> [!info]-
> Примечание:: Сбор и обработка данных мониторинга
