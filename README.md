## checkdev_mock

### Описание проекта

`checkdev_mock` — это микросервис, реализующий **функциональность тренировочных собеседований (mock interviews)** между пользователями платформы **CheckDev**.
Он обеспечивает полный цикл работы с собеседованиями: от подачи заявки пользователем до проведения интервью и обмена отзывами между участниками.

Микросервис взаимодействует с другими компонентами экосистемы — **`checkdev_auth`**, **`checkdev_notification`**, **`checkdev_desc`**, **`checkdev_site`** — и обеспечивает согласованное поведение в рамках всей платформы.


### Основные возможности

* Создание и управление **mock-собеседованиями**.
* Хранение и обработка **отзывов (feedback)** о прошедших интервью.
* Работа с **желающими (wishers)** пройти или провести собеседование.
* Поддержка **фильтров профилей и направлений** (`Filter`, `FilterProfile`).
* Управление статусами собеседований (`StatusInterview`).
* Отправка **уведомлений** участникам через `checkdev_notification`.
* REST API для интеграции с `checkdev_site`.
* Контроль доступа через **Spring Security**.
* Миграции базы данных через **Liquibase**.


### Архитектура проекта

```
ru.checkdev.mock/
├── MockSrv.java                             # Главный класс Spring Boot приложения
├── config/
│   └── SecurityConfig.java                  # Настройка безопасности
├── domain/                                  # JPA-сущности
│   ├── Feedback.java
│   ├── Filter.java
│   ├── FilterProfile.java
│   ├── Interview.java
│   └── Wisher.java
├── dto/                                     # DTO-объекты для REST API
│   ├── FeedbackDTO.java
│   ├── FilterRequestParams.java
│   ├── InterviewDTO.java
│   ├── UsersApprovedInterviewsDTO.java
│   └── WisherDto.java
├── enums/                                   # Перечисления и конвертеры
│   ├── FilterProfileStore.java
│   ├── StatusInterview.java
│   └── StatusInterviewConverter.java
├── mapper/                                  # Преобразование сущностей <-> DTO
│   ├── FeedbackMapper.java
│   ├── InterviewMapper.java
│   └── WisherMapper.java
├── repository/                              # Репозитории Spring Data JPA
│   ├── FeedbackRepository.java
│   ├── FilterRepository.java
│   ├── InterviewRepository.java
│   └── WisherRepository.java
├── service/                                 # Бизнес-логика
│   ├── FeedbackCrudService.java
│   ├── FeedbackService.java
│   ├── FilterService.java
│   ├── InterviewFilterSpecifications.java
│   ├── InterviewService.java
│   └── WisherService.java
└── web/                                     # REST API и контроллеры
    ├── FeedbackController.java
    ├── FilterController.java
    ├── GlobalExceptionHandler.java
    ├── InterviewController.java
    ├── InterviewsController.java
    ├── WisherController.java
    └── WishersController.java
```


### Технологический стек

| Компонент           | Назначение                         |
|---------------------|------------------------------------|
| **Java 21+**        | Язык реализации                    |
| **Spring Boot**     | Фреймворк приложения               |
| **Spring Data JPA** | ORM и доступ к БД                  |
| **Spring Security** | Аутентификация и авторизация       |
| **Liquibase**       | Миграции БД                        |
| **PostgreSQL**      | Основная база данных               |
| **REST API**        | Взаимодействие с другими сервисами |
| **Maven**           | Система сборки                     |
| **Jenkins**         | CI/CD                              |
| **Eureka Client**   | Регистрация и поиск сервисов       |


### Конфигурация приложения

Пример `application.properties`:

```properties
spring.application.name=mock
server.port=9013

spring.datasource.url=jdbc:postgresql://localhost:5432/checkdev_mock
spring.datasource.username=postgres
spring.datasource.password=postgres
spring.jpa.hibernate.ddl-auto=validate
spring.liquibase.change-log=classpath:db/db.changelog-master.xml

eureka.client.service-url.defaultZone=http://localhost:9009/eureka
```


### Основные REST API

#### **Интервью (Interview)**

* `GET /interviews` — получить список собеседований.
* `GET /interview/{id}` — получить интервью по ID.
* `POST /interview` — создать новое интервью.
* `PUT /interview/{id}` — обновить статус интервью.
* `DELETE /interview/{id}` — удалить интервью.

#### **Отзывы (Feedback)**

* `GET /feedback` — получить все отзывы.
* `POST /feedback` — добавить отзыв.
* `GET /feedback/{id}` — получить отзыв по ID.

#### **Желающие (Wisher)**

* `GET /wishers` — список всех участников, желающих пройти интервью.
* `POST /wisher` — добавить нового желающего.
* `DELETE /wisher/{id}` — удалить запись.

#### **Фильтры (Filter)**

* `GET /filters` — список доступных фильтров.
* `POST /filter` — создать новый фильтр.

*(точные пути и DTO можно уточнить по контроллерам в пакете `web/`)*


### Ключевые классы и логика

| Класс                             | Назначение                                                 |
|-----------------------------------|------------------------------------------------------------|
| **InterviewService**              | Управление mock-интервью (создание, изменение, фильтрация) |
| **FeedbackService**               | Обработка отзывов участников                               |
| **WisherService**                 | Управление пользователями, желающими пройти интервью       |
| **FilterService**                 | Работа с фильтрами и профилями                             |
| **InterviewFilterSpecifications** | Реализация фильтрации по критериям JPA                     |
| **GlobalExceptionHandler**        | Централизованная обработка ошибок API                      |
| **SecurityConfig**                | Настройка авторизации и доступов к REST API                |


### Интеграции с другими сервисами

| Сервис                    | Назначение интеграции                                                     |
|---------------------------|---------------------------------------------------------------------------|
| **checkdev_auth**         | Аутентификация пользователей и получение профилей                         |
| **checkdev_notification** | Отправка уведомлений об изменениях интервью или отзывов                   |
| **checkdev_desc**         | Получение направлений и категорий для фильтров и mock-интервью            |
| **checkdev_site**         | Веб-интерфейс, через который пользователи взаимодействуют с mock-интервью |
| **cd_eureka**             | Динамическое обнаружение других сервисов                                  |

### Как запустить проект

#### 1. Сборка

```bash
mvn clean package
```

#### 2. Запуск

```bash
java -jar target/checkdev_mock-0.0.1-SNAPSHOT.jar
```

или:

```bash
mvn spring-boot:run
```

#### 3. Доступ

После запуска:

```
http://localhost:9013
```


### Интеграция с Eureka

```properties
eureka.client.register-with-eureka=true
eureka.client.fetch-registry=true
eureka.client.service-url.defaultZone=http://localhost:9009/eureka
```


### Dockerfile (пример)

```dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY target/checkdev_mock-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 9013
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Сборка:

```bash
docker build -t checkdev-mock .
```

Запуск:

```bash
docker run -p 9013:9013 checkdev-mock
```


### Jenkins (CI/CD)

`Jenkinsfile` содержит стандартный pipeline:

1. **Build** — сборка артефакта через Maven
2. **Test** — запуск тестов
3. **Deploy** — публикация Docker-образа

### Типовой сценарий взаимодействия

#### **Сценарий: создание mock-интервью пользователем**

1. **Пользователь** входит на платформу через веб-интерфейс (`checkdev_site`).
2. `checkdev_site` обращается к **`checkdev_auth`** для валидации JWT токена и получения профиля пользователя.
3. Пользователь выбирает категорию и направление (данные приходят из **`checkdev_desc`**).
4. Пользователь подаёт заявку на участие в mock-интервью (через `checkdev_site` → `checkdev_mock` → `POST /wisher`).
5. `checkdev_mock` сохраняет заявку и подбирает доступные фильтры и профили (через `FilterService` и `InterviewFilterSpecifications`).
6. Если находится подходящий партнёр, `checkdev_mock` создаёт новое интервью (`InterviewService#create`).
7. После создания интервью `checkdev_mock` уведомляет обоих участников через **`checkdev_notification`**:

* отправляется Telegram-сообщение (бот),
* создаётся внутренняя запись `InnerMessage`.
8. Интервью проходит (вне платформы или через интегрированный канал).
9. После завершения один из участников оставляет отзыв:

* `checkdev_site` → `checkdev_mock` → `POST /feedback`
* отзыв сохраняется в БД, обрабатывается `FeedbackService`.
10. **`checkdev_notification`** отправляет сообщение второму участнику о полученном фидбэке.
11. В дальнейшем отзывы могут использоваться в профиле пользователя (через `checkdev_site` и `checkdev_auth`).
