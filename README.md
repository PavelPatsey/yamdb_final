# API_YamDB

![API_YamDB workflow](https://github.com/PavelPatsey/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg)

REST API для сервиса YaMDb — базы отзывов о фильмах, книгах и музыке.

Проект YaMDb собирает отзывы пользователей на произведения. Произведения делятся на категории: «Книги», «Фильмы», «Музыка».
Произведению может быть присвоен жанр. Новые жанры может создавать только администратор.
Читатели оставляют к произведениям текстовые отзывы и выставляют произведению рейтинг (оценку в диапазоне от одного до десяти).
Из множества оценок автоматически высчитывается средняя оценка произведения.

Аутентификация по JWT-токену

Поддерживает методы GET, POST, PUT, PATCH, DELETE

Предоставляет данные в формате JSON

Cоздан в команде из трёх человек с использованим Git в рамках учебного курса Яндекс.Практикум.

## Стек технологий

- проект написан на Python с использованием Django REST Framework
- библиотека Simple JWT - работа с JWT-токеном
- библиотека django-filter - фильтрация запросов
- базы данных - PostgreSQL
- система управления версиями - git
- [Docker](https://docs.docker.com/engine/install/ubuntu/) - официальная документация Docker
- [Dockerfile](https://docs.docker.com/engine/reference/builder/) - официальная документация Dockerfile
- [Docker Compose](https://docs.docker.com/compose/) - официальная документация Docker Compose

## Ресурсы API YaMDb

**AUTH**: аутентификация.

**USERS**: пользователи.

**TITLES**: произведения, к которым пишут отзывы (определённый фильм, книга или песенка).

**CATEGORIES**: категории (типы) произведений ("Фильмы", "Книги", "Музыка").

**GENRES**: жанры произведений. Одно произведение может быть привязано к нескольким жанрам.

**REVIEWS**: отзывы на произведения. Отзыв привязан к определённому произведению.

**COMMENTS**: комментарии к отзывам. Комментарий привязан к определённому отзыву.

## Алгоритм регистрации пользователей

Пользователь отправляет POST-запрос с параметром email на `/api/v1/auth/email/`.
YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на адрес email.
Пользователь отправляет POST-запрос с параметрами email и confirmation_code на `/api/v1/auth/token/`, в ответе на запрос ему приходит token (JWT-токен).
Эти операции выполняются один раз, при регистрации пользователя. В результате пользователь получает токен и может работать с API, отправляя этот токен с каждым запросом.

## Пользовательские роли

**Аноним** — может просматривать описания произведений, читать отзывы и комментарии.

**Аутентифицированный пользователь (user)** — может читать всё, как и Аноним, дополнительно может публиковать отзывы и ставить рейтинг произведениям (фильмам/книгам/песенкам), может комментировать чужие отзывы и ставить им оценки; может редактировать и удалять свои отзывы и комментарии.

**Модератор (moderator)** — те же права, что и у Аутентифицированного пользователя плюс право удалять и редактировать любые отзывы и комментарии.

**Администратор (admin)** — полные права на управление проектом и всем его содержимым. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.

**Администратор Django** — те же права, что и у роли Администратор.

## Подготовка и запуск проекта
* Склонируйте репозиторий на локальную машину.

### Для работы с удаленным сервером (на ubuntu):
* Выполните вход на свой удаленный сервер.

* Установите docker на сервер:
```
sudo apt install docker.io 
```
* Установите docker-compose на сервер. [Установка и использование Docker Compose в Ubuntu 20.04](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-compose-on-ubuntu-20-04-ru)
* Локально отредактируйте файл infra/nginx/default.conf.conf, в строке server_name впишите свой IP
* Скопируйте файл docker-compose.yml и папку nginx из папки infra на сервер:
```
scp infra/docker-compose.yaml <username>@<ip host>:/home/<username>/docker-compose.yml
scp infra/nginx/default.conf <username>@<ip host>:/home/<username>/nginx/default.conf
```

* Для работы с Workflow добавьте в Secrets GitHub переменные окружения для работы:
    ```
    DB_ENGINE=<django.db.backends.postgresql>
    DB_NAME=<имя базы данных postgres>
    DB_USER=<пользователь бд>
    DB_PASSWORD=<пароль>
    DB_HOST=<db>
    DB_PORT=<5432>
    
    DOCKER_PASSWORD=<пароль от DockerHub>
    DOCKER_USERNAME=<имя пользователя на DockerHub>
    
    SECRET_KEY=<секретный ключ проекта django>

    USER=<username для подключения к серверу>
    HOST=<IP сервера>
    SSH_KEY=<ваш SSH ключ (для получения выполните команду: cat ~/.ssh/id_rsa)>
    PASSPHRASE=<если при создании ssh-ключа вы использовали фразу-пароль>

    TELEGRAM_TO=<ID чата, в который придет сообщение, узнать свой ID можно у бота @userinfobot>
    TELEGRAM_TOKEN=<токен вашего бота, получить этот токен можно у бота @BotFather>
    ```
* Workflow состоит из четырех шагов:
     - Проверка кода на соответствие PEP8 и выполнение тестов, реализованных в проекте
     - Сборка и публикация образа приложения на DockerHub.
     - Автоматическое скачивание образа приложения и деплой на удаленном сервере.
     - Отправка уведомления в телеграм-чат.  
  

* После успешного развертывания проекта на удаленном сервере, можно выполнить:
    - Заполнить БД начальными данными (необязательно):  
    ```
    docker-compose exec web python manage.py loaddata fixtures.json
    ```
    - Создать суперпользователя Django:
    ```
    sudo docker-compose exec web python manage.py createsuperuser
    ```
    - Проект будет доступен по IP вашего сервера.
  
## Проект в интернете
Проект запущен и доступен по адресу [http://51.250.109.204/admin/](http://51.250.109.204/admin/)
## Как пользоваться

После запуска проекта, подробную инструкцию можно будет посмотреть по адресу [http://51.250.109.204/redoc/](http://51.250.109.204/redoc/)

В проекте реализована эмуляция почтового сервера, письма сохраняются в папке /sent_emails в головной директории проекта. Для того чтобы посмотреть содержимое писем выполните команду на сервере:
```
sudo docker exec -it <CONTAINER ID контейнера web> bash
```
Перейдите в папку /sent_emails, с помощью команды, прочитайте содержмое лог файла:
```
tail <имя log файла>
```
