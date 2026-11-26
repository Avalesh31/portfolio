# Exercise 09 — Определение и описание методов

## Вариант 1. Получить список мастеров

### Вопрос
Какие сотрудники доступны для назначения слотов как мастера?

### Запрос
- Метод: `GET`
- URL: `/api/v1/employees`
- Параметры:
  - `role=MASTER`
- Тело запроса: не требуется

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Успех | `200 OK` | `items[]` со списком мастеров |
| Нет мастеров | `200 OK` | `items=[]` |
| Ошибка сервера | `500 Internal Server Error` | `errorCode`, `message`, `requestId` |

### Обоснование
- CRUD: `Read`
- Метод безопасный: да
- Метод идемпотентный: да

## Вариант 2. Проверить доступность слота

### Вопрос
Свободен ли выбранный слот для конкретного мастера?

### Запрос
- Метод: `GET`
- URL: `/api/v1/master-slots/availability`
- Параметры:
  - `employeeId`
  - `slotDate`
  - `startTime`
- Тело запроса: не требуется

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Слот свободен | `200 OK` | `available=true` |
| Слот занят | `200 OK` | `available=false`, `bookingId` |
| Ошибка валидации | `400 Bad Request` | `errorCode`, `message`, `fieldErrors[]` |

### Обоснование
- CRUD: `Read`
- Метод безопасный: да
- Метод идемпотентный: да

## Вариант 3. Создать или обновить слот мастера

### Вопрос
Закрепи выбранный слот за мастером.

### Запрос
- Метод: `POST` для нового слота или `PUT` для обновления существующего
- URL:
  - `/api/v1/master-slots`
  - `/api/v1/master-slots/{slotId}`
- Параметры:
  - `slotId` для `PUT`
- Тело запроса:
  - `employeeId`
  - `slotDate`
  - `startTime`
  - `endTime`
  - `serviceId` при необходимости

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Слот создан | `201 Created` | `slotId`, `status` |
| Слот обновлен | `200 OK` | `slotId`, `status` |
| Слот конфликтует с бронью | `409 Conflict` | `errorCode`, `message`, `bookingId` |
| Ошибка валидации | `400 Bad Request` | `errorCode`, `message`, `fieldErrors[]` |

### Обоснование
- CRUD: `Create/Update`
- Метод безопасный: нет
- Метод идемпотентный:
  - `POST` — нет
  - `PUT` — да

## Вариант 4. Отправить клиенту SMS об изменении записи

### Вопрос
Сообщи клиенту, что время и/или мастер его визита изменены.

### Запрос
- Метод: `POST`
- URL: `/api/v1/notifications/booking-change-sms`
- Параметры: не требуются
- Тело запроса:
  - `customerId`
  - `phone`
  - `oldSlot`
  - `newSlot`
  - `masterName`
  - `messageText`

### Ответы

| Сценарий | HTTP status | Тело ответа |
| --- | --- | --- |
| Уведомление принято | `202 Accepted` | `notificationId`, `status` |
| Ошибка валидации | `400 Bad Request` | `errorCode`, `message`, `fieldErrors[]` |
| Провайдер недоступен | `503 Service Unavailable` | `errorCode`, `message`, `retryAfter` |

### Обоснование
- CRUD: `Create`
- Метод безопасный: нет
- Метод идемпотентный: нет
