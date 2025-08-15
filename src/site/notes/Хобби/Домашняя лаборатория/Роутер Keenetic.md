---
{"dg-publish":true,"dg-path":"Домашняя лаборатория/Роутер Keenetic.md","permalink":"/domashnyaya-laboratoriya/router-keenetic/","updated":"2024-10-09T19:48:05+03:00"}
---

Основной роутер домашней лаборатории Keenetic Hopper.
К [[Заметки/Роутеры Keenetic\|роутеру]] подключена внешний SSD диск на 120 Gb подключенный во внешнем боксе на который установлена система [[Заметки/Операционная система для роутеров Entware\|Entware]] и [файл подкачки](https://help.keenetic.com/hc/ru/articles/360000184259-Как-подготовить-USB-накопитель-для-использования-в-качестве-хранилища-и-одновременно-расширения-объема-оперативной-памяти-интернет-центра) на 500Mb.

### Установленные программы:
<div><table class="dataview table-view-table"><thead class="table-view-thead"><tr class="table-view-tr-header"><th class="table-view-th"><span>Название</span><span class="dataview small-text">1</span></th><th class="table-view-th"><span>Примечание</span></th></tr></thead><tbody class="table-view-tbody"><tr><td><span><a data-tooltip-position="top" aria-label="Заметки/Self-hosting. Adguard Home.md" data-href="Заметки/Self-hosting. Adguard Home.md" href="Заметки/Self-hosting. Adguard Home.md" class="internal-link data-link-icon data-link-icon-after data-link-text" target="_blank" rel="noopener nofollow" data-link-tags="" data-link-type="note" data-link-path="Заметки/Self-hosting. Adguard Home.md" style="--data-link-type: note; --data-link-path: Заметки/Self-hosting. Adguard Home.md;">Adguard Home</a></span></td><td><span>Локальный DNS сервер с блокировкой рекламы</span></td></tr></tbody></table></div>

### Обход блокировок

Настроен обход блокировок по доменным именам по [[Статьи/Раздельное туннелирование трафика Adguard Keenetic\|инструкции]] используя [[Заметки/Список дополнительных сайтов для обхода блокировок Keenetic\|список]] адресов. Для изменения состава сайтов по которому происходит обход блокировок смотри [[Хобби/Команды и настройки/Добавление сайта в список для обхода блокировки Keneetic Adguard\|настройку]].

Так же реализована вспомогательная схема:
![[Схема VPN подключения в локальной.canvas|Схема VPN подключения в локальной]]

---
> [!urls]- Упоминания:
> - [[Хобби/Домашняя лаборатория/Структура домашней лаборатории\|Структура домашней лаборатории]]

> [!description]- Примечание
> Примечание:: Основной роутер

> [!todo]-
> - [-] Изучить настройку мониторинга через SNMP: [Настройка SNMP-сервера – Keenetic](https://help.keenetic.com/hc/ru/articles/115001198769-Настройка-SNMP-сервера) ➕ 2024-10-01 ❌ 2024-10-06