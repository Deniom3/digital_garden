---
{"dg-publish":true,"dg-path":"Команды и настройки/Мониторинг системных показателей Keenetic в Home Assistant.md","permalink":"/komandy-i-nastrojki/monitoring-sistemnyh-pokazatelej-keenetic-v-home-assistant/","updated":"2024-10-06T02:55:10+03:00"}
---

Пример для получения системных показателей роутера используя [[Хобби/Команды и настройки/Настройка Rest Api на роутере Keenetic\|rest api keenetic]].

```yaml
- platform: rest
  resource: http://192.168.0.1:81/rci/show/system
  name: Keenetic CPU load
  value_template: "{{ value_json.cpuload }}"
  unit_of_measurement: "%"
  unique_id: Keenetic CPU load

- platform: rest
  resource: http://192.168.0.1:81/rci/show/system
  name: Keenetic RAM load
  value_template: >
    {%- set mem = value_json.memory-%}
    {%- set memfree = mem.split('/')[0]|int(0)-%}
    {%- set memtotal = mem.split('/')[1]|int(1)-%}
    {{ (memfree*100/memtotal)| round(2) }}
  unit_of_measurement: "%"
  unique_id: Keenetic RAM load

- platform: rest
  resource: http://192.168.0.1:81/rci/show/system
  name: Keenetic SWAP load
  value_template: >
    {%- set swap = value_json.swap-%}
    {%- set swapfree = swap.split('/')[0]|int(0)-%}
    {%- set swaptotal = swap.split('/')[1]|int(1)-%}
    {{ (swapfree*100/swaptotal)| round(2) }}
  unit_of_measurement: "%"
  unique_id: Keenetic SWAP load

- platform: rest
  resource: http://192.168.0.1:81/rci/show/system
  name: Keenetic Uptime
  unique_id: Keenetic Uptime
  value_template: >
    {%- set test = value_json.uptime | int(0) / 60-%}
    {%- set minutes = (test % 60) | int %} 
    {%- set hours = ((test % 1440) /60) | int %}
    {%- set days = (test / 1440)|int -%}
    {%if days > 0 %}{{days}}д {% endif %}{%if hours > 0 %}{{hours}}ч {% endif %}{%if minutes > 0  or test >60 %}{{minutes}}м{%else%}<1m{%endif%}

```

Больше примеров описано в [[Статьи/Мониторинг и управление роутерами Keenetic из Home Assistant\|Мониторинг и управление роутерами Keenetic из Home Assistant]]