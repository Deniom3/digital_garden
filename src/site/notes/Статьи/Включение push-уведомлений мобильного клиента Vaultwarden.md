---
{"dg-publish":true,"permalink":"/stati/vklyuchenie-push-uvedomlenij-mobilnogo-klienta-vaultwarden/","created":"2024-08-20-00-36-42","updated":"2024-09-24T19:36:08+03:00"}
---

Возврат:: [[Статьи/Оглавление статей\|к списку статей]]
> [!urls]- Заметки по статье
> - [[Заметки/Self-hosting. Vaultwarden\|Self-hosting. Vaultwarden]]

---
Начиная с версии `1.29.0` Vaultwarden, вы можете активировать push-уведомления мобильного клиента для [автоматической синхронизации](https://bitwarden.com/help/vault-sync/#automatic-sync) вашего личного хранилища между мобильным приложением, веб-расширением и веб-хранилищем без необходимости синхронизации вручную.

### Включить push-уведомления мобильного клиента

1.  Перейдите по ссылке [https://bitwarden.com/host /](https://bitwarden.com/host/) введите свой адрес электронной почты, и вы получите идентификатор УСТАНОВКИ и КЛЮЧ.
    
2.  Добавьте следующие настройки в свой `docker-compose.yml` (и убедитесь, что вы ввели правильный идентификатор и ключ из предыдущего шага):
    

```yaml
    environment:
      - PUSH_ENABLED=true
      - PUSH_INSTALLATION_ID=
      - PUSH_INSTALLATION_KEY=
```

Примечание

Если вы запросили идентификатор УСТАНОВКИ и КЛЮЧ для `bitwarden.eu (European Union)` на предыдущем шаге, вам также необходимо установить

```yaml
      - PUSH_RELAY_URI=https://api.bitwarden.eu
      - PUSH_IDENTITY_URI=https://identity.bitwarden.eu
```

3.  Создайте свой контейнер заново, например, с помощью

```shell
docker compose up -d vaultwarden
```

4.  Подключите свое приложение к инстансу Vaultwarden.

> [!caution] Предупреждение
> Если вы уже подключали свое приложение Bitwarden до [версии 1.30.2,](https://github.com/dani-garcia/vaultwarden/releases/tag/1.30.2) push-уведомления **не будут работать** на вашем устройстве (поскольку токен устройства так и не был сохранен). Вам необходимо **очистить данные приложения** в вашем мобильном приложении (или **переустановить приложение**) и снова подключить свою учетную запись Vaultwarden, чтобы зарегистрировать push-токен в [центре уведомлений Azure Bitwarden](https://contributing.bitwarden.com/architecture/deep-dives/push-notifications/mobile/#self-hosted-implementation).

> [!warning] Важно
> Push-уведомления также будут **работать** только в приложениях Bitwarden, приобретенных в официальных мобильных магазинах (App Store, Google Play Store) или при использовании альтернативных клиентов для Google Play Store (например, Aurora Store). Push-уведомления **не будут работать** при использовании клиентов Bitwarden, установленных из [F-Droid](https://mobileapp.bitwarden.com/fdroid/), Neo Store или других альтернативных магазинов. Эти приложения были созданы без поддержки Firebase Messaging. Чтобы обеспечить правильную работу push-уведомлений, убедитесь, что функция `firebaseinstallations.googleapis.com` не заблокирована, поскольку это необходимо для работы функции.

5.  Проверьте, работают ли мобильные push-уведомления, например, переименовав папку в веб-хранилище, и посмотрите, изменится ли она через несколько секунд в вашем мобильном приложении.