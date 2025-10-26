---
{"dg-publish":true,"permalink":"/zametki/dobavlenie-servera-retranslyatora-netbird/","created":"2025-10-26 22:49","updated":"2025-10-27T02:22:49+03:00"}
---

При построении self hosting сети [[Заметки/Self-hosting. Netbird\|Netbird]] может возникнуть ситуация при которой некоторые клиенты не могут связаться между собой из за особенности сети. В таких случаях используется relay сервер.

При стандартной установке netbird один сервер relay устанавливается вместе с основной панелью, но может возникнуть ситуация при которой прямой доступ по протоколу wireguard между клиентом и основным сервером невозможен. 

Докер конфигурация для развертывания:


<div class="transclusion internal-embed is-loaded"><a class="markdown-embed-link" href="/docker-compose/netbird-relay/" aria-label="Open link"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="svg-icon lucide-link"><path d="M10 13a5 5 0 0 0 7.54.54l3-3a5 5 0 0 0-7.07-7.07l-1.72 1.71"></path><path d="M14 11a5 5 0 0 0-7.54-.54l-3 3a5 5 0 0 0 7.07 7.07l1.71-1.71"></path></svg></a><div class="markdown-embed">





```yaml
services:
  relay:
    image: netbirdio/relay:latest
    restart: unless-stopped
    container_name: netbird-relay
    environment:
    - NB_LOG_LEVEL=info
    - NB_LISTEN_ADDRESS=:33080
    - NB_EXPOSED_ADDRESS=domein.ru:33080
    # todo: change to a secure secret
    - NB_AUTH_SECRET=<secret>
    ports:
      - 33080:33080
    logging:
      driver: "json-file"
      options:
        max-size: "500m"
        max-file: "2"
```


</div></div>


Секрет размещен в файле `management.json` основной конфигурации

```
    "Relay": {
        "Addresses": [
            "rel://netbird.domein.ru:33080",
            "rel://netbird2.domein.ru:33080"
        ],
        "CredentialsTTL": "24h0m0s",
        "Secret": "<secret>"
    },
```

Так же в файл `management.json` необходимо добавить адрес нового relay сервера

---
> [!urls]- Упоминания:
> - 