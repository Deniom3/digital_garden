---
{"dg-publish":true,"dg-path":"Команды и настройки/Настройка реверс прокси на роутере Keenetic на свой домен.md","permalink":"/komandy-i-nastrojki/nastrojka-revers-proksi-na-routere-keenetic-na-svoj-domen/"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|К списку команд]]

---
Для добавления реверс прокси с использованием своего домена можно воспользоваться web cli или через ssh следующими командами
```
ip http proxy <SubDomain>
upstream http <IpClient> <Port>
domain static <Domain>
allow public
system configuration save
```

Дополнительно надо запросить сертификат ssl
```console
ip http ssl acme get <SubDomain>.<Domain>
```

Дальнейшее обновление ssl будет выполняться в автоматическом режиме.

Подробно смотри в руководстве пользователя по работе с cli страница 344

![[cli_manual_kn-3810_ru 1.pdf#page=344]]