---
label: Настройка AMI
icon: terminal
order: 100
---

# Настройка AMI для FreePBX

**Asterisk Manager Interface (AMI)** — служебный интерфейс FreePBX/Asterisk, через который Callbee получает события о звонках (начало, ответ, завершение) и управляет вызовами (инициирует исходящие, переадресацию). Без AMI интеграция работать не будет.

В этой инструкции мы создадим отдельного пользователя AMI для Callbee, настроим права доступа и откроем порт **5038** только для наших IP-адресов.

> [!CAUTION] Безопасность — главное
> AMI даёт **полный контроль** над вашей АТС. Никогда не открывайте порт 5038 для всего интернета и не используйте простые пароли. Следуйте инструкции буквально — тут нет «необязательных» шагов.

> [!NOTE] АТС за NAT
> Если FreePBX стоит в локальной сети за роутером, порт **5038/TCP** нужно пробросить на роутере: внешний `5038` → внутренний IP сервера FreePBX, порт `5038`, протокол TCP. Ограничьте источник [IP-адресами Callbee](/ip-addresses/) — настройка в разделе роутера **«Port Forwarding»** / **«Правила трансляции»**. Настройки для конкретных моделей вы делаете самостоятельно.

## Что понадобится

|   |   |
|---|---|
| **Доступ по SSH** | пользователь `root` или с `sudo` |
| **Редактор в терминале** | `nano`, `vim` или другой |
| **Надёжный пароль** | 16+ символов, буквы/цифры/спецсимволы |
| **Доступ к firewall FreePBX** | открыть порт 5038 для [IP-адресов Callbee](/ip-addresses/) |

---

## Шаг 1. Подключитесь к серверу FreePBX

Откройте терминал и подключитесь к серверу по SSH:

```bash
ssh root@<IP-адрес-вашей-АТС>
```

Проверьте, что Asterisk запущен:

```bash
asterisk -rx "core show version"
```

Вы должны увидеть версию Asterisk. Если команда не работает — убедитесь, что АТС запущена и вы находитесь на правильном сервере.

## Шаг 2. Разрешите AMI слушать внешние подключения

По умолчанию AMI слушает только `localhost`. Нужно разрешить подключения с IP-адресов Callbee.

Откройте файл `manager.conf`:

```bash
nano /etc/asterisk/manager.conf
```

В секции `[general]` убедитесь, что параметры выставлены так:

```ini
[general]
enabled = yes
port = 5038
bindaddr = 0.0.0.0
displayconnects = no
```

> [!NOTE] Параметры
> - `enabled = yes` — AMI включён
> - `port = 5038` — стандартный порт AMI (менять не нужно)
> - `bindaddr = 0.0.0.0` — слушать все сетевые интерфейсы
> - `displayconnects = no` — не засорять логи событиями подключений

Сохраните файл (**Ctrl+O**, **Enter**) и закройте (**Ctrl+X**).

## Шаг 3. Создайте пользователя AMI для Callbee

Отдельный пользователь нужен чтобы не конфликтовать с другими интеграциями FreePBX (CRM-коннекторы, FOP2, модули WebRTC) и чтобы можно было отозвать доступ Callbee одним движением.

Выберите удобный способ:

+++ SSH (рекомендуется)

Откройте файл `manager_custom.conf`:

```bash
nano /etc/asterisk/manager_custom.conf
```

Добавьте в конец файла следующий блок (замените `ВАШ_СЛОЖНЫЙ_ПАРОЛЬ` на реальный пароль):

```ini
[crm-to-callbee]
secret = ВАШ_СЛОЖНЫЙ_ПАРОЛЬ
deny = 0.0.0.0/0.0.0.0
permit = 127.0.0.1/255.255.255.0
permit = 185.255.77.197/255.255.255.255
permit = 77.105.155.20/255.255.255.255
permit = 31.44.1.160/255.255.255.255
permit = 178.123.180.59/255.255.255.255
read = system,call,log,verbose,command,agent,user,config,dtmf,reporting,cdr,dialplan,originate,message
write = system,call,log,verbose,command,agent,user,config,dtmf,reporting,cdr,dialplan,originate,message
writetimeout = 500
```

Сохраните файл (**Ctrl+O**, **Enter**) и закройте (**Ctrl+X**).

> [!TIP] Почему именно `manager_custom.conf`
> FreePBX автоматически пересобирает `manager.conf` при каждом **«Apply Config»** и затирает ручные правки. Файл `manager_custom.conf` — специальный «карман» для ваших настроек, FreePBX его не трогает.

+++ Web-интерфейс FreePBX

1. Войдите в FreePBX Admin: `https://<адрес-АТС>/admin`
2. Откройте **Admin → System Admin → Asterisk Manager** (или **Admin → Asterisk Manager Users** — зависит от версии FreePBX и установленных модулей)
3. Нажмите **«Add Manager»**
4. Заполните поля:

|   |   |
|---|---|
| **User** | `crm-to-callbee` |
| **Password** | `ВАШ_СЛОЖНЫЙ_ПАРОЛЬ` |
| **Deny** | `0.0.0.0/0.0.0.0` |
| **Permit** | `127.0.0.1/255.255.255.0`<br>`185.255.77.197/255.255.255.255`<br>`77.105.155.20/255.255.255.255`<br>`31.44.1.160/255.255.255.255`<br>`178.123.180.59/255.255.255.255` |
| **Read** | отметьте все чекбоксы (system, call, log, verbose, command, agent, user, config, dtmf, reporting, cdr, dialplan, originate, message) |
| **Write** | отметьте все чекбоксы (те же что и в Read) |
| **Write Timeout** | `500` |

5. Нажмите **«Submit»**, затем **«Apply Config»** в правом верхнем углу

> [!NOTE] Раздела нет в меню?
> Модуль называется **«Asterisk Manager Users»** (внутреннее имя `manager`) — бесплатный, ставится через **Admin → Module Admin**: найдите его в списке **Available Online**, отметьте **Install** и нажмите **Process**. После этого раздел появится в **Admin**.

+++

> [!WARNING] Не сокращайте права `read`/`write`
> Callbee использует все перечисленные разделы. Если убрать какой-то класс — часть функциональности отвалится: исходящие звонки, история вызовов, переадресация, DTMF. Оставьте все классы как в примере выше.

> [!CAUTION] Пароль
> - **Длина** — не меньше 16 символов
> - **Нельзя** использовать логин пользователя AMI, `admin`, `password`, номер телефона, имя компании
> - **Сохраните** пароль в менеджере паролей — показан в явном виде он только один раз при настройке сервиса

## Шаг 4. Откройте порт 5038 в firewall FreePBX

В FreePBX по умолчанию включён встроенный firewall, который блокирует AMI снаружи. Нужно добавить IP-адреса Callbee в белый список.

+++ Способ 1. FreePBX Web UI (рекомендуется)

1. Откройте **Admin → Firewall → Networks**
2. В поле **«Add Host/Network»** введите первый IP-адрес Callbee: `185.255.77.197/32`
3. Выберите зону **«Trusted»**
4. Нажмите **«Add»**
5. Повторите для остальных IP: `77.105.155.20/32`, `31.44.1.160/32`, `178.123.180.59/32`

+++ Способ 2. Командная строка (iptables)

```bash
iptables -I INPUT -p tcp --dport 5038 -s 185.255.77.197 -j ACCEPT
iptables -I INPUT -p tcp --dport 5038 -s 77.105.155.20 -j ACCEPT
iptables -I INPUT -p tcp --dport 5038 -s 31.44.1.160 -j ACCEPT
iptables -I INPUT -p tcp --dport 5038 -s 178.123.180.59 -j ACCEPT
service iptables save
```

+++

> [!NOTE] Актуальные IP-адреса
> Полный список — на странице [IP-адреса сервиса](/ip-addresses/). Если адреса изменятся, мы анонсируем это в [Changelog](/changelog/) минимум за 30 дней.

## Шаг 5. Примените конфигурацию

Перезагрузите модуль manager без рестарта всей АТС:

```bash
asterisk -rx "manager reload"
```

Проверьте что пользователь `crm-to-callbee` создан:

```bash
asterisk -rx "manager show users"
```

В списке должен появиться `crm-to-callbee` наравне с системными пользователями FreePBX.

## Шаг 6. Проверьте подключение

Убедитесь, что порт доступен локально:

```bash
nc -vz localhost 5038
```

Ожидаемый ответ: `Connection to localhost 5038 port [tcp/*] succeeded!`

Проверьте авторизацию (подставьте свой пароль):

```bash
echo -e "Action: Login\r\nUsername: crm-to-callbee\r\nSecret: ВАШ_ПАРОЛЬ\r\n\r\n" | nc localhost 5038
```

Ответ `Response: Success` означает что логин/пароль правильные. `Response: Error` — перепроверьте файл `manager_custom.conf` и перезагрузите manager.

---

## Частые проблемы

**`Connection refused` при подключении извне**
Firewall блокирует порт 5038. Вернитесь к [Шагу 4](#шаг-4-откройте-порт-5038-в-firewall-freepbx).

**`Authentication failed` в логах Callbee**
Пароль в `manager_custom.conf` не совпадает с указанным в личном кабинете. Проверьте отсутствие пробелов в начале/конце, отредактируйте сервис в [my.callbee.io](https://my.callbee.io).

**Ошибка `Permission denied` после `manager reload`**
Asterisk запущен не от `root` и не может прочитать `manager_custom.conf`. Выполните:
```bash
chown asterisk:asterisk /etc/asterisk/manager_custom.conf
chmod 640 /etc/asterisk/manager_custom.conf
```

**События звонков не приходят в Callbee**
Проверьте права `read` — все перечисленные в [Шаге 3](#шаг-3-создайте-пользователя-ami-для-callbee) разделы обязательны.

**АТС за NAT — порт не виден снаружи**
Если FreePBX стоит в локальной сети за роутером, помимо firewall FreePBX нужно **пробросить порт 5038** на самом роутере:

- **Внешний порт:** `5038`
- **Внутренний IP:** адрес сервера FreePBX в локальной сети
- **Внутренний порт:** `5038`
- **Протокол:** TCP
- **Источник:** ограничьте [IP-адресами Callbee](/ip-addresses/) — на роутерах MikroTik, Keenetic, pfSense, OPNsense, Ubiquiti и большинстве SOHO-роутеров это настраивается в разделе «Port Forwarding» или «Правила трансляции».

Проверьте снаружи:
```bash
nc -vz <внешний-IP-или-домен> 5038
```

Подробности для разных моделей роутеров — в [Troubleshooting → Сетевые проблемы](/troubleshooting/connection/).

---

> [!SUCCESS] AMI настроен!
> Переходите к [публикации записей разговоров](/setup/freepbx/recording-publish/) — Callbee сможет прикреплять аудиофайлы к карточкам в CRM.
