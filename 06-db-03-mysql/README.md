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
 
docker volume create mysql_vol
mysql_vol
 
docker run -it --rm --name mysql_doc -e MYSQL_ROOT_PASSWORD=admin -p 3306:3306  -v mysql_vol:/etc/mysql/ mysql:8
```

Изучите бэкап БД и восстановитесь из него.
```
mysql> CREATE DATABASE test_db
```
``` 
docker exec -t mysql_doc sh
sh-4.4# mysql -uroot -padmin test_db < test_dump.sql
mysql: [Warning] Using a password on the command line interface can be insecure.
```
 
Перейдите в управляющую консоль mysql внутри контейнера.
```
docker exec -it mysql_doc mysql -uroot -p
```
Используя команду \h получите список управляющих команд.
Найдите команду для выдачи статуса БД и приведите в ответе из ее вывода версию сервера БД.
 
 
Подключитесь к восстановленной БД и получите список таблиц из этой БД.
 
```
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| test_db            |
+--------------------+
```

```
mysql> use test_db
Database changed
mysql> show tables;
+-------------------+
| Tables_in_test_db |
+-------------------+
| orders            |
+-------------------+
1 row in set (0.00 sec)
```

Приведите в ответе количество записей с price > 300.

```
mysql> SELECT count(*) FROM orders WHERE price >300;
+----------+
| count(*) |
+----------+
|        1 |
+----------+
1 row in set (0.00 sec)
```

В следующих заданиях мы будем продолжать работу с данным контейнером.

## Задача 2

Создайте пользователя test в БД c паролем test-pass, используя:
плагин авторизации 
 
```
mysql> CREATE USER test@localhost IDENTIFIED WITH mysql_native_password BY 'test-pass';
Query OK, 0 rows affected (0.08 sec)
```
 
срок истечения пароля - 180 дней
```
mysql> ALTER USER 'test'@'localhost' PASSWORD EXPIRE INTERVAL 180 DAY;
Query OK, 0 rows affected (0.04 sec)
```
 
количество попыток авторизации - 3
```
mysql> ALTER USER 'test'@'localhost' FAILED_LOGIN_ATTEMPTS 3 PASSWORD_LOCK_TIME 2;
Query OK, 0 rows affected (0.01 sec)
```
---
**NOTE**

ALTER USER Statement

https://dev.mysql.com/doc/refman/8.0/en/alter-user.html

Настройка привилегий 

https://selectel.ru/blog/tutorials/how-to-create-a-new-user-and-set-privileges-in-mysql/

---
 
 
максимальное количество запросов в час - 100

```
mysql> ALTER USER 'test'@'localhost' WITH MAX_QUERIES_PER_HOUR 100;
Query OK, 0 rows affected (0.02 sec)
```

аттрибуты пользователя:
Фамилия "Pretty"
Имя "James"

```
mysql> ALTER USER 'test'@'localhost' ATTRIBUTE '{"last name": "Pretty", "firstname": "James"}';
Query OK, 0 rows affected (0.08 sec)
```

Предоставьте привелегии пользователю test на операции SELECT базы test_db.

```
mysql> GRANT SELECT ON test_db.orders TO 'test'@'localhost';
Query OK, 0 rows affected, 1 warning (0.02 sec)
```
```
mysql> SHOW GRANTS FOR test@localhost;
+----------------------------------------------------------+
| Grants for test@localhost                                |
+----------------------------------------------------------+
| GRANT USAGE ON *.* TO `test`@`localhost`                 |
| GRANT SELECT ON `test_db`.`orders` TO `test`@`localhost` |
+----------------------------------------------------------+
2 rows in set (0.00 sec)
```
  
Используя таблицу INFORMATION_SCHEMA.USER_ATTRIBUTES получите данные по пользователю test и приведите в ответе к задаче.

```
mysql> SELECT * FROM INFORMATION_SCHEMA.USER_ATTRIBUTES WHERE USER='test' AND HOST='localhost';
+------+-----------+-----------------------------------------------+
| USER | HOST      | ATTRIBUTE                                     |
+------+-----------+-----------------------------------------------+
| test | localhost | {"firstname": "James", "last name": "Pretty"} |
+------+-----------+-----------------------------------------------+
1 row in set (0.01 sec)
```

## Задача 3
Установите профилирование SET profiling = 1. Изучите вывод профилирования команд SHOW PROFILES;.
Исследуйте, какой engine используется в таблице БД test_db и приведите в ответе.
```
mysql> SELECT TABLE_NAME,ENGINE,ROW_FORMAT,TABLE_ROWS,DATA_LENGTH,INDEX_LENGTH FROM information_schema.TABLES WHERE TABLE_SCHEMA = 'test_db' ORDER BY ENGINE asc;
+------------+--------+------------+------------+-------------+--------------+
| TABLE_NAME | ENGINE | ROW_FORMAT | TABLE_ROWS | DATA_LENGTH | INDEX_LENGTH |
+------------+--------+------------+------------+-------------+--------------+
| orders     | InnoDB | Dynamic    |          5 |       16384 |            0 |
+------------+--------+------------+------------+-------------+--------------+
1 row in set (0.07 sec)
```
 
Измените engine и приведите время выполнения и запрос на изменения из профайлера в ответе:
на MyISAM
```
mysql> ALTER TABLE orders ENGINE = MyISAM;
Query OK, 5 rows affected (0.06 sec)
Records: 5  Duplicates: 0  Warnings: 0
```
на InnoDB
```
mysql> ALTER TABLE orders ENGINE = InnoDB;
Query OK, 5 rows affected (0.06 sec)
Records: 5  Duplicates: 0  Warnings: 0
```
 
```
mysql> SHOW PROFILES;
+----------+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
| Query_ID | Duration   | Query                                                                                                                                                     |
+----------+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
|        1 | 0.05294575 | SHOW ENGINE INNODB STATUS                                                                                                                                 |
…
|       11 | 0.06623600 | ALTER TABLE orders ENGINE = MyISAM                                                                                                                        |
|       12 | 0.06030225 | ALTER TABLE orders ENGINE = InnoDB                                                                                                                        |
+----------+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------+
12 rows in set, 1 warning (0.00 sec)
```
 
 
## Задача 4

Изучите файл my.cnf в директории /etc/mysql.
Измените его согласно ТЗ (движок InnoDB):
Скорость IO важнее сохранности данных
Нужна компрессия таблиц для экономии места на диск
Размер буффера с незакомиченными транзакциями 1 Мб
Буффер кеширования 30% от ОЗУ
Размер файла логов операций 100 Мб
Приведите в ответе измененный файл my.cnf.
 
```
[mysqld]

skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql
 
pid-file=/var/run/mysqld/mysqld.pid
 
#1 используется для случаев,когда сохранность данных — это приоритет номер один.
#2 для случаев, когда небольшая потеря данных не критична.
#0 (ноль) — самый производительный, но небезопасный вариант.
innodb_flush_log_at_trx_commit = 0
 
#Barracuda — новейший формат файлов. Он поддерживает все форматы строк InnoDB, включая более новые форматы строк COMPRESSED и DYNAMIC. Функции, связанные с форматами строк COMPRESSED и DYNAMIC, включают сжатые таблицы, эффективное хранение внестраничных столбцов и префиксы ключей индекса размером до 3072 байт.
 
innodb_file_format = Barracuda
 
#На производительность журнала повторного выполнения (redo-log) оказывает влияние размер буфера этого журнала.
#Во время работы движок InnoDB помещает в эту область данные по незаконченным транзакциям, которые в результате непредвиденного сбоя в работе могут быть потеряны. Если произойдет сбой, то InnoDB попробует восстановить эти незаконченные транзакции взяв данные из этой области.
innodb_log_buffer_size = 1M
 
 
#Размер буферного пула можно настроить InnoDBв автономном режиме или во время работы сервера. Поведение, описанное в этом разделе, применимо к обоим методам.
#При увеличении или уменьшении innodb_buffer_pool_sizeоперация выполняется порциями. Размер фрагмента определяется параметром innodb_buffer_pool_chunk_sizeконфигурации, который по умолчанию имеет значение 128M.
innodb_buffer_pool_size = 2G
innodb_buffer_pool_chunk_size = 2G
 
#Эта опция влияет на скорость записи. Она устанавливает размер лога операций (так операции сначала записываются в лог, а потом применяются к данным на диске).#Чем больше этот лог, тем быстрее будут работать записи (т.к. их поместится больше в файл лога). Файлов всегда два, а их размер одинаковый. Значением параметра задается размер одного файла:
innodb_log_file_size = 100M
 
 
[client]
socket=/var/run/mysqld/mysqld.sock
 
!includedir /etc/mysql/conf.d/
```
 
---
**NOTE**

**Что нужно настроить в mySQL сразу после установки**

https://highload.today/index-php-2009-04-23-optimalnaya-nastroyka-mysql-servera/

https://habr.com/ru/post/66684/

https://habr.com/ru/post/539792/


**Калькулятор MySQL my.cnf**

https://sonikelf.ru/kalkulyator-dlya-nastrojki-mysql-servera-tochnee-my-cnf-i-nemnogo-rekomendacij/


**Сжатие таблиц**

https://ealebed.github.io/posts/2015/%D1%82%D1%8E%D0%BD%D0%B8%D0%BD%D0%B3-mysql-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0-%D1%81%D0%B6%D0%B0%D1%82%D0%B8%D0%B5-%D1%82%D0%B0%D0%B1%D0%BB%D0%B8%D1%86-innodb/


**Настройка redo-лога** 

https://blog.programs74.ru/how-to-change-innodb_log_file_size-safely/

---