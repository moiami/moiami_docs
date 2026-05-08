# Backend кинотеатра от команды moiami



### Архитектура

![shema](C:\Users\Leonid\PycharmProjects\moiami_docs\shema.png)



### Общее описание проекта

Backend часть онлайн-кинотеатра. Взаимодействие с внешним миром происходит через API-шлюз **Caddy**, который маршрутизирует HTTP-запросы к соответствующим микросервисам.

В проекте используются фреймворки: **FastAPI**, **Django** и **Flask**. Для хранения файлов используется **MinIO**, а для данных  **PostgreSQL**.

# Описание микросервисов

## SSO 

#### (Сервис авторизации)

Отвечает за , аутентификацию пользователей, управление пользователями, управлением ролями пользователей. Сервис позволяет новых регистрировать пользователей, создавать роли. По умолчанию созданы роли для Admin и user. Также создан пользователь Admin. Отвечает за выдачу и валидацию jwt токенов.

**Фреймворк:**  FastAPI.

**Эндпоинты:**

POST /api/v1/auth/login

POST /api/v1/auth/validate

POST /api/v1/auth/refresh

POST /api/v1/auth/register

GET /api/v1/roles/roles

GET /api/v1/roles/role

POST /api/v1/roles/create_role

PATCH /api/v1/roles/update_role

DELETE /api/v1/roles/delete_role

GET /api/v1/user/users

GET /api/v1/user/user

POST /api/v1/user/change_role

DELETE /api/v1/user/delete_user

## RESOURCE_SERVICE 

#### (Сервис ресурсов)

Отвечает за хранение фильмов, сортировку фильмов по жанрам, подписки на фильмы, плейлисты из фильмов. Пользователь, получив подписку, может посмотреть фильмы, входящие в подписку. Пользователь может составлять плейлисты из фильмов. Сервисом собирается статистика по количеству запросов на получение каждого фильма. Сервис предоставляет возможность получить список из самых часто запрашиваемых фильмов.

**Фреймворк:**  Django.

**Эндпоинты**

GET /api/v1/catalog/movies/

GET /api/v1/catalog/movies/{id}/

GET /api/v1/catalog/movies/{id}/genres/

GET /api/v1/catalog/movies/subscriptions/{subscription_id}/

GET /api/v1/catalog/genres/

GET /api/v1/catalog/genres/{id}/

GET /api/v1/catalog/images/

GET /api/v1/catalog/images/{id}/

GET /api/v1/catalog/videos/

GET /api/v1/catalog/videos/{id}/

GET /api/v1/subscriptions/

GET /api/v1/subscriptions/{id}/

POST /api/v1/user-subscriptions/add/

GET /api/v1/user-subscriptions/check/{subscription_id}/

GET /api/v1/user-subscriptions/{subscription_id}/users/

POST /api/v1/users/

GET /api/v1/users/

GET /api/v1/users/{id}/

GET /api/v1/users/{id}/subscriptions/

GET /api/v1/users/{id}/watchlists/

GET /api/v1/watchlists

POST /api/v1/watchlists

GET /api/v1/watchlists/{id}

PUT /api/v1/watchlists/{id}

PATCH /api/v1/watchlists/{id}

DELETE /api/v1/watchlists/{id}

POST /api/v1/watchlists/{id}/movies

GET /api/v1/catalog/movies/{id}/film_statistics/?start_timestamp={timestamp}&end_timestamp={timestamp}

GET /api/v1/catalog/movies/top/?start_timestamp={timestamp}&end_timestamp={timestamp}&limit={count}

## COMMENT_SERVICE 

#### (Сервис комментариев)

Отвечает за управление комментариями, оставленными на фильмы и лайки, поставленные на комментарии. Сервис собирает статистику по количеству лайков на одном комментарии. 

**Эндпоинты**

POST /api/v1/comments

GET /api/v1/comments/{comment_id}

PUT /api/v1/comments/{comment_id}

DELETE /api/v1/comments/{comments_id}

POST /api/v1/comments/{comment_id}

POST /api/v1/comments/{comment_id}/likes

GET /api/v1/comments/{comment_id}/likes

GET /api/v1/comments/{comment_id}/likes/count

DELETE /api/v1/comments/{comment_id}/likes

**Фреймворк:** Flask.

#### 4. CHECK_SERVICE (Сервис проверки и AI)

Отвечает за модерацию контента и генерацию аналитики с помощью ИИ, также ежедневно генерирует текстовые обзоры на 5 самых популярных фильмов за прошлый день. Обзор создаётся, основываясь на одном случайном комментарии под фильмом. Логика создания ревью выбрана для большей случайности и субъективности обзора. Также сервис предоставляет возможность пользователям с правами admin отправить комментарий на проверку. Что не должен содержать комментарий: 1. Ненависть/дискриминация по признакам (раса, нация, религия, гендер и т.д.), 2. Призывы к насилию, угрозы, вред себе/другим, 3. Оскорбления, унижения, травля, 4. Сексуальный контент (особенно с участием несовершеннолетних) или чрезмерно непристойный, 5. Спам/реклама/фишинг. ИИ выносит вердикт, следует ли блокировать комментарий, но финальное решение за пользователем с правами admin. Также сервис предоставляет возможность пользователям с правами admin отправить пользователя кинотеатра на проверку. ИИ напишет отчёт о поведении пользователя, основываясь на прошлых отчётах, написанных о пользователе,  его комментариях. ИИ выносит вердикт, следует ли блокировать пользователя, но финальное решение за пользователем с правами admin.

**Эндпоинты**

POST /api/v1/check_comment

POST /api/v1/check_user

GET /api/v1/news

**Фреймворк:** FastAPI.

## Доска проекта на Miro:

https://miro.com/app/board/uXjVG-KlISw=/

