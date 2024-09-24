---
{"dg-publish":true,"permalink":"/zametki/self-hosting-crowd-sec/","created":"2024-07-31 22:40","updated":"2024-09-24T22:59:47+03:00"}
---

Файрволл для защиты серверов, альтернатива [[Заметки/Self-hosting. Fail2Ban\|Fail2Ban]]. Наиболее простой способ интеграции в связке с [[Заметки/Self-hosting. Traefik\|Traefik]] необходимо использовать конфигурацию [[Хобби/Конфиги/Traefik Crowdsek\|Traefik Crowdsek]].

Сайт:  [CrowdSec | Curated Threat Intelligence Powered by the Crowd](https://www.crowdsec.net/)

Репозиторий проекта: [GitHub - crowdsecurity/crowdsec: CrowdSec - the open-source and participative security solution offering crowdsourced protection against malicious IPs and access to the most advanced real-world CTI.](https://github.com/crowdsecurity/crowdsec)

Основные команды для работы [[Хобби/Команды и настройки/Настройка CrowdSec в докере\|Настройка CrowdSec в докере]]

 ### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/crowd-sec/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
Предназначен для использования в связке с [[Заметки/Self-hosting. Traefik\|Traefik]]

Для подключения к Traefik bouncer необходимо сгенерировать токен ключ командой:
```shell
docker exec crowdsec cscli bouncers add bouncer-traefik
```

```yaml
services:
  crowdsec:
    image: crowdsecurity/crowdsec:latest
    container_name: crowdsec
    environment:
      GID: "${GID-1000}"
      COLLECTIONS: "crowdsecurity/linux crowdsecurity/traefik"
    volumes:
      - ./acquis.yaml:/etc/crowdsec/acquis.yaml
      - ./db:/var/lib/crowdsec/data/
      - ./config:/etc/crowdsec/
      - /home/deniom/docker/traefik/logs:/var/log/traefik/:ro
    ports:
      - 6060:6060
    networks:
      - proxy
    security_opt:
      - no-new-privileges:true
    restart: unless-stopped

  bouncer-traefik:
    image: docker.io/fbonalair/traefik-crowdsec-bouncer:latest
    container_name: bouncer-traefik
    environment:
      CROWDSEC_BOUNCER_API_KEY: <ApiKey>
      CROWDSEC_AGENT_HOST: crowdsec:8080
    networks:
      - proxy
    depends_on:
      - crowdsec
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true

networks:
  proxy:
    external: true
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Сетевой фаервол для реверс прокси

> [!todo]-
> - [x] Настроить сбор статистики для CrowdSek ✅ 2024-09-12
> - [<] Изучить вопрос настройки плагинов под каждый сайт типа Home Assistant или Gitea ➕ 2024-09-14
