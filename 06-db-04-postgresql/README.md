# Домашнее задание к занятию "6.4. PostgreSQL"

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

```
docker pull postgres:13
docker volume create postgres-vol
docker run --rm --name postgres-doc -e POSTGRES_PASSWORD=admin -e POSTGRES_USER=admin -e POSTGRES_DB=postgrdb -d -p 5432:5432 -v postgres-vol:/var/lib/postgres/data postgres:13
```

Подключитесь к БД PostgreSQL используя `psql`.

```
vagrant@vagrant:~$ psql -h 127.0.0.1 -U admin -d postgrdb
Password for user admin:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.9 (Debian 13.9-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
Type "help" for help.

postgrdb=#

```

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:

- вывода списка БД
```
postgrdb=# \l
                             List of databases
   Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
-----------+-------+----------+------------+------------+-------------------
 postgrdb  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 postgres  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 template1 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
(4 rows)
```

- подключения к БД
```
\c[onnect] {[DBNAME|- USER|- HOST|- PORT|-] | conninfo}
                         connect to new database (currently "postgres")

postgrdb=# \c
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.9 (Debian 13.9-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
You are now connected to database "postgrdb" as user "admin".
```

- вывода списка таблиц
```
\dt[S+] [PATTERN]      list tables

postgres=# \dt
Did not find any relations.
postgres=# \dtS

       List of relations
   Schema   |          Name           | Type  | Owner
------------+-------------------------+-------+-------
 pg_catalog | pg_aggregate            | table | admin
 pg_catalog | pg_am                   | table | admin
 pg_catalog | pg_amop                 | table | admin
 pg_catalog | pg_amproc               | table | admin
 pg_catalog | pg_attrdef              | table | admin
 pg_catalog | pg_attribute            | table | admin
 pg_catalog | pg_auth_members         | table | admin
 pg_catalog | pg_authid               | table | admin
 pg_catalog | pg_cast                 | table | admin
 pg_catalog | pg_class                | table | admin
 pg_catalog | pg_collation            | table | admin
 pg_catalog | pg_constraint           | table | admin
 pg_catalog | pg_conversion           | table | admin

```
- вывода описания содержимого таблиц
```
\d[S+]  NAME           describe table, view, sequence, or index
 
postgres=# \dtS+ pg_am
                    List of relations
   Schema   | Name  | Type  | Owner | Size  | Description
------------+-------+-------+-------+-------+-------------
 pg_catalog | pg_am | table | admin | 40 kB |
(1 row)
```

- выхода из psql
```
\q                     quit psql
postgres=# \q
```

## Задача 2

Используя `psql` создайте БД `test_database`.
```
postgrdb=# CREATE DATABASE test_database;
CREATE DATABASE
postgrdb=# \l
                               List of databases
     Name      | Owner | Encoding |  Collate   |   Ctype    | Access privileges
---------------+-------+----------+------------+------------+-------------------
 postgrdb      | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 postgres      | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 template0     | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
               |       |          |            |            | admin=CTc/admin
 template1     | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
               |       |          |            |            | admin=CTc/admin
 test_database | admin | UTF8     | en_US.utf8 | en_US.utf8 |
(5 rows)
```


Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

```
root@9e3a10334152:~# cd /var/lib/postgres/data/
root@9e3a10334152:/var/lib/postgres/data# psql -U admin test_database < test_dump.sql
SET
SET
SET
SET
SET
 set_config
------------

(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ERROR:  role "postgres" does not exist
CREATE SEQUENCE
ERROR:  role "postgres" does not exist
ALTER SEQUENCE
ALTER TABLE
COPY 8
 setval
--------
      8
(1 row)

ALTER TABLE
```
Перейдите в управляющую консоль `psql` внутри контейнера.

```
vagrant@vagrant:~$ psql -h 127.0.0.1 -U admin -d postgrdb
Password for user admin:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.9 (Debian 13.9-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
Type "help" for help.
```

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

```
postgrdb=# \c test_database
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 13.9 (Debian 13.9-1.pgdg110+1))
WARNING: psql major version 12, server major version 13.
         Some psql features might not work.
You are now connected to database "test_database" as user "admin".
test_database=# \dt
        List of relations
 Schema |  Name  | Type  | Owner
--------+--------+-------+-------
 public | orders | table | admin
(1 row)
```
```
test_database=# ANALYZE VERBOSE orders;
INFO:  analyzing "public.orders"
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
```



Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.
**Приведите в ответе** команду, которую вы использовали для вычисления и полученный результат.

```
test_database=# SELECT avg_width FROM pg_stats WHERE tablename='orders';
 avg_width
-----------
         4
        16
         4
(3 rows)
```

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили
провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

---
***NOTE***

**Секционированием данных** называется разбиение одной большой логической таблицы на несколько меньших физических секций. Секционирование может принести следующую пользу:

В определённых ситуациях оно кардинально увеличивает быстродействие, особенно когда большой процент часто запрашиваемых строк таблицы относится к одной или лишь нескольким секциям. Секционирование может сыграть роль ведущих столбцов в индексах, что позволит уменьшить размер индекса и увеличит вероятность нахождения наиболее востребованных частей индексов в памяти.

Когда в выборке или изменении данных задействована большая часть одной секции, последовательное сканирование этой секции может выполняться гораздо быстрее, чем случайный доступ по индексу к данным, разбросанным по всей таблице.

Массовую загрузку и удаление данных можно осуществлять, добавляя и удаляя секции, если это было предусмотрено при проектировании секционированных таблиц. Операция ALTER TABLE DETACH PARTITION или удаление отдельной секции с помощью команды DROP TABLE выполняются гораздо быстрее, чем массовая обработка. Эти команды также полностью исключают накладные расходы, связанные с выполнением VACUUM после DELETE.

Редко используемые данные можно перенести на более дешёвые и медленные носители.

Всё это обычно полезно только для очень больших таблиц. Какие именно таблицы выиграют от секционирования, зависит от конкретного приложения, хотя, как правило, это следует применять для таблиц, размер которых превышает объём ОЗУ сервера.

---

```
test_database=# SELECT * FROM orders;
 id |        title         | price
----+----------------------+-------
  1 | War and peace        |   100
  2 | My little database   |   500
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
  6 | WAL never lies       |   900
  7 | Me and my bash-pet   |   499
  8 | Dbiezdmin            |   501
(8 rows)
```

```
test_database=# SELECT DATA_TYPE FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name = 'orders_origin' AND COLUMN_NAME = 'id'; data_type
-----------
 integer
(1 row)

test_database=# SELECT DATA_TYPE FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name = 'orders_origin' AND COLUMN_NAME = 'title';
     data_type
-------------------
 character varying
(1 row)

test_database=# SELECT DATA_TYPE FROM INFORMATION_SCHEMA.COLUMNS WHERE table_name = 'orders_origin' AND COLUMN_NAME = 'price';
 data_type
-----------
 integer
(1 row)
```
Предложите SQL-транзакцию для проведения данной операции.
```
test_database=# ALTER TABLE orders RENAME TO orders_origin;
ALTER TABLE
test_database=# CREATE TABLE orders (id integer, title varchar(80), price integer) PARTITION BY RANGE (price);
CREATE TABLE
test_database=# CREATE TABLE orders_high499 PARTITION OF orders FOR VALUES FROM (499) to (99999);
CREATE TABLE
test_database=# INSERT INTO orders (id, title, price) SELECT * FROM orders_origin;
INSERT 0 8
```

```
test_database=# SELECT * FROM orders_low499;
 id |        title         | price
----+----------------------+-------
  1 | War and peace        |   100
  3 | Adventure psql time  |   300
  4 | Server gravity falls |   300
  5 | Log gossips          |   123
(4 rows)

test_database=# SELECT * FROM orders_high499;
 id |       title        | price
----+--------------------+-------
  2 | My little database |   500
  6 | WAL never lies     |   900
  7 | Me and my bash-pet |   499
  8 | Dbiezdmin          |   501
(4 rows)
```

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

Можно первоначально было сделать таблицу секционной.


## Задача 4

Используя утилиту `pg_dump` создайте бекап БД `test_database`.
```
root@9e3a10334152:/# pg_dump -U admin -d test_database > test_database_pgdump.sql
root@9e3a10334152:/# mv test_database_pgdump.sql /var/lib/postgres/data/
```

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?
Для уникальности можно добавить PRIMARY KEY, INDEX или CONSTRAINT.

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
