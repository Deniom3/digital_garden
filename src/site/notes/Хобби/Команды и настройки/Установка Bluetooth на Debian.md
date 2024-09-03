---
{"dg-publish":true,"permalink":"/hobbi/komandy-i-nastrojki/ustanovka-bluetooth-na-debian/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---
Для установки Bluetooth в системе Debian необходимо.

1. Установить пакет драйверов bluez
```console
sudo apt-get install bluez
```

2. Включить Bluetooth адаптер в системе
```console
sudo hciconfig hci0 up
```

3. Включить режим безопасности.
```console
sudo hciconfig hci0 sspmode 1
```

4. Включить режим сканирования
```console
sudo hciconfig hci0 piscan
```

5. Завершить процесс настройки
```console
sudo hciconfig hci0 reset
```

Команды проверялись на Debian 11