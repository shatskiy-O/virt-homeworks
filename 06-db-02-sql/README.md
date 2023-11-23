

# Домашнее задание к занятию 2. «SQL»

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

## Ответ 1

docker run -d \
 --name postgres12 \
 -e POSTGRES_USER=tor \
 -e POSTGRES_PASSWORD=1234567 \
 -e POSTGRES_DB=tor_db \
 -v postgres_data:/var/lib/postgresql/data \
 -v postgres_backups:/backups \
 -p 5432:5432 \
 postgres:12
```

## Задача 2

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

## Ответ 2

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/1.png)

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/2.png)

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/3.png)

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/4.png)

## Задача 3

Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:

    - запросы,
    - результаты их выполнения.

## Ответ 3
```
SELECT COUNT(*) FROM orders;
```

```
SELECT COUNT(*) FROM clients;
```

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/5.png)

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
 
Подсказка: используйте директиву `UPDATE`.

## Ответ 4

```
SELECT id FROM orders WHERE наименование = 'Книга';
SELECT id FROM orders WHERE наименование = 'Монитор';
SELECT id FROM orders WHERE наименование = 'Гитара';
```

```
UPDATE clients SET заказ = (SELECT id FROM orders WHERE наименование = 'Книга') WHERE фамилия = 'Иванов Иван Иванович';
UPDATE clients SET заказ = (SELECT id FROM orders WHERE наименование = 'Монитор') WHERE фамилия = 'Петров Петр Петрович';
UPDATE clients SET заказ = (SELECT id FROM orders WHERE наименование = 'Гитара') WHERE фамилия = 'Иоганн Себастьян Бах';
```

```
SELECT c.фамилия, o.наименование
FROM clients c
JOIN orders o ON c.заказ = o.id
WHERE c.заказ IS NOT NULL;
```
![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/6.png)

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

## Ответ 5

```
EXPLAIN SELECT c.фамилия, o.наименование
FROM clients c
JOIN orders o ON c.заказ = o.id
WHERE c.заказ IS NOT NULL;
```
Этот запрос вернет план выполнения, который будет содержать информацию о следующих аспектах:

Тип операции и ее порядок: Например, Seq Scan (последовательное сканирование), Index Scan (сканирование индекса) и так далее.
Стоимость начала и общая стоимость выполнения: Эти значения показывают оценку времени начала и завершения операции. Нижние значения обычно указывают на более эффективные запросы.
Количество строк: Количество обрабатываемых или возвращаемых строк на каждом этапе.
Ширина: Примерный средний размер строк в байтах.

![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/7.png)

## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).

Остановите контейнер с PostgreSQL, но не удаляйте volumes.

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 

## Ответ 6

1) Создание Бэкапа внутри контейнера
``` 
docker exec -t postgres12 pg_dump -U tor -d test_db -f /backups/test_db_backup.sql
```
2) Копирование файла бэкапа на хост
```
docker cp postgres12:/backups/test_db_backup.sql ~/test_db_backup.sql
```
3) Остановка 1 контейнера
  
4) Запуск 2 контейнера 
```
docker run -d \
 --name new_postgres12 \
 -e POSTGRES_USER=tor \
 -e POSTGRES_PASSWORD=1234567 \
 -v postgres_data:/var/lib/postgresql/data \
 -v postgres_backups:/backups \
 -p 5433:5432 \
 postgres:12
```
5) Копирование Файла Бэкапа в Новый Контейнер
 ```
 docker cp ~/test_db_backup.sql new_postgres12:/test_db_backup.sql
 ``` 
 1) Создание и восстановление базы данных из бэкапа
 ``` 
docker exec -t new_postgres12 psql -U tor -d test_db -f /test_db_backup.sql
```

```
docker exec -t new_postgres12 psql -U tor -c "CREATE DATABASE test_db"
```
---

### Как сдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---
```

Please note that the provided code is in Markdown format. You can copy and paste it into your .md-file.