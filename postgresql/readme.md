# PostgreSQL Cheat Sheet

## Table of Contents

- [Installation](#installation)
- [Creating a Database](#creating-a-database)
- [Creating a User](#creating-a-user)
- [Creating a Schema](#creating-a-schema)
- [Creating a Table](#creating-a-table)
- [Managing Security](#managing-security)
- [Remote Access](#remote-access)
- [HTTPS Communication](#https-communication)
- [Backup and Restore](#backup-and-restore)

## Installation

To install PostgreSQL, follow these steps:

1. Step 1...
2. Step 2...
3. Step 3...

## Creating a Database

Switch user :

```bash
sudo -i -u postgres
```

Connect to posgreql

```bash
psql
```

List of database:

```bash
\l
```

List of user :

```bash
\du
```

Create a superuser `admin` with the password `admin` as below:

```postgresql
CREATE ROLE admin WITH LOGIN SUPERUSER CREATEDB CREATEROLE PASSWORD 'admin';
```

Create a `developer` user with password `developer`

```postgresql
CREATE USER developer WITH ENCRYPTED PASSWORD 'developer';
```

Create database testdb:

```postgresql
CREATE DATABASE testdb;
```

Grant priviliges of database to a user:

```postgresql
GRANT ALL PRIVILEGES ON DATABASE testdb TO developer;
```

Connect to database:

```postgresql
\c testdb;
```

Connect to database with a user:

```postgresql
\c testdb developer;
```

Create schema:

```postgresql
CREATE SCHEMA testschema;
```

Create Table:

```postgresql
CREATE TABLE schemaname.tablename (
    oid VARCHAR(255) NOT NULL name VARCHAR(255) NOT NULL PRIMARY KEY,
    name VARCHAR(255) NOT NULL UNIQUE,
    calendar_date DATE,
    calendar_year INTEGER,
    created_by VARCHAR(255) NOT NULL DEFAULT 'System',
    created_on TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    other_id INTEGER,
    
    FOREIGN KEY (other_id) REFERENCES other_table (other_column)
);
```

Add primary key with alter:

```postgresql
ALTER TABLE example_table
ADD PRIMARY KEY (id);
```

Alter Table add column:

```postgresql
ALTER TABLE schemaname.tablename
ADD COLUMN edited_by VARCHAR(255) DEFAULT 'System',
ADD COLUMN edited_on TIMESTAMP DEFAULT CURRENT_TIMESTAMP;
```

Alter table add foreign key:

```postgresql
ALTER TABLE schemaname.tablename
ADD CONSTRAINT fk_other_table
FOREIGN KEY (column_name) REFERENCES other_table (other_column);
```

Alter table add unique contraint:

```postgresql
ALTER TABLE schemaname.tablename
ADD CONSTRAINT name_unique UNIQUE (name);
```

Delete table:

```postgresql
DROP TABLE schemaname.tablename;
```

Insert data:

```postgresql
INSERT INTO schemaname.tablename (column1, column2, column3)
VALUES ('value1', 'value2', 'value3');
```

Delete all data:

```postgresql
TRUNCATE TABLE schemaname.tablename;
```