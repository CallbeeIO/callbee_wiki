---
label: Yeastar S-серия
icon: server
order: 1
expanded: true
---

# Установка Callbee для Yeastar S-серия

> [!NOTE] Требования
> - IP-АТС серии S (S20, S50, S100, S300)
> - Статический IP-адрес
> - Личный кабинет [my.callbee.io](https://my.callbee.io)
> - Доступ к TCP-портам с [IP-адресов сервиса](/ip-addresses/):
>   - **5038** — AMI
>   - **8088** — API (S50, S100, S300)
>   - **21** — FTP (только S20)

## Порядок настройки

1. [Настройка AMI](/setup/yeastar/ami-setup/) — подключение через Asterisk Manager Interface
2. [Публикация записей (API)](/setup/yeastar/api-setup/) — для моделей S50, S100, S300
3. [Публикация записей (FTP)](/setup/yeastar/ftp-setup/) — только для модели S20
4. [Сетевые настройки](/setup/yeastar/network/) — проброс портов через NAT
5. Подключение CRM:
   - [Битрикс24](/setup/yeastar/bitrix24/)
   - [amoCRM](/setup/yeastar/amocrm/)

> [!WARNING] Yeastar S20
> Модель S20 **не поддерживает API**. Вместо этого используется FTP для доступа к записям. Следуйте инструкции [Публикация записей (FTP)](/setup/yeastar/ftp-setup/).
