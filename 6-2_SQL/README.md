### Д.з. к занятию 2. «SQL»
___

Задача 1
---
Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.  
Приведите получившуюся команду или docker-compose-манифест.

Решение
---
```
r@ub1:~$ docker run -d --name postgres12 \
> -e POSTGRES_PASSWORD=a1 \
> -p 5432:5432 \
> -v $HOME/docker/volumes/postgres/data:/home/r/netology/6-1_DB/postgresql/data \
> -v $HOME/docker/volumes/postgres/bckp:/home/r/netology/6-1_DB/postgresql/backup \
> postgres:12
f9db3f23c03246bf43cdb480d823102f5bd49d7bd43871c61dcd36017910b547

r@ub1:~$ docker ps
CONTAINER ID   IMAGE         COMMAND                  CREATED          STATUS          PORTS                                       NAMES
f9db3f23c032   postgres:12   "docker-entrypoint.s…"   14 seconds ago   Up 12 seconds   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   postgres12
```
___

Задача 2
---
В БД из задачи 1:  

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.  

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

Решение
---
- Итоговый список БД.

```
postgres=# \l+
                                                                    List of databases
   Name    |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   |  Size   | Tablespace |                Description
-----------+----------+----------+-------------+-------------+-----------------------+---------+------------+--------------------------------------------
 postgres  | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |                       | 8057 kB | pg_default | default administrative connection database
 template0 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +| 7809 kB | pg_default | unmodifiable empty database
           |          |          |             |             | postgres=CTc/postgres |         |            |
 template1 | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +| 7953 kB | pg_default | default template for new databases
           |          |          |             |             | postgres=CTc/postgres |         |            |
 test_db   | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 |                       | 7953 kB | pg_default |
(4 rows)
```
- Описание таблиц.
```
postgres=# \d+ orders
                                                           Table "public.orders"
    Column    |          Type          | Collation | Nullable |              Default               | Storage  | Stats target | Description
--------------+------------------------+-----------+----------+------------------------------------+----------+--------------+-------------
 id           | integer                |           | not null | nextval('orders_id_seq'::regclass) | plain    |              |
 Наименование | character varying(200) |           |          |                                    | extended |              |
 Цена         | integer                |           |          |                                    | plain    |              |
Indexes:
    "orders_pkey" PRIMARY KEY, btree (id)
Access method: heap

postgres=# \d+ clients
                                                             Table "public.clients"
      Column       |          Type          | Collation | Nullable |               Default               | Storage  | Stats target | Description
-------------------+------------------------+-----------+----------+-------------------------------------+----------+--------------+-------------
 id                | integer                |           | not null | nextval('clients_id_seq'::regclass) | plain    |              |
 Наименование      | character varying(200) |           |          |                                     | extended |              |
 Страна проживания | character varying(200) |           |          |                                     | extended |              |
 Заказ             | integer                |           |          |                                     | plain    |              |
Indexes:
    "clients_pkey" PRIMARY KEY, btree (id)
    "index_strana" btree ("Страна проживания")
Access method: heap
```
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db.
```
postgres=# SELECT
grantee, privilege_type, table_name
FROM
information_schema.table_privileges
WHERE
grantee in ('test-admin-user','test-simple-user')
and table_name in ('orders','clients')
order by
1,2,3;
```
- Список пользователей с правами к таблицам базы test_db.
```
     grantee      | privilege_type | table_name
------------------+----------------+------------
 test-admin-user  | DELETE         | clients
 test-admin-user  | DELETE         | orders
 test-admin-user  | INSERT         | clients
 test-admin-user  | INSERT         | orders
 test-admin-user  | REFERENCES     | clients
 test-admin-user  | REFERENCES     | orders
 test-admin-user  | SELECT         | clients
 test-admin-user  | SELECT         | orders
 test-admin-user  | TRIGGER        | clients
 test-admin-user  | TRIGGER        | orders
 test-admin-user  | TRUNCATE       | clients
 test-admin-user  | TRUNCATE       | orders
 test-admin-user  | UPDATE         | clients
 test-admin-user  | UPDATE         | orders
 test-simple-user | DELETE         | clients
 test-simple-user | DELETE         | orders
 test-simple-user | INSERT         | clients
 test-simple-user | INSERT         | orders
 test-simple-user | SELECT         | clients
 test-simple-user | SELECT         | orders
 test-simple-user | UPDATE         | clients
 test-simple-user | UPDATE         | orders
(22 rows)
```
___
Задача 3
---
Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:  

Таблица orders

|Наименование | Цена |
|-------------|------|
|Шоколад	    | 10   |
|Принтер	    | 3000 |
|Книга	      | 500  |
|Монитор	    | 7000 |
|Гитара	      | 4000 |

Таблица clients

|ФИО                 | Страна проживания |
|--------------------|--------|
|Иванов Иван Иванович| USA    |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan  |
|Ронни Джеймс Дио    | Russia |
|Ritchie Blackmore   | Russia |

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:
- запросы;
- результаты их выполнения.

Решение
---
Для таблицы *orders*.
```
postgres=# INSERT INTO orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
INSERT 0 5
postgres=# SELECT * FROM orders;
 id | Наименование | Цена
----+--------------+------
  1 | Шоколад      |   10
  2 | Принтер      | 3000
  3 | Книга        |  500
  4 | Монитор      | 7000
  5 | Гитара       | 4000
(5 rows)

postgres=# SELECT count(1) FROM orders;
 count
-------
     5
(1 row)
```
Для таблицы *clients*.
```
postgres=# INSERT INTO clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
INSERT 0 5
postgres=# SELECT * FROM clients;
 id |     Наименование     | Страна проживания | Заказ
----+----------------------+-------------------+-------
  1 | Иванов Иван Иванович | USA               |
  2 | Петров Петр Петрович | Canada            |
  3 | Иоганн Себастьян Бах | Japan             |
  4 | Ронни Джеймс Дио     | Russia            |
  5 | Ritchie Blackmore    | Russia            |
(5 rows)

postgres=# SELECT count(1) FROM clients;
 count
-------
     5
(1 row)

```
___
Задача 4
---
Часть пользователей из таблицы clients решили оформить заказы из таблицы orders. 
Используя foreign keys, свяжите записи из таблиц, согласно таблице:
|ФИО                  | Заказ|
|---------------------|------|
|Иванов Иван Иванович | Книга|
|Петров Петр Петрович | Монитор|
|Иоганн Себастьян Бах | Гитара|

Приведите SQL-запросы для выполнения этих операций.
Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
Подсказка: используйте директиву `UPDATE`.

Решение
---

___
Задача 5
---
Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву `EXPLAIN`).

Приведите получившийся результат и объясните, что значат полученные значения.

Решение
---
___
Задача 6
---
Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).  
Остановите контейнер с PostgreSQL, но не удаляйте volumes.  
Поднимите новый пустой контейнер с PostgreSQL.  
Восстановите БД test_db в новом контейнере.  
Приведите список операций, который вы применяли для бэкапа данных и восстановления.  

Решение
---
