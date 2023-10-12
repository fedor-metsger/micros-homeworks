
# Ответы к домашнему заданию к занятию «Микросервисы: принципы»

## Задание

Вы работаете в крупной компании, которая строит систему на основе микросервисной архитектуры.
Вам как DevOps-специалисту необходимо выдвинуть предложение по организации инфраструктуры для разработки и эксплуатации.

### Задача 1: API Gateway 

Предложите решение для обеспечения реализации API Gateway. Составьте сравнительную таблицу возможностей различных программных решений. На основе таблицы сделайте выбор решения.

Решение должно соответствовать следующим требованиям:
- маршрутизация запросов к нужному сервису на основе конфигурации,
- возможность проверки аутентификационной информации в запросах,
- обеспечение терминации HTTPS.

Обоснуйте свой выбор.

### Решение:

Решение составлено на основании статьи
["Обзор и сравнение контроллеров Ingress для Kubernetes"
](https://habr.com/ru/companies/flant/articles/447180/)

На рынке сейчас присутствует большой выбор различных программных решений для организации
API Gateway, сравнительные характеристики некоторых (не всех) я вывел в следующую таблицу:

| Возможности | Ingress от Kubernetes | NGINX  | Kong | Traefik   | HAProxy | Contour | 
|-|---------|--------------------|------|-----------|---------|---------|
| маршрутизация запросов к нужному сервису на основе конфигурации | + | + | + | **+**     | + | - |
| возможность JWT-валидации | - | + в платной версии | +| - | + | -|
| возможность проверки аутентификационной информации в запросах | + | Только Basic | + | **+**     | +| - |
| обеспечение терминации HTTPS | + | + | +    | **+**     | +       | +       |
| Количество звёзд на GitHub  | 4338 | 1469 | 434 | **21554** | 396 | 1523 |

Видно, что возможности у всех решений практически одинаковые.
Ввиду популярности и наиболее активного развития я бы предпочёл использовать продукт **Traefik**.

### Задача 2: Брокер сообщений

Составьте таблицу возможностей различных брокеров сообщений. На основе таблицы сделайте обоснованный выбор решения.

Решение должно соответствовать следующим требованиям:
- поддержка кластеризации для обеспечения надёжности,
- хранение сообщений на диске в процессе доставки,
- высокая скорость работы,
- поддержка различных форматов сообщений,
- разделение прав доступа к различным потокам сообщений,
- простота эксплуатации.

Обоснуйте свой выбор.

### Решение:

Решение составлено на основании статей:

- ["Выбор брокера сообщений в качестве шины данных"
](https://habr.com/ru/articles/326880/)
- ["Выбор MQ для высоконагруженного проекта"
](https://habr.com/ru/companies/innotech/articles/698838/)


Как и в случае с API gateway, на рынке присутствует значительное количество
решений для брокеров сообщений. Каждый раз лучше выбирать конкретное решение
в зависимости от конкретной задачи.
Тем не менее, я составил сравнительную таблицу некоторых популярных решений:

| Возможности          | RabbitMQ     | Kafka       | REDIS | 
|----------------------|--------------|-------------|----|
| Поддержка кластеризации для обеспечения надёжности| модель «лидер – последователь», требует сетей с малой задержкой | +  | +  |
| Хранение сообщений на диске в процессе доставки| +, после доставки недоступны                                    | +  | - |
| Высокая скорость работы| минимальная | средняя | большая |
| Поддержка различных форматов сообщений| наибольшая | средняя     | минимальная |
| Разделение прав доступа к различным потокам сообщений| + | + | + |
| Простота эксплуатации| средняя  | минимальная | максимальная |

По результатам сравнения для задач обучения я бы выбрал решение **RabbitMQ**.

## Задача 3: API Gateway * (необязательная)

### Есть три сервиса:

**minio**
- хранит загруженные файлы в бакете images,
- S3 протокол,

**uploader**
- принимает файл, если картинка сжимает и загружает его в minio,
- POST /v1/upload,

**security**
- регистрация пользователя POST /v1/user,
- получение информации о пользователе GET /v1/user,
- логин пользователя POST /v1/token,
- проверка токена GET /v1/token/validation.

### Необходимо воспользоваться любым балансировщиком и сделать API Gateway:

**POST /v1/register**
1. Анонимный доступ.
2. Запрос направляется в сервис security POST /v1/user.

**POST /v1/token**
1. Анонимный доступ.
2. Запрос направляется в сервис security POST /v1/token.

**GET /v1/user**
1. Проверка токена. Токен ожидается в заголовке Authorization. Токен проверяется через вызов сервиса security GET /v1/token/validation/.
2. Запрос направляется в сервис security GET /v1/user.

**POST /v1/upload**
1. Проверка токена. Токен ожидается в заголовке Authorization. Токен проверяется через вызов сервиса security GET /v1/token/validation/.
2. Запрос направляется в сервис uploader POST /v1/upload.

**GET /v1/user/{image}**
1. Проверка токена. Токен ожидается в заголовке Authorization. Токен проверяется через вызов сервиса security GET /v1/token/validation/.
2. Запрос направляется в сервис minio GET /images/{image}.

### Ожидаемый результат

Результатом выполнения задачи должен быть docker compose файл, запустив который можно локально выполнить следующие команды с успешным результатом.
Предполагается, что для реализации API Gateway будет написан конфиг для NGinx или другого балансировщика нагрузки, который будет запущен как сервис через docker-compose и будет обеспечивать балансировку и проверку аутентификации входящих запросов.

**Авторизация**

curl -X POST -H 'Content-Type: application/json' -d '{"login":"bob", "password":"qwe123"}' http://localhost/token

**Загрузка файла**

curl -X POST -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2IifQ.hiMVLmssoTsy1MqbmIoviDeFPvo-nCd92d4UFiN2O2I' -H 'Content-Type: octet/stream' --data-binary @yourfilename.jpg http://localhost/upload

**Получение файла**

curl -X GET http://localhost/images/4e6df220-295e-4231-82bc-45e4b1484430.jpg

### Решение

Был написан файл конфигурации [nginx.conf](11-microservices-02-principles/gateway/nginx.conf):
<details>
  <summary>nginx.conf</summary>

```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log notice;
pid        /var/run/nginx.pid;

events {
    worker_connections  1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;
    sendfile        on;
    keepalive_timeout  65;

# Default server configuration
#
    server {
        listen 80 default_server;

        root /var/www/html;

        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location /v1/token {
              proxy_pass http://security:3000/v1/token;
        }

        location /v1/user {
              proxy_pass http://security:3000/v1/token/validation;
        }

        location /v1/upload {
              proxy_pass http://uploader:3000/v1/upload/;
        }

        location /v1/image {
              proxy_pass http://storage/images/;
        }
    }
}
```
</details>

**Запуск**:

<details>
  <summary>Развернуть</summary>

```
fedor@fedor-X99-F8:~/CODE/Netology/DevOps/micros-homeworks/11-microservices-02-principles$ docker-compose up
Starting 11-microservices-02-principles_storage_1    ... done
Recreating 11-microservices-02-principles_security_1    ... done
Starting 11-microservices-02-principles_createbuckets_1 ... done
Starting 11-microservices-02-principles_uploader_1      ... done
Recreating 11-microservices-02-principles_gateway_1     ... done
Attaching to 11-microservices-02-principles_storage_1, 11-microservices-02-principles_security_1, 11-microservices-02-principles_createbuckets_1, 11-microservices-02-principles_uploader_1, 11-microservices-02-principles_gateway_1
gateway_1        | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
gateway_1        | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
security_1       |  * Serving Flask app "server" (lazy loading)
security_1       |  * Environment: production
createbuckets_1  | Added `storage` successfully.
gateway_1        | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
security_1       |    WARNING: This is a development server. Do not use it in a production deployment.
security_1       |    Use a production WSGI server instead.
createbuckets_1  | Bucket created successfully `storage/data`.
storage_1        | MinIO Object Storage Server
storage_1        | Copyright: 2015-2023 MinIO, Inc.
storage_1        | License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
storage_1        | Version: RELEASE.2023-10-07T15-07-38Z (go1.21.1 linux/amd64)
storage_1        | 
gateway_1        | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
gateway_1        | 10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
createbuckets_1  | mc: Please use 'mc anonymous'
storage_1        | Status:         1 Online, 0 Offline. 
storage_1        | S3-API: http://172.22.0.2:9000  http://127.0.0.1:9000 
security_1       |  * Debug mode: off
storage_1        | Console: http://172.22.0.2:40453 http://127.0.0.1:40453 
storage_1        | 
storage_1        | Documentation: https://min.io/docs/minio/linux/index.html
storage_1        | Warning: The standard parity is set to 0. This can lead to data loss.
gateway_1        | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
uploader_1       | S3: storage:9000 data
security_1       |  * Running on all addresses.
security_1       |    WARNING: This is a development server. Do not use it in a production deployment.
security_1       |  * Running on http://172.22.0.3:3000/ (Press CTRL+C to quit)
gateway_1        | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
uploader_1       | Listening on port 3000
gateway_1        | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
gateway_1        | /docker-entrypoint.sh: Configuration complete; ready for start up
uploader_1       | (node:1) [DEP0152] DeprecationWarning: Custom PerformanceEntry accessors are deprecated. Please use the detail property.
uploader_1       | (Use `node --trace-deprecation ...` to show where the warning was created)
11-microservices-02-principles_createbuckets_1 exited with code 0
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: using the "epoll" event method
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: nginx/1.25.2
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: built by gcc 12.2.1 20220924 (Alpine 12.2.1_git20220924-r10) 
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: OS: Linux 5.15.0-83-generic
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker processes
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 31
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 32
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 33
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 34
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 35
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 36
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 37
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 38
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 39
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 40
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 41
gateway_1        | 2023/10/12 17:36:04 [notice] 1#1: start worker process 42
```
</details>

**Вывод команды** `docker ps`:

```
fedor@fedor-X99-F8:~/CODE/auth$ docker ps
CONTAINER ID   IMAGE                                     COMMAND                  CREATED             STATUS                   PORTS                                       NAMES
3597085eaa0a   nginx:alpine                              "/docker-entrypoint.…"   8 minutes ago       Up 8 minutes             0.0.0.0:80->80/tcp, :::80->80/tcp           11-microservices-02-principles_gateway_1
c113b06568e2   11-microservices-02-principles_security   "python ./server.py"     8 minutes ago       Up 8 minutes             0.0.0.0:3001->3000/tcp, :::3001->3000/tcp   11-microservices-02-principles_security_1
921032774c06   11-microservices-02-principles_uploader   "docker-entrypoint.s…"   12 minutes ago      Up 8 minutes             0.0.0.0:3000->3000/tcp, :::3000->3000/tcp   11-microservices-02-principles_uploader_1
731c332e04e2   minio/minio:latest                        "/usr/bin/docker-ent…"   About an hour ago   Up 8 minutes (healthy)   9000/tcp                                    11-microservices-02-principles_storage_1
```
</details>

**Получение токена**:
```
fedor@fedor-X99-F8:~/CODE/auth$ curl -L -X POST -H 'Content-Type: application/json' -d '{"login":"bob", "password":"qwe123"}' http://localhost/v1/token
{"token":"eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2IifQ.hiMVLmssoTsy1MqbmIoviDeFPvo-nCd92d4UFiN2O2I"}
```

**Валидация**:
```
fedor@fedor-X99-F8:~/CODE/auth$ curl -X GET -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2IifQ.hiMVLmssoTsy1MqbmIoviDeFPvo-nCd92d4UFiN2O2I' http://localhost/v1/user
{"sub":"bob"}
```

**Загрузка картинки на сервер**:
```
fedor@fedor-X99-F8:~/CODE/auth$ curl -X POST -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2IifQ.hiMVLmssoTsy1MqbmIoviDeFPvo-nCd92d4UFiN2O2I' -H 'Content-Type: octet/stream' --data-binary @/home/fedor/Desktop/card.png http://localhost/v1/upload
{"filename":"58266785-81e4-4b96-8a73-38133dea195a.png"}
```

**Скачивание с сервера**:
```
fedor@fedor-X99-F8:~/CODE/auth$ curl -f -X GET -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJib2IifQ.hiMVLmssoTsy1MqbmIoviDeFPvo-nCd92d4UFiN2O2I' http://localhost/v1/image/58266785-81e4-4b96-8a73-38133dea195a.png > ~/Desktop/test.png
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  4758  100  4758    0     0  1408k      0 --:--:-- --:--:-- --:--:-- 1548k
```
