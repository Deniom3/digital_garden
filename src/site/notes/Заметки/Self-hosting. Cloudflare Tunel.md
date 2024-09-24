---
{"dg-publish":true,"permalink":"/zametki/self-hosting-cloudflare-tunel/","created":"2024-08-18 01:15","updated":"2024-09-24T23:02:28+03:00"}
---

Cloudfkare Tunel - это zerotier подключение для публикации сервисов и сайтов без использования белого ip адреса. Имеет ряд ограничений и особенностей таких как ограничение разовой передачи данных (100 Мб за файл на бесплатном плане) более низкая надежность и легкая блокируемость.

Документация: [Cloudflare Tunnel | Cloudflare Zero Trust docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/)

Инструкции:
- [[Статьи/Получите доступ к локальной установке Immich через Интернет, используя свой собственный домен\|Инструкция по настройке на примере Immich]]
- [[Заметки/Настройка Cloudflare Tunel вместе с Traefik\|Настройка Cloudflare Tunel вместе с Traefik]]
### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">

<div class="markdown-embed-title">

# Cloudflare Tunel

</div>



Назад:: [[Хобби/Docker compose/Docker Compose\|список шаблонов]]

---
Докер файл написан из расчет на подключение к Traefik в сети докер proxy. Токен выдается при создании нового подключения в cloudflare

```yaml
services:
  cloudflare:
    image: cloudflare/cloudflared:latest
    container_name: cloudflare
    command: tunnel --no-autoupdate run
    environment:
      - TUNNEL_TOKEN=$TUNNEL_TOKEN
    networks:
      - proxy
    restart: unless-stopped

networks:
  proxy:
    external: true
```



</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]

> [!info]-
> Примечание:: Публикация сервиса в интернете без белого IP
