---
{"dg-publish":true,"dg-path":"Конфиги/Caddyfile.md","permalink":"/konfigi/caddyfile/","updated":"2025-06-10T01:23:56+03:00"}
---

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