---
label: 3. Настройка АТС
icon: server
order: 3
---

# Настройка АТС

Чтобы Callbee смог управлять звонками и получать события, нужно настроить доступ к вашей IP-АТС: открыть служебные порты, создать пользователя интеграции и опубликовать записи разговоров.

## Выберите вашу АТС

+++ FreePBX 13+

Инструкция по настройке FreePBX:

1. [Настройка AMI](/setup/freepbx/ami-setup/) — пользователь для интеграции, порт 5038
2. [Публикация записей](/setup/freepbx/recording-publish/) — nginx/Caddy с HTTP или HTTPS

[!ref](/setup/freepbx/)

+++ Yeastar S-серия

Инструкция по настройке Yeastar S (S20/S50/S100/S300):

1. [Настройка AMI](/setup/yeastar/ami-setup/) — учётные данные и белый список IP
2. [Настройка API](/setup/yeastar/api-setup/) — для S50/S100/S300 (доступ к записям)
3. [Настройка FTP](/setup/yeastar/ftp-setup/) — только для S20

[!ref](/setup/yeastar/)

+++ Yeastar P-серия

> [!WARNING] В разработке
> Интеграция Yeastar P-серии (P550/P560/P570) через **App Center** тестируется. Напишите в поддержку [support@callbee.io](mailto:support@callbee.io) чтобы попасть в пилот.

+++

---

> [!SUCCESS] АТС настроена?
> Переходите к [созданию сервиса в личном кабинете и первому звонку](/quickstart/first-call/).
