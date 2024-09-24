---
{"dg-publish":true,"dg-path":"Конфиги/Caddyfile.md","permalink":"/konfigi/caddyfile/","updated":"2024-09-24T23:21:43+03:00"}
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

adguard.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.1:8080
	}
}

perenio.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.115:80
	}
}

homed.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.115:8080
	}
}

sonarr.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.134:8989
	}
}

radarr.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.134:7878
	}
}

qbittorrent.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.134:8080
	}
}

casaos.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.132:8080
	}
}

calibre.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.132:8083
	}
}

readeck.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.132:8082
	}
}

gitea.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.132:3002
	}
}

grafana.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.135:3000
	}
}

influxdb.{env.DOMAIN} {
	import local
	import cloudflare
	
	log
	
	handle @allowed {
		reverse_proxy 192.168.0.135:8086
	}
}

```