������ � PostgreSQL � �������� �������������, ������� ����, �������� ���� ������, �������� ������
*postgres-# - ������ ��� ������� ������������� 

����� ���������
chcp 1251
*psql \! chcp 1251

*������� ���� ������
create database testdb (�������� ��);

������������ � ���� ������
psql -h <hostname or ip address> -p <port number of remote machine> -d <database name which you want to connect> -U <username of the database server>
��������, ������������ postgres, ���� ������ testdb: 
psql -d testdb -U postgres
��������, ������������ postgres:
psql -U postgres

������� ���� ������ testdb
\c testdb

*������� ������������ (CRUD)
create user usercrud (��� ������������) with encrypted password '������';
*���� ������������ CRUD-����� �� ���� ������ testdb
GRANT ALL PRIVILEGES ON DATABASE testdb (��� ���� ������) TO usercrud (��� ������������); 
****������� ������������ c ������������� �������!!!!!!
*������� ���� (READ)
CREATE ROLE readonly;
*���� ����� � ������������ ��������
GRANT USAGE ON SCHEMA public TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;
*���� ����� � ����� ��������
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO readonly;
*������� ������������ � ���� ��� ����
CREATE USER ��� ������������ WITH PASSWORD '������';
GRANT readonly TO ��� ������������;
*��������� ������������ ������������ � ��
GRANT CONNECT ON DATABASE testdb TO userread;

*��������� ����� ������������ ���������
\conninfo

�������� ������
***BIGSERIAL - id primay key ���������� �������������
������ ������� ���������� primay key!!!

*������� ������� ��� testdb (��� �������, � ������� ������� ������������ � �������)
CREATE TABLE (��� �������) (
id BIGSERIAL NOT NULL PRIMARY KEY,
maker VARCHAR(50) NOT NULL,
model VARCHAR(50) NOT NULL,
type VARCHAR(50) NOT NULL,
price NUMBERIC(5, 5)
);

\d - ��������� ������� � ������������������ (id)
\d product (��� �������) - ���������� ������� �������

*������� �������
ALTER TABLE pc (��� �������) DROP COLUMN screen (��� �������); 

*�������� ��� �������
ALTER TABLE pc ALTER COLUMN price TYPE numeric(12,2);

*���������� ������� ���� � ������� (����� �������) - ��������� ������� ����� ��������� � �������
ALTER TABLE pc (��� ������ �������) ADD product_id (��� �������� �����) BIGINT (���) 
REFERENCES (��������� ��) product (��� ������ �������) (id) (���� �������, � ������� ���������);

*��������� ������� �������
INSERT INTO product ('��� �������') (maker, model, type)('������ � �������') VALUES ('������', '������', '������');
��� ���������� ���� ������ 

* ���� ����� ������������ crud ��� ����������� ������ � ������� ������
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON product TO usercrud;
GRANT
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON pc TO usercrud;
GRANT
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON laptop TO usercrud;
GRANT
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON tablet TO usercrud;
GRANT
testdb=# GRANT SELECT, UPDATE, INSERT, DELETE ON mobile TO usercrud;

* ���� ����� ������������ read ��� ������� ������
testdb=# GRANT SELECT ON product TO userread;
GRANT
testdb=# GRANT SELECT ON pc TO userread;
GRANT
testdb=# GRANT SELECT ON laptop TO userread;
GRANT
testdb=# GRANT SELECT ON tablet TO userread;
GRANT
testdb=# GRANT SELECT ON mobile TO userread;

* ���� ����� ������������ crud ��� �������� ������
testdb=# GRANT USAGE, SELECT ON SEQUENCE product_id_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE pc_id_pc_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE laptop_id_laptop_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE tablet_id_tablet_seq TO usercrud;
GRANT
testdb=# GRANT USAGE, SELECT ON SEQUENCE mobile_id_mobile_seq TO usercrud;
