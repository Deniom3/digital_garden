---
{"dg-publish":true,"dg-path":"Команды и настройки/Изменение политики доступа клиента через rest api keenetic в Home Assistant.md","permalink":"/komandy-i-nastrojki/izmenenie-politiki-dostupa-klienta-cherez-rest-api-keenetic-v-home-assistant/","updated":"2024-09-03T16:10:26+03:00"}
---

Возврат:: [[Хобби/Команды и настройки/Справочник команд и настроек\|к списку команд]]

---
Пример для переключения политики доступа используя [[Хобби/Команды и настройки/Настройка Rest Api на роутере Keenetic\|rest api keenetic]]. Можно использовать для включение впн у клиента используя автоматизации HA.

```yaml
switch:
   - platform: rest
     name: keenetic_deniom_tel_vpn
     unique_id: keenetic_deniom_tel_vpn
     resource: "http://192.168.0.1:81/rci/ip/hotspot/host"
     body_on:  '{"mac":"d8:b0:53:32:23:61", "policy":"Policy0"}'
     body_off: '{"mac":"d8:b0:53:32:23:61", "policy":"Policy1" }'
     is_on_template: >
            {% for k in value_json %}
            {% if k.mac == 'd8:b0:53:32:23:61' %}
            {{ k.policy == 'Policy0' }}
            {% endif %}{% endfor %}
```

Больше примеров описано в [[Статьи/Мониторинг и управление роутерами Keenetic из Home Assistant\|Мониторинг и управление роутерами Keenetic из Home Assistant]]