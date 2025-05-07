---
{"dg-publish":true,"permalink":"/zametki/rezervnoe-kopirovanie-routera-keenetik/","created":"2025-04-22 21:58","updated":"2025-05-06T15:19:45+03:00"}
---

### Описание

Если на роутер установлена дополнительная система [[Заметки/Операционная система для роутеров Entware\|Entware]] тогда можно использовать скрипт. В рамках резервного копирования создается копия настроек роутера и его прошивка, а так же архив для автоматического развертывания Entware со всеми настройками. По результатам резервного копирования формируется уведомление.

Скрипт собран на основе тему с форума: [Периодическое резервное копирование entware, конфига и прошивки - Страница 3 - Каталог готовых решений Opkg - Keenetic Community](https://forum.keenetic.ru/topic/1356-периодическое-резервное-копирование-entware-конфига-и-прошивки/page/3/)

### Скрипт

```
#!/opt/bin/bash

set -x

export PATH=/opt/bin:/opt/sbin:/bin:/usr/bin:/usr/sbin:/sbin

# =========== НАСТРОЙКИ ============
CONFIG_FILE="/opt/etc/backup_config.env"

if [[ ! -f $CONFIG_FILE ]]; then
    echo "❌ Конфигурационный файл $CONFIG_FILE отсутствует!"
    exit 1
fi

# Импорт конфигурации
source "$CONFIG_FILE"

DATE=$(date +%Y-%m-%d_%H-%M-%S)
ARCHIVE_NAME="keenetic_backup_${DATE}.tar.gz"
ARCHIVE_PATH="${LOCAL_TMP_DIR}/${ARCHIVE_NAME}"

# Логирование в syslog
log() {
    logger "[BACKUP]: $1"
    echo "$(date +%Y-%m-%d_%H:%M:%S) $1" | tee -a "$LOG_FILE"
}

# Отправка Telegram уведомлений
send_telegram() {
    MESSAGE=$(echo -e "$1" | sed 's/$/%0A/g')
    curl -s -X POST "https://api.telegram.org/bot$TELEGRAM_TOKEN/sendMessage" \
        -d chat_id="$TELEGRAM_CHAT_ID" \
        -d message_thread_id="$TELEGRAM_THREAD_ID" \
        -d parse_mode="Markdown" \
        -d text="$MESSAGE"
}

# Очистка локальной директории
cleanup_local() {
    log "🧹 Очистка локальной директории $LOCAL_BACKUP_DIR..."
    rm -rf "$LOCAL_BACKUP_DIR"/* || log "⚠️ Не удалось очистить $LOCAL_BACKUP_DIR."
    log "🧹 Очистка локальной директории $LOCAL_TMP_DIR..."
    rm -rf "$LOCAL_TMP_DIR"/* || log "⚠️ Не удалось очистить $LOCAL_TMP_DIR."
}

# Бэкап Entware
backup_entware() {
    log "📦 Создание бэкапа Entware..."
    tar cvzf ${LOCAL_BACKUP_DIR}/entware_backup_${DATE}.tar.gz -C /opt . > /dev/null
}

# Бэкап прошивки
backup_firmware() {
    log "🔧 Создание бэкапа прошивки..."
    REL=$(ndmc -c 'show version' | grep 'release:' | awk '{print $2}')
    ndmc -c "copy flash:/firmware ${FW_BACKUP_DIR}/firmware-${REL}_${DATE}.bin"
    log "✅ Создан бэкап прошивки: ${FW_BACKUP_DIR}/firmware-${REL}_${DATE}.bin"
}

# Бэкап конфига
backup_config() {
    log "🛠️ Создание бэкапа конфигурации..."
    ndmc -c "show running-config" > ${LOCAL_BACKUP_DIR}/config_${DATE}.cfg
    log "✅ Создан бэкап конфигурации: ${LOCAL_BACKUP_DIR}/config_${DATE}.cfg"
}

# Архивация бэкапов
create_archive() {
    log "📦 Создание архива $ARCHIVE_NAME..."
    tar cvzf ${ARCHIVE_PATH} -C ${LOCAL_BACKUP_DIR} . > /dev/null || return 1
    log "✅ Архив создан: $ARCHIVE_PATH"
}

# Копирование на Nextcloud
upload_to_nextcloud() {
    log "☁️ Копирование $ARCHIVE_PATH на Nextcloud..."
    curl -T ${ARCHIVE_PATH} -u ${NEXTCLOUD_USER}:${NEXTCLOUD_PASS} {$BACKUP_DIR_URL}/${ARCHIVE_NAME}
	log "✅ Архив отправлен на Nextloud: $BACKUP_DIR_URL/${ARCHIVE_NAME}"
}

# Ротация бэкапов
rotate_backups() {
    log "📁 Получаем список бэкапов в Nextcloud..."

    # Получаем список бэкапов
    file_list=$(curl -s -u "$NEXTCLOUD_USER:$NEXTCLOUD_PASS" -X PROPFIND "$BACKUP_DIR_URL" -H "Depth: 1" \
        | grep -oE "/remote.php/dav/files/[^<]*keenetic_backup_[^<]*\.tar\.gz")

    if [ -z "$file_list" ]; then
        log "ℹ️ Нет доступных бэкапов для ротации."
        return 0
    fi

    echo "$file_list" | sort > /tmp/backups_sorted.txt
    total=$(wc -l < /tmp/backups_sorted.txt)
	
	# Удалим на один больше так как делаем новый бекап
    target_keep=$((KEEP_LAST - 1))
	
    if [ "$total" -le "$target_keep"]; then
        log "👍 Пока удалять нечего — всего $total бэкапов."
        return 0
    fi

    delete_count=$((total - target_keep))
    log "🗑️ Удаляем $delete_count старых бэкапов..."

    head -n "$delete_count" /tmp/backups_sorted.txt | while read -r path; do
        filename=$(basename "$path")
        full_url="${BACKUP_DIR_URL}/${filename}"
        echo "➡️ Удаление: $full_url"
        curl -s -u ${NEXTCLOUD_USER}:${NEXTCLOUD_PASS} -X DELETE ${full_url}
    done

    log "✅ Ротация завершена."
}

# =========== ОСНОВНОЙ СКРИПТ ============
main() {
    log "=== 🚀 Начало резервного копирования ==="
    cleanup_local
    mkdir -p "$LOCAL_BACKUP_DIR"
	mkdir -p "$LOCAL_TMP_DIR"

    # Выполнение шагов
    backup_entware
    backup_firmware
    backup_config
	
	# Упаковка в единый архив
    create_archive
	
    # Ротация
    rotate_backups

    # Копирование на хранилища
    upload_to_nextcloud

    # Отправка отчета
    send_report

    log "🧹 Очистка локальной директории $LOCAL_TMP_DIR..."
    rm -rf "$LOCAL_TMP_DIR"/* || log "⚠️ Не удалось очистить $LOCAL_TMP_DIR."
	
	send_telegram "✅ Создана [резервная копия]($BACKUP_DIR_URL/$ARCHIVE_NAME) настроек роутера."
	
    log "=== ✅ Завершено ==="
}

main
```

### Параметры
Задаются в отдельном файле `backup_config.env`
```
# Конфигурационный файл для скрипта резервного копирования Keenetic

# Количество хранимых на сервере копий
KEEP_LAST=3 

# Путь до каталога с бэкапами
LOCAL_BACKUP_DIR="/tmp/mnt/Router/backups"

# Путь до каталога с бэкапамом прошивки
FW_BACKUP_DIR="Router:/backups"

# Параметры для облаков
NEXTCLOUD_USER="user"
NEXTCLOUD_PASS="password"
BACKUP_DIR_URL="webdav_url"


# Параметры Telegram
TELEGRAM_TOKEN="token"   # Ваш токен Telegram бота
TELEGRAM_CHAT_ID="CHAT_ID"             # ID чата для получения уведомлений
TELEGRAM_THREAD_ID="THREAD_ID"

# Логи
LOG_FILE="/tmp/mnt/Router/backup.log"  # Путь к файлу для логов

# Временная папка
LOCAL_TMP_DIR="/tmp/mnt/Router/tmp"
```

`FW_BACKUP_DIR` задается как <ИмяРазделаРоутера>:/<ПапкаХранения>
`BACKUP_DIR_URL` - адрес для webdav для nextloud выглядит так:
```
https://cloud.domein.ru/remote.php/dav/files/<username>/<Folder>
```
`TELEGRAM_THREAD_ID` можно не использовать но надо удалить строку из скрипта:
```
-d message_thread_id="$TELEGRAM_THREAD_ID" \
```

### Запуск

Скрипт `backup` надо разместить по пути:
```
 opt/usr/bin/
```
Файл параметров `backup_config.env` по пути:
```
/opt/etc
```

Сделать скрипт исполняемым:
```
chmod +x /opt/usr/bin/backup
```
И добавить в cron например так:
```
0 11 * * * /opt/bin/bash /opt/usr/bin/backup
```

### Настройка отправки и хранения

Архив с результатами резервного копирования отправляются на сервер nextcloud через webdav с использованием curl.

Можно заменить на использование [[Заметки/Self-hosting. rclone\|rclone]]  по примеру:
```
# Копирование на Google Drive
upload_to_google_drive() {
    log "☁️ Копирование $ARCHIVE_PATH на Google Drive..."
    attempt_command "rclone copy $ARCHIVE_PATH $GDRIVE_REMOTE" "Копирование на Google Drive"
}

# Ротация бэкапов
rotate_backups() {
    log "♻️ Ротация бэкапов старше $RETENTION_DAYS дней..."
    rclone delete --min-age "${RETENTION_DAYS}d" "$GDRIVE_REMOTE" && log "✅ Ротация на GDrive завершена."
    rclone delete --min-age "${RETENTION_DAYS}d" "$MEGA_REMOTE" && log "✅ Ротация на Mega завершена."
}
```

> [!bug] Важно
> Реализация rclone на роутере очень сильно нагружает систему что может привести к сбою работы других программ, лучше использовать curl.

### Восстановление

В архиве содержится 3 файла:
`config_*` - настройки роутера
`firmware-*` - прошивка роутера
`entware_backup_*.tar.gz` - архив для установки Entware со всеми внесенными изменениями, для восстановления на новый носитель положить в папку install вместо установочного Entware.

---
> [!urls]- Упоминания:
> - [[Заметки/Роутеры Keenetic\|Роутеры Keenetic]]
