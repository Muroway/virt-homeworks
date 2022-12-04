# Домашнее задание к занятию "6.3. MySQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя docker поднимите инстанс MySQL (версию 8). Данные БД сохраните в volume.

```
docker pull mysql:8
Status: Downloaded newer image for mysql:8
docker.io/library/mysql:8
```
```
docker volume create mysql_vol
mysql_vol
```
```
docker run -it --rm --name mysql_doc -e MYSQL_ROOT_PASSWORD=admin -p -v mysql_vol:/etc/mysql/ mysql:8
```

---
А дальше MySQL выдал такую картину на двух маках и трех виртуалках:
```
2022-12-04 18:50:21+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2022-12-04 18:50:23+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2022-12-04 18:50:23+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2022-12-04 18:50:24+00:00 [Note] [Entrypoint]: Initializing database files
2022-12-04T18:50:24.658778Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2022-12-04T18:50:24.709120Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 80
2022-12-04T18:50:24.869617Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-12-04T18:50:27.079935Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-12-04T18:50:33.585960Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2022-12-04 18:50:44+00:00 [Note] [Entrypoint]: Database files initialized
2022-12-04 18:50:44+00:00 [Note] [Entrypoint]: Starting temporary server
mysqld will log errors to /var/lib/mysql/945ae7d6b0a3.err
mysqld is running as pid 133
2022-12-04 18:50:47+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.

2022-12-04 18:50:54+00:00 [Note] [Entrypoint]: Stopping temporary server
2022-12-04 18:50:58+00:00 [Note] [Entrypoint]: Temporary server stopped

2022-12-04 18:50:58+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

2022-12-04T18:50:59.189017Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2022-12-04T18:50:59.193951Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 1
2022-12-04T18:50:59.218816Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-12-04T18:50:59.741140Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-12-04T18:51:00.468713Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2022-12-04T18:51:00.469191Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2022-12-04T18:51:00.476496Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2022-12-04T18:51:00.578442Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2022-12-04T18:51:00.579530Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.
sudo docker run -t --name mysql_doc -e MYSQL_ROOT_PASSWORD=admin -v mysqlvol:/etc/mysql/ mysql:8.0
2022-12-04 19:09:47+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2022-12-04 19:09:48+00:00 [Note] [Entrypoint]: Switching to dedicated user 'mysql'
2022-12-04 19:09:48+00:00 [Note] [Entrypoint]: Entrypoint script for MySQL Server 8.0.31-1.el8 started.
2022-12-04 19:09:48+00:00 [Note] [Entrypoint]: Initializing database files
2022-12-04T19:09:48.893625Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2022-12-04T19:09:48.894020Z 0 [System] [MY-013169] [Server] /usr/sbin/mysqld (mysqld 8.0.31) initializing of server in progress as process 80
2022-12-04T19:09:48.918331Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-12-04T19:09:51.146132Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-12-04T19:09:58.033055Z 6 [Warning] [MY-010453] [Server] root@localhost is created with an empty password ! Please consider switching off the --initialize-insecure option.
2022-12-04 19:10:10+00:00 [Note] [Entrypoint]: Database files initialized
2022-12-04 19:10:10+00:00 [Note] [Entrypoint]: Starting temporary server
mysqld will log errors to /var/lib/mysql/f26214d11f6f.err
mysqld is running as pid 133
2022-12-04 19:10:11+00:00 [Note] [Entrypoint]: Temporary server started.
'/var/lib/mysql/mysql.sock' -> '/var/run/mysqld/mysqld.sock'
Warning: Unable to load '/usr/share/zoneinfo/iso3166.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/leapseconds' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/tzdata.zi' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone.tab' as time zone. Skipping it.
Warning: Unable to load '/usr/share/zoneinfo/zone1970.tab' as time zone. Skipping it.

2022-12-04 19:10:21+00:00 [Note] [Entrypoint]: Stopping temporary server
2022-12-04 19:10:25+00:00 [Note] [Entrypoint]: Temporary server stopped

2022-12-04 19:10:25+00:00 [Note] [Entrypoint]: MySQL init process done. Ready for start up.

2022-12-04T19:10:26.293245Z 0 [Warning] [MY-011068] [Server] The syntax '--skip-host-cache' is deprecated and will be removed in a future release. Please use SET GLOBAL host_cache_size=0 instead.
2022-12-04T19:10:26.298732Z 0 [System] [MY-010116] [Server] /usr/sbin/mysqld (mysqld 8.0.31) starting as process 1
2022-12-04T19:10:26.324689Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2022-12-04T19:10:26.955833Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2022-12-04T19:10:27.872131Z 0 [Warning] [MY-010068] [Server] CA certificate ca.pem is self signed.
2022-12-04T19:10:27.872269Z 0 [System] [MY-013602] [Server] Channel mysql_main configured to support TLS. Encrypted connections are now supported for this channel.
2022-12-04T19:10:27.880699Z 0 [Warning] [MY-011810] [Server] Insecure configuration for --pid-file: Location '/var/run/mysqld' in the path is accessible to all OS users. Consider choosing a different directory.
2022-12-04T19:10:27.994050Z 0 [System] [MY-011323] [Server] X Plugin ready for connections. Bind-address: '::' port: 33060, socket: /var/run/mysqld/mysqlx.sock
2022-12-04T19:10:27.994665Z 0 [System] [MY-010931] [Server] /usr/sbin/mysqld: ready for connections. Version: '8.0.31'  socket: '/var/run/mysqld/mysqld.sock'  port: 3306  MySQL Community Server - GPL.



```

На последнем этапе завис, и не совсем понятно, что с ним делать дальше.

---

**ДЗ на доработку, потому что не успеваю с этим бороться.**

---


Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-03-mysql/test_data) и 
восстановитесь из него.

Перейдите в управляющую консоль `mysql` внутри контейнера.

Используя команду `\h` получите список управляющих команд.

Найдите команду для выдачи статуса БД и **приведите в ответе** из ее вывода версию сервера БД.

Подключитесь к восстановленной БД и получите список таблиц из этой БД.

**Приведите в ответе** количество записей с `price` > 300.

В следующих заданиях мы будем продолжать работу с данным контейнером.

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
- плагин авторизации mysql_native_password
- срок истечения пароля - 180 дней 
- количество попыток авторизации - 3 
- максимальное количество запросов в час - 100
- аттрибуты пользователя:
    - Фамилия "Pretty"
    - Имя "James"

Предоставьте привелегии пользователю `test` на операции SELECT базы `test_db`.
    
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю `test` и 
**приведите в ответе к задаче**.

## Задача 3

Установите профилирование `SET profiling = 1`.
Изучите вывод профилирования команд `SHOW PROFILES;`.

Исследуйте, какой `engine` используется в таблице БД `test_db` и **приведите в ответе**.

Измените `engine` и **приведите время выполнения и запрос на изменения из профайлера в ответе**:
- на `MyISAM`
- на `InnoDB`

## Задача 4 

Изучите файл `my.cnf` в директории /etc/mysql.

Измените его согласно ТЗ (движок InnoDB):
- Скорость IO важнее сохранности данных
- Нужна компрессия таблиц для экономии места на диске
- Размер буффера с незакомиченными транзакциями 1 Мб
- Буффер кеширования 30% от ОЗУ
- Размер файла логов операций 100 Мб

Приведите в ответе измененный файл `my.cnf`.

---

### Как оформить ДЗ?

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
