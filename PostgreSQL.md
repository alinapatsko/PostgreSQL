Работа с PostgreSQL – создание пользователей, раздача прав, создание базы данных, создание таблиц
=================================================================================================

*postgres-# - работа под текущим пользователем* 

**Смена кодировки**
```
  chcp 1251 или
  psql \! chcp 1251
```
1. **СОЗДАТЬ БАЗУ ДАННЫХ**
```
create database testdb (название бд);
```
2. **Подключиться к базе данных**
```
psql -h <hostname or ip address> -p <port number of remote machine> -d <database name which you want to connect> -U <username of the database server>
```
*Например, пользователь postgres, база данных testdb:*
```
psql -d testdb -U postgres
```
*Например, пользователь postgres:*
```
psql -U postgres
```
**Выбрать базу данных testdb**
```
\c testdb
```
3. **СОЗДАТЬ ПОЛЬЗОВАТЕЛЯ (CRUD)**
```
create user usercrud (имя пользователя) with encrypted password 'пароль';`
```
1) *Дать пользователю CRUD-права на базу данных testdb*
```
GRANT ALL PRIVILEGES ON DATABASE testdb (имя базы дданых) TO usercrud (имя пользователя);`
```
4. **СОЗДАТЬ ПОЛЬЗОВАТЕЛЯ С ОГРАНИЧЕННЫМИ ПРАВАМИ**

*1) Создать роль (READ)*
```
CREATE ROLE readonly;
```
2) *Дать права к существующим таблицам*
```
GRANT USAGE ON SCHEMA public TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;
```
3) *Дать права к новым таблицам*
```
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO readonly;
```
4) *Создать пользователя и дать ему роль*
```
CREATE USER имя пользователя WITH PASSWORD 'пароль';
GRANT readonly TO имя пользователя;
```
5) *Разрешить пользователю подключаться к баде данных*
```
GRANT CONNECT ON DATABASE testdb TO userread;
```
**Проверить какой пользователь подключен**
```
\conninfo
```
5. **СОЗДАНИЕ ТАБЛИЦ**

*BIGSERIAL* - id primay key нарастался автоматически
каждой таблице установить primay key!!!

1) **Создать таблицу под testdb (имя таблицы, в которой созданы пользователи с правами)**
```
CREATE TABLE (имя таблицы) (
id BIGSERIAL NOT NULL PRIMARY KEY,
maker VARCHAR(50) NOT NULL,
model VARCHAR(50) NOT NULL,
type VARCHAR(50) NOT NULL,
price NUMBERIC(5, 5)
);
```
**\d** - проверить таблицу и последовательность (id)

**\d product (имя таблицы)** - посмотреть таблицу изнутри

- **Удалить столбец**
```
ALTER TABLE pc (имя таблицы) DROP COLUMN screen (имя столбца);
```
- **Изменить тип столбца**
```
ALTER TABLE pc ALTER COLUMN price TYPE numeric(12,2);
```
2) **Обозначить внешний ключ в таблице (кроме главной) - остальные таблицы имеют отношение к главной**
```
ALTER TABLE pc (имя второй таблицы) ADD product_id (имя внешнего ключа) BIGINT (тип) 
REFERENCES (ссылается на) product (имя первой таблицы) (id) (поле таблицы, к которму ссылается);
```
3) **Заполнить таблицу данными**
```
INSERT INTO product ('имя таблицы') (maker, model, type)('строки в таблице') VALUES ('данные', 'данные', 'данные');
```
так заполнится одна строка 

4) **Дать права пользователю crud для модификации таблиц и выборки данных**
```
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON product TO usercrud;
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON pc TO usercrud;
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON laptop TO usercrud;
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON tablet TO usercrud;
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON mobile TO usercrud;
```
5) **Дать права пользователю read для выборки данных**
```
testdb=# GRANT SELECT ON product TO userread;
testdb=# GRANT SELECT ON pc TO userread;
testdb=# GRANT SELECT ON laptop TO userread;
testdb=# GRANT SELECT ON tablet TO userread;
testdb=# GRANT SELECT ON mobile TO userread;`
```
6) **Дать права пользователю crud для счетчика таблиц**
```
testdb=# GRANT USAGE, SELECT ON SEQUENCE product_id_seq TO usercrud;
testdb=# GRANT USAGE, SELECT ON SEQUENCE pc_id_pc_seq TO usercrud;
testdb=# GRANT USAGE, SELECT ON SEQUENCE laptop_id_laptop_seq TO usercrud;
testdb=# GRANT USAGE, SELECT ON SEQUENCE tablet_id_tablet_seq TO usercrud;
testdb=# GRANT USAGE, SELECT ON SEQUENCE mobile_id_mobile_seq TO usercrud;
```
