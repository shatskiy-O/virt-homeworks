
<!--DELIMITER-->
# Домашнее задание к занятию 2. «SQL»

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

## Ответ 1

<!--DELIMITER-->docker run -d \
<!--DELIMITER--> --name postgres12 \
<!--DELIMITER--> -e POSTGRES_USER=tor \
<!--DELIMITER--> -e POSTGRES_PASSWORD=1234567 \
<!--DELIMITER--> -e POSTGRES_DB=tor_db \
<!--DELIMITER--> -v postgres_data:/var/lib/postgresql/data \
<!--DELIMITER--> -v postgres_backups:/backups \
<!--DELIMITER--> -p 5432:5432 \
<!--DELIMITER--> postgres:12
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->## Задача 2
<!--DELIMITER-->
<!--DELIMITER-->В БД из задачи 1: 
<!--DELIMITER-->
<!--DELIMITER-->- создайте пользователя test-admin-user и БД test_db;
<!--DELIMITER-->- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
<!--DELIMITER-->- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
<!--DELIMITER-->- создайте пользователя test-simple-user;
<!--DELIMITER-->- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.
<!--DELIMITER-->
<!--DELIMITER-->Таблица orders:
<!--DELIMITER-->
<!--DELIMITER-->- id (serial primary key);
<!--DELIMITER-->- наименование (string);
<!--DELIMITER-->- цена (integer).
<!--DELIMITER-->
<!--DELIMITER-->Таблица clients:
<!--DELIMITER-->
<!--DELIMITER-->- id (serial primary key);
<!--DELIMITER-->- фамилия (string);
<!--DELIMITER-->- страна проживания (string, index);
<!--DELIMITER-->- заказ (foreign key orders).
<!--DELIMITER-->
<!--DELIMITER-->Приведите:
<!--DELIMITER-->
<!--DELIMITER-->- итоговый список БД после выполнения пунктов выше;
<!--DELIMITER-->- описание таблиц (describe);
<!--DELIMITER-->- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
<!--DELIMITER-->- список пользователей с правами над таблицами test_db.
<!--DELIMITER-->
<!--DELIMITER-->## Ответ 2
<!--DELIMITER-->
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/1.png)
<!--DELIMITER-->
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/2.png)
<!--DELIMITER-->
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/3.png)
<!--DELIMITER-->
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/4.png)
<!--DELIMITER-->
<!--DELIMITER-->## Задача 3
<!--DELIMITER-->
<!--DELIMITER-->Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:
<!--DELIMITER-->
<!--DELIMITER-->Таблица orders
<!--DELIMITER-->
<!--DELIMITER-->|Наименование|цена|
<!--DELIMITER-->|------------|----|
<!--DELIMITER-->|Шоколад| 10 |
<!--DELIMITER-->|Принтер| 3000 |
<!--DELIMITER-->|Книга| 500 |
<!--DELIMITER-->|Монитор| 7000|
<!--DELIMITER-->|Гитара| 4000|
<!--DELIMITER-->
<!--DELIMITER-->Таблица clients
<!--DELIMITER-->
<!--DELIMITER-->|ФИО|Страна проживания|
<!--DELIMITER-->|------------|----|
<!--DELIMITER-->|Иванов Иван Иванович| USA |
<!--DELIMITER-->|Петров Петр Петрович| Canada |
<!--DELIMITER-->|Иоганн Себастьян Бах| Japan |
<!--DELIMITER-->|Ронни Джеймс Дио| Russia|
<!--DELIMITER-->|Ritchie Blackmore| Russia|
<!--DELIMITER-->
<!--DELIMITER-->Используя SQL-синтаксис:
<!--DELIMITER-->- вычислите количество записей для каждой таблицы.
<!--DELIMITER-->
<!--DELIMITER-->Приведите в ответе:
<!--DELIMITER-->
<!--DELIMITER-->    - запросы,
<!--DELIMITER-->    - результаты их выполнения.
<!--DELIMITER-->
<!--DELIMITER-->## Ответ 3
<!--DELIMITER-->```
<!--DELIMITER-->SELECT COUNT(*) FROM orders;
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->```
<!--DELIMITER-->SELECT COUNT(*) FROM clients;
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/5.png)
<!--DELIMITER-->
<!--DELIMITER-->## Задача 4
<!--DELIMITER-->
<!--DELIMITER-->Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.
<!--DELIMITER-->
<!--DELIMITER-->Используя foreign keys, свяжите записи из таблиц, согласно таблице:
<!--DELIMITER-->
<!--DELIMITER-->|ФИО|Заказ|
<!--DELIMITER-->|------------|----|
<!--DELIMITER-->|Иванов Иван Иванович| Книга |
<!--DELIMITER-->|Петров Петр Петрович| Монитор |
<!--DELIMITER-->|Иоганн Себастьян Бах| Гитара |
<!--DELIMITER-->
<!--DELIMITER-->Приведите SQL-запросы для выполнения этих операций.
<!--DELIMITER-->
<!--DELIMITER-->Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
<!--DELIMITER--> 
<!--DELIMITER-->Подсказка: используйте директиву `UPDATE`.
<!--DELIMITER-->
<!--DELIMITER-->## Ответ 4
<!--DELIMITER-->
<!--DELIMITER-->```
<!--DELIMITER-->SELECT id FROM orders WHERE наименование = 'Книга';
<!--DELIMITER-->SELECT id FROM orders WHERE наименование = 'Монитор';
<!--DELIMITER-->SELECT id FROM orders WHERE наименование = 'Гитара';
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->```
<!--DELIMITER-->UPDATE clients SET заказ = (SELECT id FROM orders WHERE наименование = 'Книга') WHERE фамилия = 'Иванов Иван Иванович';
<!--DELIMITER-->UPDATE clients SET заказ = (SELECT id FROM orders WHERE наименование = 'Монитор') WHERE фамилия = 'Петров Петр Петрович';
<!--DELIMITER-->UPDATE clients SET заказ = (SELECT id FROM orders WHERE наименование = 'Гитара') WHERE фамилия = 'Иоганн Себастьян Бах';
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->```
<!--DELIMITER-->SELECT c.фамилия, o.наименование
<!--DELIMITER-->FROM clients c
<!--DELIMITER-->JOIN orders o ON c.заказ = o.id
<!--DELIMITER-->WHERE c.заказ IS NOT NULL;
<!--DELIMITER-->```
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/6.png)
<!--DELIMITER-->
<!--DELIMITER-->## Задача 5
<!--DELIMITER-->
<!--DELIMITER-->Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
<!--DELIMITER-->(используя директиву EXPLAIN).
<!--DELIMITER-->
<!--DELIMITER-->Приведите получившийся результат и объясните, что значат полученные значения.
<!--DELIMITER-->
<!--DELIMITER-->## Ответ 5
<!--DELIMITER-->
<!--DELIMITER-->```
<!--DELIMITER-->EXPLAIN SELECT c.фамилия, o.наименование
<!--DELIMITER-->FROM clients c
<!--DELIMITER-->JOIN orders o ON c.заказ = o.id
<!--DELIMITER-->WHERE c.заказ IS NOT NULL;
<!--DELIMITER-->```
<!--DELIMITER-->Этот запрос вернет план выполнения, который будет содержать информацию о следующих аспектах:
<!--DELIMITER-->
<!--DELIMITER-->Тип операции и ее порядок: Например, Seq Scan (последовательное сканирование), Index Scan (сканирование индекса) и так далее.
<!--DELIMITER-->Стоимость начала и общая стоимость выполнения: Эти значения показывают оценку времени начала и завершения операции. Нижние значения обычно указывают на более эффективные запросы.
<!--DELIMITER-->Количество строк: Количество обрабатываемых или возвращаемых строк на каждом этапе.
<!--DELIMITER-->Ширина: Примерный средний размер строк в байтах.
<!--DELIMITER-->
<!--DELIMITER-->![alt text](https://github.com/shatskiy-O/virt-homeworks/blob/virt-11/06-db-02-sql/images/7.png)
<!--DELIMITER-->
<!--DELIMITER-->## Задача 6
<!--DELIMITER-->
<!--DELIMITER-->Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).
<!--DELIMITER-->
<!--DELIMITER-->Остановите контейнер с PostgreSQL, но не удаляйте volumes.
<!--DELIMITER-->
<!--DELIMITER-->Поднимите новый пустой контейнер с PostgreSQL.
<!--DELIMITER-->
<!--DELIMITER-->Восстановите БД test_db в новом контейнере.
<!--DELIMITER-->
<!--DELIMITER-->Приведите список операций, который вы применяли для бэкапа данных и восстановления. 
<!--DELIMITER-->
<!--DELIMITER-->## Ответ 6
<!--DELIMITER-->
<!--DELIMITER-->1) Создание Бэкапа внутри контейнера
<!--DELIMITER-->``` 
<!--DELIMITER-->docker exec -t postgres12 pg_dump -U tor -d test_db -f /backups/test_db_backup.sql
<!--DELIMITER-->```
<!--DELIMITER-->2) Копирование файла бэкапа на хост
<!--DELIMITER-->```
<!--DELIMITER-->docker cp postgres12:/backups/test_db_backup.sql ~/test_db_backup.sql
<!--DELIMITER-->```
<!--DELIMITER-->3) Остановка 1 контейнера
<!--DELIMITER-->  
<!--DELIMITER-->4) Запуск 2 контейнера 
<!--DELIMITER-->```
<!--DELIMITER-->docker run -d \
<!--DELIMITER--> --name new_postgres12 \
<!--DELIMITER--> -e POSTGRES_USER=tor \
<!--DELIMITER--> -e POSTGRES_PASSWORD=1234567 \
<!--DELIMITER--> -v postgres_data:/var/lib/postgresql/data \
<!--DELIMITER--> -v postgres_backups:/backups \
<!--DELIMITER--> -p 5433:5432 \
<!--DELIMITER--> postgres:12
<!--DELIMITER-->```
<!--DELIMITER-->5) Копирование Файла Бэкапа в Новый Контейнер
<!--DELIMITER--> ```
<!--DELIMITER--> docker cp ~/test_db_backup.sql new_postgres12:/test_db_backup.sql
<!--DELIMITER--> ``` 
<!--DELIMITER--> 1) Создание и восстановление базы данных из бэкапа
<!--DELIMITER--> ``` 
<!--DELIMITER-->docker exec -t new_postgres12 psql -U tor -d test_db -f /test_db_backup.sql
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->```
<!--DELIMITER-->docker exec -t new_postgres12 psql -U tor -c "CREATE DATABASE test_db"
<!--DELIMITER-->```
<!--DELIMITER-->---
<!--DELIMITER-->
<!--DELIMITER-->### Как сдавать задание
<!--DELIMITER-->
<!--DELIMITER-->Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
<!--DELIMITER-->
<!--DELIMITER-->---
<!--DELIMITER-->```
<!--DELIMITER-->
<!--DELIMITER-->Please note that the provided code is in Markdown format. You can copy and paste it into your .md-file.