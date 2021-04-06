Работа с PostgreSQL – создание пользователей, раздача прав, создание базы данных, создание таблиц
=================================================================================================

*postgres-# - работа под текущим пользователем* 

**Смена кодировки**

  `chcp 1251` или
  `psql \! chcp 1251`


**Создать базу данных**

`create database testdb (название бд);`


**Подключиться к базе данных**

`psql -h <hostname or ip address> -p <port number of remote machine> -d <database name which you want to connect> -U <username of the database server>`
  
*Например, пользователь postgres, база данных testdb:*

`psql -d testdb -U postgres`

*Например, пользователь postgres:*

`psql -U postgres`


**Выбрать базу данных testdb**

`\c testdb`


**Создать пользователя (CRUD)**

`create user usercrud (имя пользователя) with encrypted password 'пароль';`


*Дать пользователю CRUD-права на базу данных testdb*

`GRANT ALL PRIVILEGES ON DATABASE testdb (имя базы дданых) TO usercrud (имя пользователя);`

**Создать пользователя c ограниченными правами**

*Создать роль (READ)*

`CREATE ROLE readonly;`


*Дать права к существующим таблицам*

`GRANT USAGE ON SCHEMA public TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;`


*дать права к новым таблицам*

`ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO readonly;`


*Создать пользователя и дать ему роль*

`CREATE USER имя пользователя WITH PASSWORD 'пароль';
GRANT readonly TO имя пользователя;`


*Разрешить пользователю подключаться к бд*

`GRANT CONNECT ON DATABASE testdb TO userread;`


**Проверить какой пользователь подключен**

`\conninfo`


**СОЗДАНИЕ ТАБЛИЦ**

*BIGSERIAL* - id primay key нарастался автоматически
каждой таблице установить primay key!!!


**Создать таблицу под testdb (имя таблицы, в которой созданы пользователи с правами)**

`CREATE TABLE (имя таблицы) (
> id BIGSERIAL NOT NULL PRIMARY KEY,
> maker VARCHAR(50) NOT NULL,
> model VARCHAR(50) NOT NULL,
> type VARCHAR(50) NOT NULL,
> price NUMBERIC(5, 5)
> );`


**\d** - проверить таблицу и последовательность (id)

**\d product (имя таблицы)** - посмотреть таблицу изнутри


**Удалить столбец**

`ALTER TABLE pc (имя таблицы) DROP COLUMN screen (имя столбца);`


**Изменить тип столбца**

`ALTER TABLE pc ALTER COLUMN price TYPE numeric(12,2);`


**Обозначить внешний ключ в таблице (кроме главной) - остальные таблицы имеют отношение к главной**

`ALTER TABLE pc (имя второй таблицы) ADD product_id (имя внешнего ключа) BIGINT (тип) 
REFERENCES (ссылается на) product (имя первой таблицы) (id) (поле таблицы, к которму ссылается);`


**Заполнить таблицу данными**

`INSERT INTO product ('имя таблицы') (maker, model, type)('строки в таблице') VALUES ('данные', 'данные', 'данные');`
так заполнится одна строка 


**Дать права пользователю crud для модификации таблиц и выборки данных**

`testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON product TO usercrud;

testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON pc TO usercrud;

testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON laptop TO usercrud;

testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON tablet TO usercrud;

testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON mobile TO usercrud;`


**Дать права пользователю read для выборки данных**

`testdb=# GRANT SELECT ON product TO userread;
GRANT
testdb=# GRANT SELECT ON pc TO userread;
GRANT
testdb=# GRANT SELECT ON laptop TO userread;
GRANT
testdb=# GRANT SELECT ON tablet TO userread;
GRANT
testdb=# GRANT SELECT ON mobile TO userread;`


**Дать права пользователю crud для счетчика таблиц**

testdb=# GRANT USAGE, SELECT ON SEQUENCE product_id_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE pc_id_pc_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE laptop_id_laptop_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE tablet_id_tablet_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE mobile_id_mobile_seq TO usercrud;
