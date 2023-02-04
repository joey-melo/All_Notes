## PSQL

Local connection:
```bash
psql -h localhost -U dbuser -d broscience
```

After connection established:
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