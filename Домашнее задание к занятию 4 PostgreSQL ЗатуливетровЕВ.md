# Домашнее задание к занятию 4. «PostgreSQL»

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

<sub> 
docker pull postgres:13

docker volume create vol_postges

docker image tag postgres:13 hw64

docker run --rm --name hw64 -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol_postges:/var/lib/postgresql/data postgres:13

docker exec -ti hw64 bash

</sub>


Подключитесь к БД PostgreSQL, используя `psql`.

<sub> 

root@9b7d62c50004:/# psql -U postgres

psql (13.11 (Debian 13.11-1.pgdg110+1))

Type "help" for help.

</sub>

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.


**Найдите и приведите** управляющие команды для:

- вывода списка БД,

<sub> 

postgres=# \l
  
![listDB](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/dz11postgresql.png)

</sub>

- подключения к БД,

<sub> 

postgres=# \c postgres

</sub>

- вывода списка таблиц,

<sub> 
  
postgres=# \dtS

![listDB2](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/dz12postgresql.png)

</sub>

- вывода описания содержимого таблиц,

<sub> 

postgres=# \dS+ pg_index
  

![listDB3](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/dz13postgresql.png)

</sub>

- выхода из psql.

<sub> 

postgres=# \q
  
root@9b7d62c50004:/#


</sub>

***


## Задача 2

Используя `psql`, создайте БД `test_database`.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-04-postgresql/test_data).


<sub>

root@9b7d62c50004:/# psql -U postgres
  
psql (13.11 (Debian 13.11-1.pgdg110+1))
  
Type "help" for help.

postgres=# CREATE DATABASE test_database;
  
CREATE DATABASE

</sub>
  
Восстановите бэкап БД в `test_database`.

<sub>
  
  root@9b7d62c50004:/# psql -U postgres -f /tmp/test_dump.sql  test_database
  
SET
  
SET
  
SET
  
SET
  
SET
  
 set_config
  
*------------*
 
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
  
ALTER TABLE
  
COPY 8
  
 setval
  
*--------*
  
  
8
  
(1 row)
  

ALTER TABLE


</sub>

Перейдите в управляющую консоль `psql` внутри контейнера.

<sub>

root@9b7d62c50004:/# psql -U postgres
  
psql (13.11 (Debian 13.11-1.pgdg110+1))
  
Type "help" for help.

  </sub>

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

<sub>
  
  postgres=# \c test_database
  
You are now connected to database "test_database" as user "postgres".

test_database=# \dt
  
List of relations
  
 Schema |  Name  | Type  |  Owner   
  
--------+--------+-------+----------
  
 public | orders | table | postgres
  
(1 row)

test_database=# ANALYZE VERBOSE public.orders;
  
INFO:  analyzing "public.orders"
  
INFO:  "orders": scanned 1 of 1 pages, containing 8 live rows and 0 dead rows; 8 rows in sample, 8 estimated total rows
ANALYZE
  
  

</sub>


Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

<sub>
  
test_database=# SELECT avg_width FROM pg_stats WHERE tablename='orders';
  
 avg_width
  
*-----------*
  
 4
  
 16
  
 4
  
(3 rows)



</sub>



***

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам как успешному выпускнику курсов DevOps в Нетологии предложили
провести разбиение таблицы на 2: шардировать на orders_1 - price>499 и orders_2 - price<=499.

Предложите SQL-транзакцию для проведения этой операции.


<sub>

test_database=# CREATE TABLE orders_more_499_price (CHECK (price > 499)) INHERITS (orders);
  
CREATE TABLE
  
test_database=# INSERT INTO orders_more_499_price SELECT * FROM orders WHERE price > 499;
  
INSERT 0 3
  
test_database=# CREATE TABLE orders_less_499_price (CHECK (price <= 499)) INHERITS (orders);
                                                                        
CREATE TABLE
                                                                        
test_database=# INSERT INTO orders_LESS_499_price SELECT * FROM orders WHERE price <= 499;
  
INSERT 0 5
  
test_database=# DELETE FROM ONLY orders;
  
DELETE 8
  
test_database=# \dt
  
List of relations
  
 Schema |     	Name      	| Type  |  Owner   
  
*--------+-----------------------+-------+----------*
  
 public | orders            	| table | postgres
  
 public | orders_less_499_price | table | postgres
  
 public | orders_more_499_price | table | postgres
  
(3 rows)
  
  
 ![listDB2](https://github.com/zatulik2606/Netology-devops/blob/screenshorts/dz31postgresql.png)
  
  
  </sub>


Можно ли было изначально исключить ручное разбиение при проектировании таблицы orders?

  <sub>
  
 Да. Такое возможно. Например так.
  
CREATE RULE orders_insert_to_more AS ON INSERT TO orders WHERE ( price > 499 ) DO INSTEAD INSERT INTO orders_more_499_price VALUES (NEW.*);
  
CREATE RULE orders_insert_to_less AS ON INSERT TO orders WHERE ( price <= 499 ) DO INSTEAD INSERT INTO orders_less_499_price VALUES (NEW.*);

</sub>
  
***  

## Задача 4

Используя утилиту `pg_dump`, создайте бекап БД `test_database`.
  
<sub>
  
root@9b7d62c50004:/# export PGPASSWORD=netology && pg_dump -h localhost -U postgres test_database > /tmp/test_database_backup.sql

root@9b7d62c50004:/tmp# ls -la | grep sql
  
-rw-r--r-- 1 root root 3767 Jun  7 06:49 test_database_backup.sql
  
-rw-r--r-- 1 root root 2082 Jun  7 06:29 test_dump.sql
 
</sub>


Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?
  
  
</sub>  

Как решение ,можно добавить UNIQUE 

title character varying(80) NOT NULL UNIQUE



<sub>

***


