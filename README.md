# Домашнее задание к занятию "`Работа с данными (DDL/DML)`" - `Гилельс Кирилл`
Задание можно выполнить как в любом IDE, так и в командной строке.

### Задание 1
1.1. Поднимите чистый инстанс MySQL версии 8.0+. Можно использовать локальный сервер или контейнер Docker.

1.2. Создайте учётную запись sys_temp. 

1.3. Выполните запрос на получение списка пользователей в базе данных. (скриншот)

1.4. Дайте все права для пользователя sys_temp. 

1.5. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)

1.6. Переподключитесь к базе данных от имени sys_temp.

Для смены типа аутентификации с sha2 используйте запрос: 
```sql
ALTER USER 'sys_test'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```
1.6. По ссылке https://downloads.mysql.com/docs/sakila-db.zip скачайте дамп базы данных.

1.7. Восстановите дамп в базу данных.

1.8. При работе в IDE сформируйте ER-диаграмму получившейся базы данных. При работе в командной строке используйте команду для получения всех таблиц базы данных. (скриншот)

*Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.*


### Решение


Работа проводилась на Ubuntu server 24.04LTS.
Установил mysql-server

![1.1](/1.1.png)

Дополнительно отключил проверку паролей из под рут:
```sql
SHOW VARIABLES LIKE 'validate_password%';
UNINSTALL COMPONENT 'file://component_validate_password';
```
Создал пользователя sys_temp

![1.3](/1.3.png)

Дал все привелегии пользователю sys_temp
Запрос на получение списка прав:

![1.5](/1.5.png)

Скачал и распаковал данные,востановил БД, вывел список таблиц:
```bash
wget https://downloads.mysql.com/docs/sakila-db.zip
unzip sakila-db.zip
# Авторизация и создание БД mysql -u sys_temp -p > CREATE DATABASE sakila;
mysql -u sys_temp -p sakila < sakila-db/sakila-schema.sql
mysql -u sys_temp -p sakila < sakila-db/sakila-data.sql
mysql -u sys_temp -p -e "USE sakila; SHOW TABLES;"
```

![1.8](/1.8.png)

Стартовые запросы:
```sql 
#root
CREATE USER 'sys_temp'@'localhost' IDENTIFIED BY '1';
FLUSH PRIVILEGES;
SELECT User, Host FROM mysql.user;
GRANT ALL PRIVILEGES ON *.* TO 'sys_temp'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
SHOW GRANTS FOR 'sys_temp'@'localhost';
EXIT;
```
```sql 
#systemp
CREATE DATABASE sakila;
EXIT;
```

### Задание 2
Составьте таблицу, используя любой текстовый редактор или Excel, в которой должно быть два столбца: в первом должны быть названия таблиц восстановленной базы, во втором названия первичных ключей этих таблиц. Пример: (скриншот/текст)
```
Название таблицы | Название первичного ключа
customer         | customer_id
```

### Решение 
Подключился под sys_temp
```bash
mysql -u sys_temp -p
```
```sql
USE sakila;
SELECT t.table_name AS 'Название таблицы', kcu.column_name AS 'Название первичного ключа'
FROM information_schema.tables t
JOIN information_schema.key_column_usage kcu ON t.table_name = kcu.table_name
WHERE t.table_schema = 'sakila' AND kcu.constraint_name = 'PRIMARY'
ORDER BY t.table_name;
```
Вывод таблицы:

![2](/2.png)


### Задание 3*
3.1. Уберите у пользователя sys_temp права на внесение, изменение и удаление данных из базы sakila.

3.2. Выполните запрос на получение списка прав для пользователя sys_temp. (скриншот)

*Результатом работы должны быть скриншоты обозначенных заданий, а также простыня со всеми запросами.*
