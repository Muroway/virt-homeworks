# Домашнее задание к занятию "6.2. SQL"

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

```
vagrant@vagrant:~$ docker volume create --name db
db
vagrant@vagrant:~$ docker volume create --name bkp
bkp
vagrant@vagrant:~$ docker volume list
DRIVER    VOLUME NAME
local     bkp
local     db
vagrant@vagrant:~$ sudo systemctl start docker
vagrant@vagrant:~$ sudo systemctl enable docker
vagrant@vagrant:~$ mkdir -p $HOME/docker/volumes/postgres

vagrant@vagrant:~$ docker run --rm --name postgresdock -e POSTGRES_PASSWORD=admin -e POSTGRES_USER=admin -e POSTGRES_DB=postgrdb -d -p 5432:5432 -v db:/var/lib/postgresql/data -v bkp:/var/lib/postgresql postgres:12
da698d41fd687579088b3f668a7caba2e0c084fa67fbd2ec774643fc3796c137

vagrant@vagrant:~$ sudo apt install postgresql-client
...
vagrant@vagrant:~$ psql -h 127.0.0.1 -U admin -d postgrdb
Password for user admin:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 12.13 (Debian 12.13-1.pgdg110+1))
postgrdb=#
```

## Задача 2

В БД из задачи 1: 
- создайте пользователя test-admin-user и БД test_db
```
postgrdb=# create database test_db;
CREATE DATABASE

postgrdb=# CREATE ROLE "test-admin-user" SUPERUSER LOGIN;
CREATE ROLE

postgrdb=# \c test_db
```
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
```
test_db=# CREATE TABLE orders (id SERIAL PRIMARY KEY, name text, price integer);
CREATE TABLE
```
```
test_db=# CREATE TABLE clients (id SERIAL PRIMARY KEY, lastname text, country text, booking integer, FOREIGN KEY (booking) REFERENCES orders (id));
CREATE TABLE
```
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
- создайте пользователя test-simple-user  

```
test_db=# CREATE USER "test-simple-user";
CREATE ROLE
```

- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

```
SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db
GRANT SELECT ON TABLE orders TO "test-simple-user";
GRANT
GRANT SELECT ON TABLE clients TO "test-simple-user";
GRANT
GRANT INSERT ON TABLE orders TO "test-simple-user";
GRANT
GRANT INSERT ON TABLE clients TO "test-simple-user";
GRANT
GRANT UPDATE ON TABLE orders TO "test-simple-user";
GRANT
GRANT UPDATE ON TABLE clients TO "test-simple-user";GRANT UPDATE ON TABLE clients TO "test-simple-user";
GRANT
GRANT DELETE ON TABLE orders TO "test-simple-user";
GRANT
GRANT DELETE ON TABLE clients TO "test-simple-user";GRANT DELETE ON TABLE clients TO "test-simple-user";
GRANT
```

Таблица orders:
- id (serial primary key)
- наименование (string)
- цена (integer)


Таблица clients:
- id (serial primary key)
- фамилия (string)
- страна проживания (string, index)
- заказ (foreign key orders)

Приведите:
- итоговый список БД после выполнения пунктов выше,

```
test_db=# \l
                             List of databases
   Name    | Owner | Encoding |  Collate   |   Ctype    | Access privileges
-----------+-------+----------+------------+------------+-------------------
 postgrdb  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 postgres  | admin | UTF8     | en_US.utf8 | en_US.utf8 |
 template0 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 template1 | admin | UTF8     | en_US.utf8 | en_US.utf8 | =c/admin         +
           |       |          |            |            | admin=CTc/admin
 test_db   | admin | UTF8     | en_US.utf8 | en_US.utf8 |
(5 rows)
```

- описание таблиц (describe)
```
test_db=# \dt
        List of relations
 Schema |  Name   | Type  | Owner
--------+---------+-------+-------
 public | clients | table | admin
 public | orders  | table | admin
(2 rows)
```
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
```
test_db=# SELECT * FROM information_schema.table_privileges where grantee in ('test-admin-user','test-simple-user');
 grantor |     grantee      | table_catalog | table_schema | table_name | privilege_type | is_grantable | with_hierarchy
---------+------------------+---------------+--------------+------------+----------------+--------------+----------------
 admin   | test-simple-user | test_db       | public       | orders     | INSERT         | NO           | NO
 admin   | test-simple-user | test_db       | public       | orders     | SELECT         | NO           | YES
 admin   | test-simple-user | test_db       | public       | orders     | UPDATE         | NO           | NO
 admin   | test-simple-user | test_db       | public       | orders     | DELETE         | NO           | NO
 admin   | test-simple-user | test_db       | public       | clients    | INSERT         | NO           | NO
 admin   | test-simple-user | test_db       | public       | clients    | SELECT         | NO           | YES
 admin   | test-simple-user | test_db       | public       | clients    | UPDATE         | NO           | NO
 admin   | test-simple-user | test_db       | public       | clients    | DELETE         | NO           | NO
(8 rows)
```
- список пользователей с правами над таблицами test_db
```
test_db=# \du
                                       List of roles
    Role name     |                         Attributes                         | Member of
------------------+------------------------------------------------------------+-----------
 admin            | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
 test-admin-user  | Superuser                                                  | {}
 test-simple-user |                                                            | {}
```

## Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|
```
test_db=# INSERT INTO orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3,'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
```
Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|
```
test_db=# INSERT INTO clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3,'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
```



Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
```
test_db=# SELECT count (*) FROM orders;
 count
-------
     5
(1 row)
```
```
test_db=# SELECT count (*) FROM clients;
 count
-------
     5
(1 row)
```
- приведите в ответе:
    - запросы 
    - результаты их выполнения.
```
test_db=# SELECT * FROM orders;
 id |  name   | price
----+---------+-------
  1 | Шоколад |    10
  2 | Принтер |  3000
  3 | Книга   |   500
  4 | Монитор |  7000
  5 | Гитара  |  4000
(5 rows)
```
```
test_db=# SELECT * FROM clients;
 id |       lastname       | country | booking
----+----------------------+---------+---------
  1 | Иванов Иван Иванович | USA     |
  2 | Петров Петр Петрович | Canada  |
  3 | Иоганн Себастьян Бах | Japan   |
  4 | Ронни Джеймс Дио     | Russia  |
  5 | Ritchie Blackmore    | Russia  |
(5 rows)
```




## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.
```
test_db=# UPDATE clients set booking = 3 where id = 1;
UPDATE 1
test_db=# UPDATE clients set booking = 4 where id = 2;
UPDATE 1
test_db=# UPDATE clients set booking = 5 where id = 3;
UPDATE 1

test_db=# SELECT * FROM clients;
  5 | Ritchie Blackmore    | Russia  |
  4 | Ронни Джеймс Дио     | Russia  |
  1 | Иванов Иван Иванович | USA     |       3
  2 | Петров Петр Петрович | Canada  |       4
  3 | Иоганн Себастьян Бах | Japan   |       5
```

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
```
test_db=# SELECT c.id, c.lastname, o.id, o.name FROM clients as c INNER JOIN orders as o ON o.id = c.booking;
  1 | Иванов Иван Иванович |  3 | Книга
  2 | Петров Петр Петрович |  4 | Монитор
  3 | Иоганн Себастьян Бах |  5 | Гитара
```

Подсказка - используйте директиву `UPDATE`.

---
**NOTE**

Пример очистки ячеек:
```
test_db=# UPDATE clients set booking = null where id = 2;
```
---

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).
Приведите получившийся результат и объясните что значат полученные значения.
```
test_db=# EXPLAIN SELECT c.id, c.lastname, o.id, o.name FROM clients as c INNER JOIN orders as o ON o.id = c.booking;
 Hash Join  (cost=37.00..57.24 rows=810 width=72)
   Hash Cond: (c.booking = o.id)
   ->  Seq Scan on clients c  (cost=0.00..18.10 rows=810 width=40)
   ->  Hash  (cost=22.00..22.00 rows=1200 width=36)
         ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=36)
```
```
test_db=# EXPLAIN ANALYZE SELECT c.id, c.lastname, o.id, o.name FROM clients as c INNER JOIN orders as o ON o.id = c.booking;
 Hash Join  (cost=37.00..57.24 rows=810 width=72) (actual time=0.028..0.031 rows=3 loops=1)
   Hash Cond: (c.booking = o.id)
   ->  Seq Scan on clients c  (cost=0.00..18.10 rows=810 width=40) (actual time=0.010..0.010 rows=5 loops=1)
   ->  Hash  (cost=22.00..22.00 rows=1200 width=36) (actual time=0.010..0.010 rows=5 loops=1)
         Buckets: 2048  Batches: 1  Memory Usage: 17kB
         ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=36) (actual time=0.005..0.006 rows=5 loops=1)
 Planning Time: 0.117 ms
 Execution Time: 0.056 ms
```
Указаны затраты на выполнения запроса, использование памяти и время выполненения.

Output: c.id, c.lastname, o.id, o.name - получение выходных данных из таблиц.

--- 
**NOTE**

Hash join - для большой выборки оптимизатор предпочитает соединение хешированием:
Узел Hash Join начинает работу с того, что обращается к дочернему узлу Hash. Тот получает от своего дочернего узла (здесь - Seq Scan) весь набор строк и строит хеш-таблицу.
Затем Hash Join обращается ко второму дочернему узлу и соединяет строки, постепенно возвращая полученные результаты.

---



## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

```
docker exec -t postgresdock pg_dump -U admin test_db -f /var/lib/postgresql/data/test_db_bkp.sql
vagrant@vagrant:~$ docker exec -it postgresdock bash
root@1a67285caa91:/# cd /var/lib/postgresql/data/
root@1a67285caa91:/var/lib/postgresql/data# cp test_db_bkp.sql /var/lib/postgresql/
root@1a67285caa91:/var/lib/postgresql/data# ls /var/lib/postgresql/
data  test_db_bkp.sql
```

Остановите контейнер с PostgreSQL (но не удаляйте volumes).
```
vagrant@vagrant:~$ docker stop postgresdock
postgresdock
```

Поднимите новый пустой контейнер с PostgreSQL.
```
docker run --rm --name postgresdocknew -e POSTGRES_PASSWORD=admin -e POSTGRES_USER=admin -e POSTGRES_DB=postgrdb -d -p 5432:5432 -v bkp:/var/lib/postgresql postgres:12
```

```
vagrant@vagrant:~$ psql -h 127.0.0.1 -U admin -d postgrdb
Password for user admin:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 12.13 (Debian 12.13-1.pgdg110+1))
Type "help" for help.

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

postgrdb=#
```


Восстановите БД test_db в новом контейнере.
```
vagrant@vagrant:~$ psql -h 127.0.0.1 -U admin -d postgrdb
Password for user admin:
psql (12.12 (Ubuntu 12.12-0ubuntu0.20.04.1), server 12.13 (Debian 12.13-1.pgdg110+1))
Type "help" for help.

postgrdb=# create database test_db;
CREATE DATABASE
postgrdb=# create user "test-simple-user";
CREATE ROLE

vagrant@vagrant:~$ docker exec -it postgresdocknew psql -U admin -d test_db -f /var/lib/postgresql/test_db_bkp.sql
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
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
CREATE TABLE
ALTER TABLE
CREATE SEQUENCE
ALTER TABLE
ALTER SEQUENCE
ALTER TABLE
ALTER TABLE
COPY 5
COPY 5
 setval
--------
      1
(1 row)

 setval
--------
      1
(1 row)

ALTER TABLE
ALTER TABLE
ALTER TABLE
GRANT
GRANT
```
```
test_db=# SELECT c.id, c.lastname, o.id, o.name FROM clients as c INNER JOIN orders as o ON o.id = c.booking;
 id |       lastname       | id |  name
----+----------------------+----+---------
  1 | Иванов Иван Иванович |  3 | Книга
  2 | Петров Петр Петрович |  4 | Монитор
  3 | Иоганн Себастьян Бах |  5 | Гитара
(3 rows)
```


Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
