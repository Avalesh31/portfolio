# Exercise 04 — Определение и описание методов

## Вариант 1. Проверить, зарегистрирован ли клиент по номеру телефона

### Вопрос
Есть ли в системе клиент с указанным номером телефона?

### Запрос
- Метод: `GET`
- URL: `/api/v1/customers/by-phone`
- Параметры:
  - `phone` — номер телефона клиента
- Тело запроса: не требуется

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Клиент найден | `200 OK` | `found=true`, `customerId`, `personaId`, `phone`, `firstName`, `registrationDate` |
| Клиент не найден | `200 OK` | `found=false`, `phone` |
| Неверный формат телефона | `400 Bad Request` | `errorCode`, `message`, `field=phone` |
| Ошибка сервера | `500 Internal Server Error` | `errorCode`, `message`, `requestId` |

### Почему этот метод
- CRUD: `Read`
- Безопасный метод: да
- Идемпотентный метод: да

## Вариант 2. Зарегистрировать клиента менеджером

### Вопрос
Создай нового клиента, если номер телефона свободен.

### Запрос
- Метод: `POST`
- URL: `/api/v1/customers/manager-registration`
- Параметры: не требуются
- Тело запроса:
  - `phone`
  - `firstName`
  - `initiatorEmployeeId`
  - `requestId`

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Клиент зарегистрирован | `201 Created` | `customerId`, `personaId`, `login`, `temporaryPassword`, `passwordTemporary=true`, `registeredAt` |
| Телефон уже существует | `409 Conflict` | `errorCode`, `message`, `existingCustomerId`, `phone` |
| Ошибка валидации | `400 Bad Request` | `errorCode`, `message`, `fieldErrors[]` |
| Нет прав у сотрудника | `403 Forbidden` | `errorCode`, `message`, `employeeId` |
| Ошибка сервера | `500 Internal Server Error` | `errorCode`, `message`, `requestId` |

### Почему этот метод
- CRUD: `Create`
- Безопасный метод: нет
- Идемпотентный метод: нет

## Вариант 3. Отправить регистрационное SMS

### Вопрос
Отправь клиенту SMS о регистрации с логином и временным паролем.

### Запрос
- Метод: `POST`
- URL: `/api/v1/notifications/registration-sms`
- Параметры: не требуются
- Тело запроса:
  - `customerId`
  - `phone`
  - `firstName`
  - `login`
  - `temporaryPassword`
  - `notificationType`
  - `channel`
  - `messageText`
  - `requestId`

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Сообщение принято в обработку | `202 Accepted` | `notificationId`, `status=PENDING`, `providerMessageId` или `queueId` |
| Ошибка валидации сообщения | `400 Bad Request` | `errorCode`, `message`, `fieldErrors[]` |
| Канал или тип уведомления не поддержан | `422 Unprocessable Entity` | `errorCode`, `message`, `field`, `rejectedValue` |
| Провайдер недоступен | `503 Service Unavailable` | `errorCode`, `message`, `retryAfter`, `requestId` |

### Почему этот метод
- CRUD: `Create`
- Безопасный метод: нет
- Идемпотентный метод: нет

## Итоговый набор интеграций UC04

| № | Назначение | Метод |
| --- | --- | --- |
| 1 | Проверка существования клиента по телефону | `GET /api/v1/customers/by-phone` |
| 2 | Регистрация клиента менеджером | `POST /api/v1/customers/manager-registration` |
| 3 | Отправка регистрационного SMS | `POST /api/v1/notifications/registration-sms` |
