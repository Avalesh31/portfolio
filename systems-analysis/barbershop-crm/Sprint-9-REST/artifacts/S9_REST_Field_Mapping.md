# Exercise 06 — Маппинг

## Вариант 1. Проверка клиента по телефону

### Маппинг: система-отправитель -> сообщение

| Сущность/класс системы | Вложенный блок / множественность | Элемент/поле системы | Тип данных | Размерность | Обязательность | Условия, ограничения | Ссылка на справочник | Элемент сообщения из ex05 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `UI.PhoneSearchForm` | `1` | `phoneInput` | `string` | 15 | Да | Нормализация в E.164 | Нет | `query.phone` |

### Маппинг: сообщение -> система-получатель

| Сущность/класс системы | Вложенный блок / множественность | Элемент/поле системы | Тип данных | Размерность | Обязательность | Условия, ограничения | Ссылка на справочник | Элемент сообщения из ex05 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `CustomerSearchRequest` | `1` | `phone` | `string` | 15 | Да | Один номер на запрос | Нет | `query.phone` |
| `CustomerSearchResponse` | `1` | `found` | `boolean` | - | Да | Признак результата поиска | Нет | `result.found` |
| `Customer` | `1` | `id` | `integer` | - | Нет | Заполняется, если найден клиент | Нет | `result.customerId` |
| `Persona` | `1` | `id` | `integer` | - | Нет | Заполняется, если найден клиент | Нет | `result.personaId` |
| `Persona` | `1` | `phone_number` | `string` | 15 | Да | Канонический формат | Нет | `result.phone` |
| `Persona` | `1` | `first_name` | `string` | 50 | Нет | Заполняется, если найден клиент | Нет | `result.firstName` |
| `Customer` | `1` | `registration_date` | `datetime` | - | Нет | UTC | Нет | `result.registrationDate` |

## Вариант 2. Регистрация клиента менеджером

### Маппинг: система-отправитель -> сообщение

| Сущность/класс системы | Вложенный блок / множественность | Элемент/поле системы | Тип данных | Размерность | Обязательность | Условия, ограничения | Ссылка на справочник | Элемент сообщения из ex05 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `UI.CustomerRegistrationForm` | `customer` | `phone` | `string` | 15 | Да | Формат E.164 | Нет | `customer.phone` |
| `UI.CustomerRegistrationForm` | `customer` | `firstName` | `string` | 50 | Да | Без служебных символов | Нет | `customer.firstName` |
| `SessionContext` | `meta` | `employeeId` | `integer` | - | Да | Пользователь должен быть менеджером | `EmployeeRole` | `meta.initiatorEmployeeId` |
| `SessionContext` | `meta` | `requestId` | `uuid` | 36 | Да | UUID запроса | Нет | `meta.requestId` |

### Маппинг: сообщение -> система-получатель

| Сущность/класс системы | Вложенный блок / множественность | Элемент/поле системы | Тип данных | Размерность | Обязательность | Условия, ограничения | Ссылка на справочник | Элемент сообщения из ex05 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `Persona` | `1` | `phone_number` | `string` | 15 | Да | Уникальный номер | Нет | `customer.phone` |
| `Persona` | `1` | `first_name` | `string` | 50 | Да | Имя клиента | Нет | `customer.firstName` |
| `Employee` | `1` | `id` | `integer` | - | Да | Инициатор операции | Нет | `meta.initiatorEmployeeId` |
| `AuditLog` | `1` | `request_id` | `uuid` | 36 | Да | Трассировка | Нет | `meta.requestId` |
| `Customer` | `1` | `id` | `integer` | - | Да | Создается системой | Нет | `result.customerId` |
| `Persona` | `1` | `id` | `integer` | - | Да | Создается системой | Нет | `result.personaId` |
| `Persona` | `1` | `phone_number` | `string` | 15 | Да | Используется как логин | Нет | `result.login` |
| `GeneratedCredentials` | `1` | `temporaryPassword` | `string` | 20 | Да | Генерируется системой | Нет | `result.temporaryPassword` |
| `Persona` | `1` | `is_temp_password` | `boolean` | - | Да | `true` при менеджерской регистрации | Нет | `result.passwordTemporary` |
| `Customer` | `1` | `registration_date` | `datetime` | - | Да | UTC | Нет | `result.registeredAt` |

## Вариант 3. Отправка регистрационного SMS

### Маппинг: система-отправитель -> сообщение

| Сущность/класс системы | Вложенный блок / множественность | Элемент/поле системы | Тип данных | Размерность | Обязательность | Условия, ограничения | Ссылка на справочник | Элемент сообщения из ex05 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `Customer` | `recipient` | `id` | `integer` | - | Да | Внутренний id клиента | Нет | `recipient.customerId` |
| `Persona` | `recipient` | `phone_number` | `string` | 15 | Да | Канонический номер | Нет | `recipient.phone` |
| `Persona` | `recipient` | `first_name` | `string` | 50 | Да | Для персонализации | Нет | `recipient.firstName` |
| `Persona` | `credentials` | `phone_number` | `string` | 15 | Да | Используется как логин | Нет | `credentials.login` |
| `GeneratedCredentials` | `credentials` | `temporaryPassword` | `string` | 20 | Да | Генерируется системой | Нет | `credentials.temporaryPassword` |
| `NotificationTypeRef` | `message` | `code` | `string` | 20 | Да | Значение `REGISTRATION` | `NotificationType` | `message.notificationType` |
| `NotificationChannelRef` | `message` | `code` | `string` | 15 | Да | Значение `SMS` | `NotificationChannel` | `message.channel` |
| `NotificationTemplate` | `message` | `text` | `string` | 500 | Да | Шаблонный текст | Нет | `message.messageText` |
| `AuditLog` | `meta` | `request_id` | `uuid` | 36 | Да | Трассировка | Нет | `meta.requestId` |

### Маппинг: сообщение -> система-получатель

| Сущность/класс системы | Вложенный блок / множественность | Элемент/поле системы | Тип данных | Размерность | Обязательность | Условия, ограничения | Ссылка на справочник | Элемент сообщения из ex05 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| `NotificationRequest` | `recipient` | `customerId` | `integer` | - | Да | Обязателен для трассировки | Нет | `recipient.customerId` |
| `NotificationRequest` | `recipient` | `phone` | `string` | 15 | Да | Используется для доставки | Нет | `recipient.phone` |
| `NotificationRequest` | `recipient` | `firstName` | `string` | 50 | Да | Для обращения к клиенту | Нет | `recipient.firstName` |
| `NotificationRequest` | `credentials` | `login` | `string` | 15 | Да | Логин клиента | Нет | `credentials.login` |
| `NotificationRequest` | `credentials` | `temporaryPassword` | `string` | 20 | Да | Пароль первого входа | Нет | `credentials.temporaryPassword` |
| `Notification` | `message` | `notification_type` | `string` | 20 | Да | Код типа уведомления | `NotificationType` | `message.notificationType` |
| `Notification` | `message` | `channel` | `string` | 15 | Да | Код канала отправки | `NotificationChannel` | `message.channel` |
| `Notification` | `message` | `message` | `string` | 500 | Да | Текст сообщения | Нет | `message.messageText` |
| `ProviderRequest` | `meta` | `requestId` | `uuid` | 36 | Да | Корреляция с BRSP | Нет | `meta.requestId` |
| `Notification` | `result` | `id` | `string/integer` | 50 | Да | Идентификатор уведомления | Нет | `result.notificationId` |
| `Notification` | `result` | `status` | `string` | 15 | Да | `PENDING`, `SENT`, `FAILED` | Нет | `result.status` |
| `Notification` | `result` | `provider_message_id` | `string` | 50 | Нет | Ответ внешнего провайдера | Нет | `result.providerMessageId` |

## Правила преобразования

1. Номер телефона нормализуется до единого формата до отправки во все интеграции.
2. Логин клиента при регистрации менеджером равен номеру телефона.
3. Временный пароль создается системой и не поступает от пользователя.
4. Справочные значения `REGISTRATION` и `SMS` передаются как коды справочников.
5. Все даты и время рекомендуется хранить и передавать в UTC.
