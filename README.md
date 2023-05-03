# yamdb_final
[![Django-app workflow](https://github.com/Aleksander-VLZ/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)](https://github.com/Aleksander-VLZ/yamdb_final/actions/workflows/yamdb_workflow.yml)

====
API для получения информации и обсуждения наиболее интересных произведений.
Для автоматизации развертывания на боевых серверах используется среда виртуализации Docker, а также Docker-compose - инструмент для запуска многоконтейнерных приложений.

## Стек технологий

- Python 3
- DRF (Django REST framework)
- Django ORM
- Docker
- Gunicorn
- nginx
- Яндекс Облако(Ubuntu 18.04)
- Django 2.2 TLS
- PostgreSQL
- GIT

## О проекте

Проект доступен по адресу : 

http://158.160.67.160/admin/

http://158.160.67.160/redoc/
Реализована регистрация с кодом подтверждения и дальнейшая авторизация с использованием JWT токена, при отправке запроса к API.

Проект YaMDb собирает отзывы пользователей на произведения.
Произведения делятся на категории: «Книги», «Фильмы», «Музыка».
Список категорий может быть расширен администратором.

### Документация и возможности API

К проекту подключен redoc. Для просмотра документации используйте эндпойнт `redoc/`

[Добавлена менеджмент команда, для выгрузки данных в БД, из csv.]
(#описание-команды-для-заполнения-базы-данными)

## Шаблон наполнения .env

```
# указываем, с какой БД работаем
DB_ENGINE=django.db.backends.postgresql
# имя базы данных
DB_NAME=
# логин для подключения к базе данных
POSTGRES_USER=
# пароль для подключения к БД (установите свой)
POSTGRES_PASSWORD=
# название сервиса (контейнера)
DB_HOST=
# порт для подключения к БД
DB_PORT=
SECRET_KEY=<секретный ключ проекта django>

```

## Автоматизация развертывания серверного ПО

Для автоматизации развертывания ПО на боевых серверах используется среда виртуализации Docker, а также Docker-compose - инструмент для запуска многоконтейнерных приложений. Docker позволяет «упаковать» приложение со всем его окружением и зависимостями в контейнер, который может быть перенесён на любую Linux -систему, а также предоставляет среду по управлению контейнерами. Таким образом, для разворачивания серверного ПО достаточно чтобы на сервере с ОС семейства Linux были установлены среда Docker и инструмент Docker-compose.


## Описание команд для запуска приложения в контейнерах

Для запуска проекта в контейнерах используем **docker-compose** : ```docker-compose up -d --build```, находясь в директории с ```docker-compose.yaml```

После сборки контейнеров выполяем:

```bash
# Выполняем миграции
docker-compose exec web python manage.py migrate
# Создаем суперппользователя
docker-compose exec web python manage.py createsuperuser
# Собираем статику со всего проекта
docker-compose exec web python manage.py collectstatic --no-input
# Для дампа данных из БД
docker-compose exec web python manage.py dumpdata > dump.json
```
Пройдите по адресу http://localhost/admin/ , авторизуйтесь как созданный выше суперпользователь,
и внесите записи в базу данных через админ панель.

### Для выгрузки данных из дампа (резервной копии) в БД

```bash
docker-compose exec web bash
# Сброс БД, суперюзеры так же удаляются
>>> python manage.py flush

>>> python3 manage.py shell  
        # выполнить в открывшемся терминале:
>>>>>> from django.contrib.contenttypes.models import ContentType
>>>>>> ContentType.objects.all().delete()
>>>>>> quit()

>>> python manage.py loaddata dump.json
```

## Описание команды для заполнения БД данными из csv

```python manage.py import_csv_to_db``` в контейнере web (```docker-compose exec web bash```)

>Это - менеджмент команда.
>
Выгружаются данные из файлов директории ```api_yamdb/static```

НО, прежде выполнить миграции! 
В случае изменения названий моделей/csv файлов и которых брать данне - изменить настройки в `api_yamdb/reviews/management/commands/_settings_for_import.py`, а именно:

```python
NEED_TO_PARSE = {
'users.csv': User,
'category.csv': Category,
'genre.csv': Genre,
'titles.csv': Title,
'genre_title.csv': Genre_title,
'review.csv': Review,
'comments.csv': Comment,
}
```

## Авторы

- Александр Ратушнюк

- Евгений Березовский

- Наталья Петелина
