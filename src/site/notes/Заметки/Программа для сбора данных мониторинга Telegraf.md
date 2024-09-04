---
{"dg-publish":true,"permalink":"/zametki/programma-dlya-sbora-dannyh-monitoringa-telegraf/","created":"2024-07-03 22:27","updated":"2024-09-03T16:35:26+03:00"}
---

Агент для сбора различных серверных аналитик с открытым исходным кодом. Позволяет собирать различные данные для организации мониторинга систем и передавать их в различные системы сбора и обработки статистики. В основном используется в связке с influxdb, но так же может писать в логи и в mqtt и и.д.

Имеет множество различных плагинов для расширения функционала: [Plugin directory | Telegraf Documentation](https://docs.influxdata.com/telegraf/v1/plugins/)

Официальный сайт: [Telegraf | InfluxData](https://www.influxdata.com/time-series-platform/telegraf/)

---
#### Упоминания:
- Докер файл для запуска мониторинга системы linux с использованием [[Хобби/Docker compose/Telegraf\|Telegraf]] и файл конфигурации [[Хобби/Конфиги/Telegraf\|Telegraf]]
- Докер файл для запуска мониторинга OpenWrt c использованием [[Хобби/Docker compose/Telegraf-openwrt\|Telegraf-openwrt]] и файл конфигурации [[Хобби/Конфиги/Telegraf OpenWrt\|Telegraf OpenWrt]]
- Докер файл [[Хобби/Docker compose/Telegraf-nut\|Telegraf-nut]] для запуска мониторинга ИБП с использованием NUT и файл конфигурации для мониторинга  [[Хобби/Конфиги/Telegraf-Nut\|Telegraf-Nut]]
- [[Статьи/Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana\|Мониторинг домашней и серверной инфраструктуры на базе InfluxDB и Grafana]]
- [[Статьи/Мониторинг роутеров openwrt в influxdb v2 и grafana\|Мониторинг роутеров openwrt в influxdb v2 и grafana]]
- [[Статьи/Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа\|Мониторинг состояния роутеров keenetic в influxdb v2 и grafanа]]