---
{"dg-publish":true,"permalink":"/zametki/self-hosting-caddy/","created":"2024-07-03 19:51","updated":"2025-11-07T10:18:56+03:00"}
---

Просто http сервер для публикации сайтов и организации реверс прокси. Для настройки используется файл конфигурации Caddyfile с json описанием сайтов. Имеет большое количество модулей и расширений, может гибко настраиваться.

Не имеет графического интерфейса, вся настройка через файл конфигурации. Поддерживает dns chelenge.

Официальный сайт: [Caddy - The Ultimate Server with Automatic HTTPS](https://caddyserver.com/)
Проект на GitHub  [GitHub - caddyserver/caddy: Fast and extensible multi-platform HTTP/1-2-3 web server with automatic HTTPS](https://github.com/caddyserver/caddy)

### Пример docker compose файла:

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/caddy/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">

<div class="markdown-embed-title">

# Докер файл для запуска

</div>




```yml
services:
  caddy:
    image: ghcr.io/deniom3/caddy-cloudflare-transform:latest
    container_name: caddy
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
    ports:
      - 80:80
      - 443:443 
    environment:
      ACME_AGREE: true
      CLOUDFLARE_API_TOKEN: <CLOUDFLARE_API_TOKEN>
      CLOUDFLARE_EMAIL: <CLOUDFLARE_EMAIL>
      DOMAIN: deniom.ru
    volumes:
      - ./data:/data
      - ./config:/config
      - ./logs:/logs
      - ./Caddyfile:/etc/caddy/Caddyfile  
```

> [!note]-
> Подобно разобрано в [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|статье]]
> 
> Настройки проксирования указываются в [[Хобби/Конфиги/Caddyfile\|файле]]


</div></div>

### Пример конфиг файла 

<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/konfigi/caddyfile/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">




Пример настроек Caddy для использования как реверс прокси в домашней сети. Подробная настройка рассмотрена в [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|Настройка реверс прокси Caddy и Fail2Ban в docker]].

```Caddyfile
(common) {
	header /* {
	-Server
	}
}

(cloudflare) {
	import common
	tls { 
		dns cloudflare {env.CLOUDFLARE_API_TOKEN}
	}
}

(local) {
	@allowed remote_ip 192.168.0.0/24
	handle {
		respond 401
	}
}

{
	log access-log {
		include http.log.access
		output file /logs/access.log {
		  roll_keep_for 48h
		}
		format transform `{ts} {request>headers>X-Forwarded-For>[0]:request>remote_ip} {request>host} {request>method} {request>uri} {status}` {
			time_format "02/Jan/2006:15:04:05"
		}
	}
}

home-assistant.{env.DOMAIN} {
	import cloudflare
	
	log
	
	reverse_proxy 192.168.0.125:8123
}

vaultwarden.{env.DOMAIN} {
	import cloudflare
	
	log
	
	reverse_proxy 192.168.0.132:10380 {
		header_up X-Real-IP {remote_host}
	}
}

nextcloud.{env.DOMAIN} {
	import cloudflare
	
	log
	
	reverse_proxy 192.168.0.133:11000
}

immich.{env.DOMAIN} {
	import cloudflare
	
	log
	
	reverse_proxy 192.168.0.136:2283
}

jackett.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.134:9117
	}
}

plex.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.134:32400
	}
}

portainer.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.132:9000
	}
}
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Статьи/Настройка реверс прокси Caddy и Fail2Ban в docker\|Настройка реверс прокси Caddy и Fail2Ban в docker]]

> [!description]- Примечание
> Примечание:: Легкий реверс прокси
