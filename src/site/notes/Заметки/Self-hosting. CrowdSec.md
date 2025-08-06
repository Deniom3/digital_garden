---
{"dg-publish":true,"permalink":"/zametki/self-hosting-crowd-sec/","created":"2024-07-31 22:40","updated":"2025-08-06T20:56:57+03:00"}
---

Файрволл для защиты серверов, альтернатива [[Заметки/Self-hosting. Fail2Ban\|Fail2Ban]]. Наиболее простой способ интеграции в связке с [[Заметки/Self-hosting. Traefik\|Traefik]] необходимо использовать конфигурацию [[Хобби/Конфиги/Traefik Crowdsec\|Traefik Crowdsec]].

Сайт:  [CrowdSec | Curated Threat Intelligence Powered by the Crowd](https://www.crowdsec.net/)

Репозиторий проекта: [GitHub - crowdsecurity/crowdsec: CrowdSec - the open-source and participative security solution offering crowdsourced protection against malicious IPs and access to the most advanced real-world CTI.](https://github.com/crowdsecurity/crowdsec)

Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/crowd-sec/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  crowdsec:
    image: crowdsecurity/crowdsec:latest
    container_name: crowdsec
    environment:
      GID: "${GID-1000}"
      COLLECTIONS: "crowdsecurity/linux crowdsecurity/traefik crowdsecurity/appsec-virtual-patching crowdsecurity/appsec-generic-rules firix/authentik Dominic-Wagner/vaultwarden gauth-fr/immich aidalinfo/couchdb LePresidente/gitea"
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - ./acquis.yaml:/etc/crowdsec/acquis.yaml
      - ./db:/var/lib/crowdsec/data/
      - ./config:/etc/crowdsec/
      - /home/deniom/docker/traefik/logs:/var/log/traefik/:ro
    ports:
      - 6060:6060
      - 8081:8080
    networks:
      - proxy
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped

networks:
  proxy:
    external: true
```



</div></div>


Основные настройки описаны отдельно:
- [[Хобби/Команды и настройки/Команды работы с Crowdsec в docker\|Команды работы с Crowdsec в docker]]
- [[Хобби/Команды и настройки/Прогрессивное увеличение времени бана для Crowdsec\|Прогрессивное увеличение времени бана для Crowdsec]]
- [[Заметки/Настройка Crowdsec в связке с Traefik\|Настройка Crowdsec в связке с Traefik]]
- [[Заметки/Защита отдельных программ в docker с использованием Crowdsec\|Защита отдельных программ в docker с использованием Crowdsec]]
- [[Заметки/Локальная панель мониторинга Crowdsec\|Локальная панель мониторинга Crowdsec]]
- [[Заметки/Панель мониторинга Crowdsec на базе Grafana с использованием Victoria Metrics\|Панель мониторинга Crowdsec на базе Grafana с использованием Victoria Metrics]]
- [[Заметки/Мониторинг Crowdsec с использованием Prometheus\|Мониторинг Crowdsec с использованием Prometheus]]


# Компонент AppSec — CrowdSec WAF
[AppSec Component - CrowdSec WAF \| CrowdSec](https://doc.crowdsec.net/docs/next/appsec/intro/)


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Сервер Gateway\|Сервер Gateway]]
> - [[Заметки/Self-hosting. Traefik\|Traefik]]
> - [[Home Assistant\|Home Assistant]]

> [!description]- Примечание
> Примечание:: Сетевой фаервол для реверс прокси

> [!todo]-
> - [x] Настроить сбор статистики для CrowdSek ✅ 2024-09-12
>- [x] Изучить вопрос настройки плагинов под каждый сайт типа Home Assistant или Gitea ➕ 2024-09-14 ✅ 2024-10-06
>- [ ] Изучить новую компоненту crowdsec 🔽 ➕ 2025-08-06
