---
{"dg-publish":true,"dg-path":"Команды и настройки/Автоматическое обновление списков обхода блокировок для Keenetic.md","permalink":"/komandy-i-nastrojki/avtomaticheskoe-obnovlenie-spiskov-obhoda-blokirovok-dlya-keenetic/","updated":"2024-09-03T15:59:30+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
### Скрипт загрузки

> [!caution]
> Скрипт расчитан на работу только с Ipv4. Для использования с v6 необходимо скорректировать приписку bypass заменив на bypass,bypass6

[[Заметки/Списки обхода блокировок для разных вариантов реализации\|Списки обхода блокировок для разных вариантов реализации]]

Для автоматического обновления списка обхода блокировок можно использовать следующий скрипт:
```sh
#!/bin/sh

# URL проекта на GitHub, содержащего список сайтов
GITHUB_URL="https://raw.githubusercontent.com/itdoginfo/allow-domains/main/Russia/inside-raw.lst"

# Локальный файл для сохранения результатов
OUTPUT_FILE="/opt/etc/AdGuardHome/ipset.conf"

# Локальный файл с вашим списком сайтов
LOCAL_FILE="/opt/etc/AdGuardHome/my-domains-list.conf"

# Создаем временный файл для загрузки списка сайтов
TEMP_FILE=$(mktemp)

# Загружаем список сайтов из GitHub
curl -s $GITHUB_URL -o $TEMP_FILE

# Проверяем успешность загрузки
if [ $? -ne 0 ]; then
    echo "Ошибка при загрузке списка сайтов"
    rm -f $TEMP_FILE
    exit 1
fi

# Очищаем файл перед записью новых данных
> $OUTPUT_FILE

# Сначала обрабатываем локальный файл и добавляем /bypass
if [ -f "$LOCAL_FILE" ]; then
    while IFS= read -r site || [ -n "$site" ]; do
        # Удаляем символы CR (возврат каретки)
        site=$(echo "$site" | tr -d '\r')
        # Пропускаем пустые строки
        [ -z "$site" ] && continue
        echo "$site/bypass" >> $OUTPUT_FILE
    done < $LOCAL_FILE
else
    echo "Локальный файл $LOCAL_FILE не найден"
fi

# Затем обрабатываем загруженный файл и добавляем /bypass
while IFS= read -r site || [ -n "$site" ]; do
    # Удаляем символы CR (возврат каретки)
    site=$(echo "$site" | tr -d '\r')
    # Пропускаем пустые строки
    [ -z "$site" ] && continue
    echo "$site/bypass" >> $OUTPUT_FILE
done < $TEMP_FILE

# Удаляем временный файл
rm -f $TEMP_FILE

echo "Файл $OUTPUT_FILE успешно обновлен"
```

Скрипт поддерживает использование [[Заметки/Список дополнительных сайтов для обхода блокировок Keenetic\|дополнительного списка своих сайтов]] которые должны быть размещены в файле `/opt/etc/AdGuardHome/my-domains-list.conf`

Создайте файл `/opt/etc/AdGuardHome/my-domains-list.conf`

Для работы необходимо разместить скрипт по пути `/opt/etc/AdGuardHome/update_sites.sh`

И сделать его исполняемым:
```shell
chmod +x update_sites.sh
```

Для ручного запуска:
```shell
./update_sites.sh
```

В результате получим список подобного вида
![Автоматическое обновление списков обхода блокировок для Keenetic.png|300](/img/user/%D0%98%D1%81%D1%85%D0%BE%D0%B4%D0%BD%D0%B8%D0%BA%D0%B8/%D0%90%D0%B2%D1%82%D0%BE%D0%BC%D0%B0%D1%82%D0%B8%D1%87%D0%B5%D1%81%D0%BA%D0%BE%D0%B5%20%D0%BE%D0%B1%D0%BD%D0%BE%D0%B2%D0%BB%D0%B5%D0%BD%D0%B8%D0%B5%20%D1%81%D0%BF%D0%B8%D1%81%D0%BA%D0%BE%D0%B2%20%D0%BE%D0%B1%D1%85%D0%BE%D0%B4%D0%B0%20%D0%B1%D0%BB%D0%BE%D0%BA%D0%B8%D1%80%D0%BE%D0%B2%D0%BE%D0%BA%20%D0%B4%D0%BB%D1%8F%20Keenetic.png)

### Настройка автоматического обновления

Для автоматического обновления каждый день в 6 утра добавим событие в cron, но сначала установим его:

```shell
opkg update
opkg install cron

/opt/etc/init.d/S10cron start

nano /opt/etc/crontab
```

Добавить строку:
```
0 6 * * * root /opt/etc/AdGuardHome/update_sites.sh
```
