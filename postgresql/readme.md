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


List of schema :

```bash
\dn
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

Size of data in a schema:

```postgresql
SELECT 
    pg_size_pretty(sum(pg_total_relation_size(quote_ident(schemaname) || '.' || quote_ident(tablename)))::bigint) as size
FROM 
    pg_tables
WHERE 
    schemaname = 'your_schema_name';
```

Row counts of all the table under a schema:

```postgresql
SELECT 
    table_name, 
    (xpath('/row/cnt/text()', xml_count))[1]::text::int AS row_count
FROM (
    SELECT 
        table_name, 
        query_to_xml(format('SELECT COUNT(*) AS cnt FROM %I', table_name), false, true, '') AS xml_count
    FROM 
        information_schema.tables
    WHERE 
        table_schema = 'your_schema_name'
) t;
```

Row counts of all the table with non zero row counts under a schema:

```postgresql
SELECT 
    table_name, 
    (xpath('/row/cnt/text()', xml_count))[1]::text::int AS row_count
FROM (
    SELECT 
        table_name, 
        query_to_xml(format('SELECT COUNT(*) AS cnt FROM %I', table_name), false, true, '') AS xml_count
    FROM 
        information_schema.tables
    WHERE 
        table_schema = 'your_schema_name'
) t
WHERE 
    (xpath('/row/cnt/text()', xml_count))[1]::text::int > 0;
```

Kill all the unused connections:

```postgresql
SELECT 
    pg_terminate_backend(pid) 
FROM 
    pg_stat_activity 
WHERE 
    -- don't kill my own connection!
    pid <> pg_backend_pid()
    -- don't kill the connections to other databases
    AND datname = 'database_name'
    ;
```
