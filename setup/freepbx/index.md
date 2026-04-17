# Установка Callbee для FreePBX 13+

> [!NOTE] Требования
> - FreePBX версии 13 и выше
> - Активированный chan_sip и транки на его основе
> - Статический IP-адрес
> - Личный кабинет [my.callbee.io](https://my.callbee.io)
> - Доступ к TCP-порту 5038 (AMI) с [IP-адресов сервиса](/ip-addresses/)
> - Доступ пользователей к записям разговоров по HTTP/HTTPS

## Порядок настройки

1. [Настройка AMI](/setup/freepbx/ami-setup/) — подключение Callbee к Asterisk Manager Interface
2. [Публикация записей](/setup/freepbx/recording-publish/) — доступ к записям разговоров через веб-сервер
3. Подключение CRM:
   - [Битрикс24](/setup/freepbx/bitrix24/)
   - [amoCRM](/setup/freepbx/amocrm/)
