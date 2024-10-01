---
{"dg-publish":true,"permalink":"/zametki/self-hosting-hom-ed-web/","created":"2024-10-01 22:22","updated":"2024-10-01T22:24:29+03:00"}
---

Легковесный веб интерфейс для взаимодействия с системой умного дома HOMEd

Система относится к стеку [[Заметки/Система умного дома HOMEd\|HOMEd]]

Репозиторий: https://github.com/u236/homed-service-web
### Файл конфигурации:

<div class="transclusion internal-embed is-loaded"><div class="markdown-embed">




Актуальная версия конфигурации приведена на [wiki](https://wiki.homed.dev/page/Web/Configuration)

```shell
[log]
enabled=false
file=/var/log/homed.log

[mqtt]
host=localhost
port=1883
username=homed
password=secret
prefix=homed

[server]
port=8080
database=/opt/homed-web/database.json
frontend=/usr/share/homed-web
```

</div></div>


---
> [!urls]- Упоминания:
> - [[Служебное/Self-hosting программы\|Self-hosting программы]]
> - [[Хобби/Домашняя лаборатория/Роутер Perenio\|Роутер Perenio]]

> [!info]-
> Примечание:: Веб интерфейс для управления системой HOMEd
