# Домашнее задание к занятию 4. «PostgreSQL»

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL, используя `psql`.

Воспользуйтесь командой `\?` для вывода подсказки по имеющимся в `psql` управляющим командам.

**Найдите и приведите** управляющие команды для:

- вывода списка БД,
- подключения к БД,
- вывода списка таблиц,
- вывода описания содержимого таблиц,
- выхода из psql.
  
## Ответ 1
 
- вывода списка БД - \l
- подключения к БД - \c [имя_базы_данных]
- вывода списка таблиц - \dt 
- вывода описания содержимого таблиц - \d [имя_таблицы]
- выхода из psql. - \q

## Задача 2

Используя `psql`, создайте БД `test_database`.

Изучите [бэкап БД](https://github.com/netology-code/virt-homeworks/tree/virt-11/06-db-04-postgresql/test_data).

Восстановите бэкап БД в `test_database`.

Перейдите в управляющую консоль `psql` внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу [pg_stats](https://postgrespro.ru/docs/postgresql/12/view-pg-stats), найдите столбец таблицы `orders` 
с наибольшим средним значением размера элементов в байтах.

**Приведите в ответе** команду, которую вы использовали для вычисления, и полученный результат.

## Ответ 2

```
psql -U postgres -d test_database -c "SELECT attname, avg_width FROM pg_stats WHERE tablename = 'orders' ORDER BY avg_width DESC LIMIT 1;"
```

## Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и
поиск по ней занимает долгое время. Вам как успешному выпускнику курсов DevOps в Нетологии предложили
провести разбиение таблицы на 2: шардировать на orders_1 - price>499 и orders_2 - price<=499.

Предложите SQL-транзакцию для проведения этой операции.

Можно ли было изначально исключить ручное разбиение при проектировании таблицы orders?

## Ответ 3

### 1) Создание новых шардированных таблиц:
```
CREATE TABLE orders_1 (LIKE orders INCLUDING ALL);
CREATE TABLE orders_2 (LIKE orders INCLUDING ALL);
```

### 2) Перенос данных в шардированные таблицы:
```
INSERT INTO orders_1 SELECT * FROM orders WHERE price > 499;
INSERT INTO orders_2 SELECT * FROM orders WHERE price <= 499;
```

### 3) Удаление или переименование исходной таблицы:
```
DROP TABLE orders; -- или ALTER TABLE orders RENAME TO orders_backup;
```

### 4) Удаление или переименование исходной таблицы:
```
DROP TABLE orders; -- или ALTER TABLE orders RENAME TO orders_backup;
```

### 5) Да, изначально можно было исключить ручное разбиение, используя механизм разбиения таблиц (table partitioning) в PostgreSQL


## Задача 4

Используя утилиту `pg_dump`, создайте бекап БД `test_database`.

Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца `title` для таблиц `test_database`?

## Ответ 4

```
pg_dump -U postgres test_database > /test_database_backup.sql
```
   а)Открыть файл бэкапа в текстовом редакторе.
   б)Найти определения таблиц, в которых нужно изменить столбец title, и добавьте ограничение UNIQUE к этому столбцу.
   в)Сохраните изменения.


---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

mysecretpassword