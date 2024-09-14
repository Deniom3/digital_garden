---
{"dg-publish":true,"permalink":"/zametki/monitoring-adguard-home-s-ispolzovaniem-prometheus/","created":"2024-09-14 23:35","updated":"2024-09-14T23:47:50+03:00"}
---

У AdguardHome нет полноценного мониторинга, но есть возможность получения данных об установке через api. Для получения данных и конвертации их в представление для Prometheus можно использовать экспортер конвертер.

Репозиторий: [GitHub - henrywhitaker3/adguard-exporter](https://github.com/henrywhitaker3/adguard-exporter)

## Настройка экспортера

Файл развертывания в виде docker compose:


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/adguard-ertxpoer/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  adguard-ertxpoer:
    container_name: adguard-ertxpoer
    environment:
      - ADGUARD_SERVERS=http://192.168.0.1:8080
      - ADGUARD_USERNAMES=none
      - ADGUARD_PASSWORDS=none
    ports:
      - 9618:9618
    image: ghcr.io/henrywhitaker3/adguard-exporter:latest
```


</div></div>


> [!note] Особенность установки
> Параметры `ADGUARD_USERNAMES` и `ADGUARD_PASSWORDS` обязательные для запуска. Если авторизация не используется в установке Adguard Home то значения могут быть любыми.

> [!attention] Важно
> Адрес доступа к веб интерфейсу Adguard Home должен быть казан в полном виде с указанием протокола и порта в переменную `ADGUARD_SERVERS`

## Настройка Prometheus

Добавить в конфигурационный файл [[Заметки/Система мониторинга Prometheus\|Prometheus]] новую задачу сбора данных:
```yaml
#Adguard
  - job_name: 'adguard'
    static_configs:
      - targets: ['192.168.0.135:9618']
```
Адрес указывает на экспортер а не на конкретную установку Adguard.
## Мониторинг в Grafana

![Мониторинг AdguardHome с использованием Prometheus.png](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%9C%D0%BE%D0%BD%D0%B8%D1%82%D0%BE%D1%80%D0%B8%D0%BD%D0%B3%20AdguardHome%20%D1%81%20%D0%B8%D1%81%D0%BF%D0%BE%D0%BB%D1%8C%D0%B7%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D0%B5%D0%BC%20Prometheus.png)

Можно использовать следующий дашборд для [[Заметки/Программа визуализации статистики Grafana\|Grafana]]:
![[GrafanaAdguardHome.json]]

Описание дашборда на официальном сайте: [AdGuard Home Exporter | Grafana Labs](https://grafana.com/grafana/dashboards/20799-adguard-home-exporter/)

---
> [!urls]- Упоминания:
> - [[Заметки/Локальный DNS сервер с блокировкой рекламы Adguard Home\|Adguard Home]]
> - [[Заметки/Программа визуализации статистики Grafana\|Grafana]]
> - [[Заметки/Система мониторинга Prometheus\|Prometheus]]