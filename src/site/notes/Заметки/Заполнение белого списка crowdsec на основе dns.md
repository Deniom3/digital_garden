---
{"dg-publish":true,"permalink":"/zametki/zapolnenie-belogo-spiska-crowdsec-na-osnove-dns/","created":"2025-10-26 22:06","updated":"2025-10-26T22:23:44+03:00"}
---

Если клиент имеет динамический ip адрес и не может быть добавлен в список исключений из блокировки [[Заметки/Self-hosting. CrowdSec\|CrowdSec]] можно воспользоваться механизмом ddns, такими как cloudflare или dickdns.

Инструкция по [[Статьи/Настройка ddns openwrt\|Настройка ddns на open wrt]]

> [!bug]
> В самом crowdsec существует механизм для фильтрации по dns запросам, но почему то полноценной работы добиться не удалось. Подробно описано в [Whitelists \| CrowdSec](https://docs.crowdsec.net/u/getting_started/post_installation/whitelists/#dynamic-ip-address)

> [!note]
> Данный механизм очень помог для решения проблем попадания в блокировку клиентов netbird. Сам netbird имеет ряд особенностей которые приводят к ложно положительному срабатыванию защиты.
> 
> Альтернативой может быть полное отключение проверки для [[Заметки/Self-hosting. Netbird\|Netbird]].

Альтернативное решение использовать скрипт который выполняет проверку dns адреса и дополняет список с очисткой устаревших записей.

Предварительно необходимо создать список:
```sh
cscli allowlist create my_allowlist -d 'created from the docs'
```

Скрипт предназначен для использования с crowdsec установленном в docker. В процессе выполнения происходит проверка указанных доменов и поиск в указанном списке уже существующих записей для домена. В случае изменения выполняется замена записей и отправка сообщения телеграмм.

Скрипт:
```sh
#!/usr/bin/env bash
set -euo pipefail

# --- Настройки ---
CROWDSEC_CONTAINER="crowdsec"
ALLOWLIST_NAME="netbird"
DOMAINS=("11111.duckdns.org" "2222.duckdns.org")
LOG_FILE="/home/deniom/docker/crowdsec/crowdsec_ddns_allowlist.log"
COMMENT_PREFIX="Netbird DDNS"

# --- Telegram ---
TELEGRAM_BOT_TOKEN=""
TELEGRAM_CHAT_ID=""
THREAD_ID=""  # ID темы (thread) в супергруппе

log() {
    echo "$(date -u) $*" | tee -a "$LOG_FILE"
}

send_telegram() {
    local message="$1"
    curl -s -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
        -d chat_id="$TELEGRAM_CHAT_ID" \
        -d message_thread_id="$THREAD_ID" \
        -d parse_mode="Markdown" \
        -d disable_notification=true \
        -d text="$message" >/dev/null 2>&1
}

# Добавление IP с комментарием
add_to_allowlist() {
    local ip="$1"
    local domain="$2"
    log "Добавляю $ip в allowlist $ALLOWLIST_NAME для $domain..."
    if docker exec "$CROWDSEC_CONTAINER" cscli allowlists add "$ALLOWLIST_NAME" "$ip" -d "$COMMENT_PREFIX $domain" >/dev/null 2>&1; then
        log "Успешно добавлен $ip"
        send_telegram "*ALLOWLIST UPDATE:* Добавлен IP \`$ip\` для \`$domain\`"
    else
        log "ОШИБКА: не удалось добавить $ip"
        send_telegram "*ALLOWLIST ERROR:* Не удалось добавить IP \`$ip\` для \`$domain\`"
    fi
}

# Удаление IP
delete_from_allowlist() {
    local ip="$1"
    local domain="$2"
    log "Удаляю устаревший IP $ip из allowlist..."
    if docker exec "$CROWDSEC_CONTAINER" cscli allowlists remove "$ALLOWLIST_NAME" "$ip" >/dev/null 2>&1; then
        log "Успешно удалён $ip"
        send_telegram "*ALLOWLIST UPDATE:* Удалён устаревший IP \`$ip\` для \`$domain\`"
    else
        log "ОШИБКА: не удалось удалить $ip"
        send_telegram "*ALLOWLIST ERROR:* Не удалось удалить IP \`$ip\` для \`$domain\`"
    fi
}

# --- Основной цикл ---
for domain in "${DOMAINS[@]}"; do
    log "Обрабатываю домен: $domain"

    # Получаем текущий IPv4
    ip=$(dig +short "$domain" | grep -E '^[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+

---
> [!urls]- Упоминания:
> -  | head -n1)
    if [[ -z "$ip" ]]; then
        log "Не удалось разрешить $domain"
        send_telegram "*ALLOWLIST WARNING:* Не удалось разрешить домен \`$domain\`"
        continue
    fi
    log "Текущий IP для $domain: $ip"

    # Получаем все старые IP для этого домена через комментарий
    existing_ips=$(docker exec "$CROWDSEC_CONTAINER" cscli allowlists inspect "$ALLOWLIST_NAME" 2>/dev/null \
        | awk -v prefix="$COMMENT_PREFIX $domain" 'NR>7 && $0 ~ prefix {print $1}')

    if [[ -n "$existing_ips" ]]; then
        log "Найденные старые IP для $domain: $existing_ips"
    else
        log "Старые IP для $domain не найдены"
    fi

    # Удаляем устаревшие IP (все кроме текущего)
    for old_ip in $existing_ips; do
        if [[ "$old_ip" != "$ip" ]]; then
            delete_from_allowlist "$old_ip" "$domain"
        else
            log "Текущий IP $ip уже в списке, не удаляем"
        fi
    done

    # Добавляем текущий IP, если его ещё нет
    if ! docker exec "$CROWDSEC_CONTAINER" cscli allowlists check "$ip" 2>/dev/null | grep -q "is allowlisted"; then
        add_to_allowlist "$ip" "$domain"
    else
        log "$domain ($ip) уже есть в allowlist"
    fi

    log "Обработка домена $domain завершена"
done
```

---
> [!urls]- Упоминания:
> - 