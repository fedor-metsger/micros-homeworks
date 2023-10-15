
# Ответы к домашнему заданию к занятию «Микросервисы: масштабирование»

## Задание 

Вы работаете в крупной компании, которая строит систему на основе микросервисной архитектуры.
Вам как DevOps-специалисту необходимо выдвинуть предложение по организации инфраструктуры для разработки и эксплуатации.

## Задача 1: Кластеризация

Предложите решение для обеспечения развёртывания, запуска и управления приложениями.
Решение может состоять из одного или нескольких программных продуктов и должно описывать способы и принципы их взаимодействия.

Решение должно соответствовать следующим требованиям:
- поддержка контейнеров;
- обеспечивать обнаружение сервисов и маршрутизацию запросов;
- обеспечивать возможность горизонтального масштабирования;
- обеспечивать возможность автоматического масштабирования;
- обеспечивать явное разделение ресурсов, доступных извне и внутри системы;
- обеспечивать возможность конфигурировать приложения с помощью переменных среды, в том числе с возможностью безопасного хранения чувствительных данных таких как пароли, ключи доступа, ключи шифрования и т. п.

Обоснуйте свой выбор.

## Решение

Для выполнения всех требований подойдёт ПО **Kubernetis**. Детализация представлена в таблице:

| Требование  | Функционал Kubernetis |
|-------------|-----------------------|
|поддержка контейнеров|Kubernetes работает со следующими средами исполнения контейнеров: **containerd**, **CRI-O**, **docker**
|обеспечивать обнаружение сервисов и маршрутизацию запросов|Kubernetes может предоставить доступ к контейнеру, используя DNS-имя или IP-адрес. Если трафик в контейнере высокий, Kubernetes может балансировать нагрузку и распределять сетевой трафик, для повышения стабильности
|обеспечивать возможность горизонтального масштабирования|Масштабирование приложения возможно вверх и вниз с помощью команды, с помощью GUI
|обеспечивать возможность автоматического масштабирования|Масштабирование приложения возможно автоматически в зависимости от загрузки
|обеспечивать явное разделение ресурсов, доступных извне и внутри системы|Kubernetes обеспечивает изоляцию ресурсов 
|обеспечивать возможность конфигурировать приложения с помощью переменных среды, в том числе с возможностью безопасного хранения чувствительных данных таких как пароли, ключи доступа, ключи шифрования и т.п|Kubernetes позволяет хранить конфиденциальную информацию, такую как пароли, токены OAuth и ключи SSH, и управлять ею. Возможно развертывать и обновлять секреты и конфигурацию приложения, не перестраивая образы контейнеров и не раскрывая секреты в конфигурации стека


### Задача 2: Распределённый кеш * (необязательная)

Разработчикам вашей компании понадобился распределённый кеш для организации хранения временной информации по сессиям пользователей.
Вам необходимо построить Redis Cluster, состоящий из трёх шард с тремя репликами.

### Схема:

![11-04-01](https://user-images.githubusercontent.com/1122523/114282923-9b16f900-9a4f-11eb-80aa-61ed09725760.png)

## Решение

Был создан файл конфигурации [docker-compose.yml](redis_cluster/docker-compose.yml),
а также файл конфигурации узла [redis.conf](redis_cluster/redis.conf).

Запуск:

```
fedor@fedor-X99-F8:~/CODE/Netology/DevOps/micros-homeworks/redis_cluster$ docker-compose up
Starting redis_3 ... done
Starting redis_2 ... done
Starting redis_1 ... done
Starting redis_cluster ... done
Attaching to redis_1, redis_3, redis_2, redis_cluster
redis_2          | 1:C 15 Oct 2023 08:09:30.844 # WARNING: Changing databases number from 16 to 1 since we are in cluster mode
redis_2          | 1:C 15 Oct 2023 08:09:30.844 # WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_2          | 1:C 15 Oct 2023 08:09:30.844 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_2          | 1:C 15 Oct 2023 08:09:30.844 * Redis version=7.2.1, bits=64, commit=00000000, modified=0, pid=1, just started
redis_2          | 1:C 15 Oct 2023 08:09:30.844 * Configuration loaded
redis_1          | 1:C 15 Oct 2023 08:09:30.831 # WARNING: Changing databases number from 16 to 1 since we are in cluster mode
redis_1          | 1:C 15 Oct 2023 08:09:30.831 # WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_cluster    | Unrecognized option or bad number of args for: '--requirepass'
redis_3          | 1:C 15 Oct 2023 08:09:30.837 # WARNING: Changing databases number from 16 to 1 since we are in cluster mode
redis_3          | 1:C 15 Oct 2023 08:09:30.837 # WARNING Memory overcommit must be enabled! Without it, a background save or replication may fail under low memory condition. Being disabled, it can also cause failures without low memory condition, see https://github.com/jemalloc/jemalloc/issues/1328. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_2          | 1:M 15 Oct 2023 08:09:30.845 * monotonic clock: POSIX clock_gettime
redis_2          | 1:M 15 Oct 2023 08:09:30.845 * Running mode=cluster, port=6379.
redis_2          | 1:M 15 Oct 2023 08:09:30.846 # WARNING: The TCP backlog setting of 65536 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 4096.
redis_1          | 1:C 15 Oct 2023 08:09:30.831 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1          | 1:C 15 Oct 2023 08:09:30.831 * Redis version=7.2.1, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1          | 1:C 15 Oct 2023 08:09:30.831 * Configuration loaded
redis_3          | 1:C 15 Oct 2023 08:09:30.837 * oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_3          | 1:C 15 Oct 2023 08:09:30.837 * Redis version=7.2.1, bits=64, commit=00000000, modified=0, pid=1, just started
redis_3          | 1:C 15 Oct 2023 08:09:30.837 * Configuration loaded
redis_1          | 1:M 15 Oct 2023 08:09:30.831 * monotonic clock: POSIX clock_gettime
redis_3          | 1:M 15 Oct 2023 08:09:30.838 * monotonic clock: POSIX clock_gettime
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * Node configuration loaded, I'm 50aea079e99f5c21737bb57c10100252c73cf1f8
redis_1          | 1:M 15 Oct 2023 08:09:30.831 * Running mode=cluster, port=6379.
redis_3          | 1:M 15 Oct 2023 08:09:30.838 * Running mode=cluster, port=6379.
redis_1          | 1:M 15 Oct 2023 08:09:30.831 # WARNING: The TCP backlog setting of 65536 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 4096.
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * Server initialized
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Node configuration loaded, I'm 1a794a58e1f7ae8822a02f0d63d9a815d64942c9
redis_3          | 1:M 15 Oct 2023 08:09:30.838 # WARNING: The TCP backlog setting of 65536 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 4096.
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * Reading RDB base file on AOF loading...
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * Loading RDB produced by version 7.2.1
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * RDB age 942 seconds
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Server initialized
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * RDB memory usage when created 1.51 Mb
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * RDB is base AOF
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * Done loading RDB, keys loaded: 0, keys expired: 0.
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Reading RDB base file on AOF loading...
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Loading RDB produced by version 7.2.1
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * RDB age 942 seconds
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * DB loaded from base file appendonly.aof.1.base.rdb: 0.000 seconds
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * RDB memory usage when created 1.51 Mb
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * RDB is base AOF
redis_3          | 1:M 15 Oct 2023 08:09:30.838 * Node configuration loaded, I'm c0a84ee796e762074753f96d4bb9ea64a818b320
redis_2          | 1:M 15 Oct 2023 08:09:30.846 * DB loaded from append only file: 0.000 seconds
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Done loading RDB, keys loaded: 0, keys expired: 0.
redis_2          | 1:M 15 Oct 2023 08:09:30.847 * Opening AOF incr file appendonly.aof.1.incr.aof on server start
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * Server initialized
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * DB loaded from base file appendonly.aof.1.base.rdb: 0.000 seconds
redis_2          | 1:M 15 Oct 2023 08:09:30.847 * Ready to accept connections tcp
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * Reading RDB base file on AOF loading...
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * Loading RDB produced by version 7.2.1
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * RDB age 918 seconds
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * RDB memory usage when created 1.51 Mb
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * RDB is base AOF
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * DB loaded from append only file: 0.000 seconds
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * Done loading RDB, keys loaded: 0, keys expired: 0.
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Opening AOF incr file appendonly.aof.1.incr.aof on server start
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * DB loaded from base file appendonly.aof.1.base.rdb: 0.000 seconds
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * DB loaded from append only file: 0.000 seconds
redis_1          | 1:M 15 Oct 2023 08:09:30.832 * Ready to accept connections tcp
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * Opening AOF incr file appendonly.aof.1.incr.aof on server start
redis_3          | 1:M 15 Oct 2023 08:09:30.839 * Ready to accept connections tcp
redis_cluster exited with code 1
```

Подключение к кластеру:

```
fedor@fedor-X99-F8:~$ redis-cli -p 6381 -a qwerty123456
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
127.0.0.1:6381> 

```
