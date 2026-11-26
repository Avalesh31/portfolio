# Exercise 01 — Подготовка данных

## 1. Источники данных

Для выполнения упражнения использованы два проекта `BSA09`: мой и пира из моего кампуса.

## 2. Классы, требуемые для реализации UC02 и UC04

### Для UC02 "Сформировать расписание мастера"
- `Employee`
- `EmployeeRole`
- `Service`
- `MasterService`
- `ServiceSlot`

### Для UC04 "Зарегистрировать клиента (менеджер)"
- `Persona`
- `Customer`
- `Employee`
- `EmployeeRole`
- `Notification` как вспомогательная сущность для уведомления о регистрации

## 3. Структуры данных из моего проекта

### Persona

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | Первичный ключ |
| `phone_number` | `VARCHAR(15)` | Да | Уникальный номер телефона |
| `first_name` | `VARCHAR(50)` | Да | Имя |
| `middle_name` | `VARCHAR(50)` | Нет | Отчество |
| `password_hash` | `VARCHAR(255)` | Да* | Для менеджерской регистрации хранится временный пароль |
| `registration_date` | `TIMESTAMP` | Да | Дата регистрации |
| `is_temp_password` | `BOOLEAN` | Да | Признак временного пароля |

### Customer

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `persona_id` | `BIGINT` | Да | Ссылка на `Persona` |
| `registration_date` | `TIMESTAMP` | Да | Дата регистрации клиента |

### Employee

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `persona_id` | `BIGINT` | Да | Ссылка на `Persona` |
| `hire_date` | `DATE` | Да | Дата приема |

### EmployeeRole

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `employee_id` | `BIGINT` | Да | Ссылка на `Employee` |
| `role` | `VARCHAR(20)` | Да | `MASTER`, `MANAGER` |

### Service

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `name` | `VARCHAR(100)` | Да | Название услуги |
| `duration_minutes` | `INTEGER` | Да | Продолжительность |

### MasterService

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `employee_id` | `BIGINT` | Да | Ссылка на мастера |
| `service_id` | `BIGINT` | Да | Ссылка на услугу |

### ServiceSlot

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `slot_date` | `DATE` | Да | Дата слота |
| `start_time` | `TIME` | Да | Начало |
| `end_time` | `TIME` | Да | Окончание |
| `employee_id` | `BIGINT` | Да | Мастер |
| `customer_id` | `BIGINT` | Нет | Клиент, если слот занят |
| `service_id` | `BIGINT` | Нет | Услуга, если слот занят |

### Notification

| Поле | Тип | Обяз. | Комментарий |
| --- | --- | --- | --- |
| `id` | `BIGINT/SERIAL` | Да | PK |
| `customer_id` | `BIGINT` | Да | Получатель |
| `notification_type` | `VARCHAR(20)` | Да | Например, `REGISTRATION` |
| `channel` | `VARCHAR(15)` | Да | `SMS`, `TELEGRAM`, `WHATSAPP`, `VK` |
| `title` | `VARCHAR(100)` | Нет | Заголовок |
| `message` | `TEXT` | Да | Текст уведомления |
| `status` | `VARCHAR(15)` | Да | `PENDING`, `SENT`, `FAILED`, `DELIVERED` |
| `scheduled_at` | `TIMESTAMP` | Да | Плановое время отправки |
| `sent_at` | `TIMESTAMP` | Нет | Фактическое время отправки |
| `related_booking_id` | `BIGINT` | Нет | Ссылка на бронирование |

## 4. Структуры данных из проекта другого пира

- `Persona`
- `Customer`
- `Employee`
- `EmployeeRole`
- `Service`
- `MasterService`
- `ServiceSlot`

### Ключевые атрибуты проекта другого пира

| Сущность | Поля |
| --- | --- |
| `Persona` | `id`, `phone_number`, `first_name`, `middle_name`, `password_hash`, `registration_date`, `is_temp_password` |
| `Customer` | `id`, `persona_id`, `registration_date` |
| `Employee` | `id`, `persona_id`, `hire_date` |
| `EmployeeRole` | `id`, `employee_id`, `role` |
| `Service` | `id`, `name`, `duration_minutes` |
| `MasterService` | `id`, `employee_id`, `service_id` |
| `ServiceSlot` | `id`, `slot_date`, `start_time`, `end_time`, `employee_id`, `customer_id`, `service_id` |

## 5. Соответствие моего проекта и проекта другого пира

### Соответствие сущностей

| Мой проект | Проект другого пира | Соответствие |
| --- | --- | --- |
| `Persona` | `Persona` | Полное совпадение |
| `Customer` | `Customer` | Полное совпадение |
| `Employee` | `Employee` | Полное совпадение |
| `EmployeeRole` | `EmployeeRole` | Полное совпадение |
| `Service` | `Service` | Полное совпадение |
| `MasterService` | `MasterService` | Полное совпадение |
| `ServiceSlot` | `ServiceSlot` | Полное совпадение |

### Соответствие атрибутов

| Сущность | Мой проект | Проект другого пира | Вывод |
| --- | --- | --- | --- |
| `Persona` | Совпадают названия полей, типы и обязательность | Совпадают | Различий не найдено |
| `Customer` | Совпадают | Совпадают | Различий не найдено |
| `Employee` | Совпадают | Совпадают | Различий не найдено |
| `EmployeeRole` | Совпадают | Совпадают | Различий не найдено |
| `Service` | Совпадают | Совпадают | Различий не найдено |
| `MasterService` | Совпадают | Совпадают | Различий не найдено |
| `ServiceSlot` | Совпадают | Совпадают | Различий не найдено |

## 6. Итоговый вывод по переносу

Для реализации `UC02` и `UC04` можно использовать единый набор классов и атрибутов, так как между моим проектом и проектом другого пира по найденным артефактам расхождений не выявлено.

Это значит:
- дополнительное преобразование модели между двумя проектами не требуется;
- отдельный маппинг `мой BSA09 -> BSA09 другого пира` можно зафиксировать как `1:1`;
- в текущем проекте BSA15 можно брать эту модель как согласованную основу для последующих упражнений.

## 7. Что было бы нужно делать, если бы различия нашлись

Если бы в проекте другого пира отличались названия сущностей, состав полей, типы или обязательность, то в этом упражнении нужно было бы дополнительно:

1. построить таблицу `сущность моего проекта -> сущность другого проекта`;
2. построить таблицу `поле моего проекта -> поле другого проекта`;
3. отметить конфликты типов, длины и обязательности;
4. выбрать итоговую целевую структуру для BSA15.
