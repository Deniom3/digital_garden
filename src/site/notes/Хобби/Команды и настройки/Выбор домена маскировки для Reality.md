---
{"dg-publish":true,"dg-path":"Команды и настройки/Выбор домена маскировки для Reality.md","permalink":"/komandy-i-nastrojki/vybor-domena-maskirovki-dlya-reality/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Для поиска используем специальный сканер [GitHub - XTLS/RealiTLScanner: A TLS server scanner for Reality](https://github.com/XTLS/RealiTLScanner)

После загрузки выполнить команду в папке со сканером
```shell
./RealiTLScanner -addr IP_вашего_VPS -out file.csv
```

Сканер переберет IP адреса подсети и выведет сайты которые по ним расположены. Необходимо проверить сайт путем захода на него, если сайт открывается без ошибок то можно использовать и указывать для маскировки.