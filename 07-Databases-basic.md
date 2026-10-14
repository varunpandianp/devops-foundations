# DevOps Learning Notes: Databases, SQL, MySQL, SQL Server, Users, and Permissions

## 1. What Is a Database?

A database is an organized collection of data that applications can store, retrieve, update, and delete.

For example, an e-commerce application may store:

- Customer details
- Product information
- Orders
- Payments
- Inventory

Instead of storing all this information in separate text files, the application uses a database to manage the data efficiently.

### Example: E-commerce application

```text
E-commerce Application
        |
        v
    Database
        |
        |-- Customers
        |-- Products
        |-- Orders
        |-- Payments
```

The application sends queries to the database to read or modify information.

## 2. What Is a DBMS?

DBMS stands for Database Management System.

A DBMS is software used to create, manage, secure, and access databases.

Examples:

- MySQL
- Microsoft SQL Server
- PostgreSQL
- Oracle Database
- MongoDB
- Redis

A DBMS can provide features such as authentication, permissions, transactions, backups, recovery, and concurrent access.

### Database vs. DBMS

| Term | Meaning | Example |
|---|---|---|
| Database | Organized data | `company_db` |
| DBMS | Software managing databases | MySQL |
| Database server | Machine or service running the DBMS | MySQL running on a Linux server |
| Database client | Tool used to connect to a DBMS | MySQL CLI |
| SQL | Language used to query relational databases | `SELECT * FROM employees;` |

## 3. What Is SQL?

SQL stands for Structured Query Language.

It is commonly used to communicate with relational databases.

SQL can be used to:

- Create databases and tables
- Insert records
- Retrieve records
- Update records
- Delete records
- Create users
- Grant and revoke permissions

Example:

```sql
SELECT * FROM employees;
```

This query retrieves all columns and rows from the `employees` table.

**Important:** SQL is a language, not a database server. MySQL and Microsoft SQL Server are products that understand SQL dialects.

## 4. SQL vs. NoSQL Databases

Databases are commonly divided into relational SQL databases and different types of NoSQL databases.

### SQL databases

SQL databases commonly organize information into tables with rows and columns. Relationships between tables can be represented using primary keys and foreign keys.

Examples:

- MySQL
- Microsoft SQL Server
- PostgreSQL
- Oracle Database

Example table: `employees`

| id | name | department |
|---:|---|---|
| 1 | Arun | DevOps |
| 2 | Priya | Development |
| 3 | Ravi | Testing |

Each row represents an employee, and each column represents a particular attribute.

### NoSQL databases

NoSQL databases use different data models. Depending on the database, data may be stored as documents, key-value pairs, wide-column records, or graph structures.

Examples:

- MongoDB — document database
- Redis — key-value database
- Apache Cassandra — wide-column database
- Neo4j — graph database

Example MongoDB document:

```json
{
  "id": 1,
  "name": "Arun",
  "department": "DevOps",
  "skills": ["Linux", "Docker", "AWS"]
}
```

A document can contain nested objects and arrays, which may be convenient for certain application data models.

### SQL vs. NoSQL comparison

| Feature | SQL / relational | NoSQL |
|---|---|---|
| Common data model | Tables, rows, columns | Documents, key-value, wide-column, graph |
| Schema | Usually defined in advance | Varies by database; often more flexible |
| Relationships | Foreign keys and joins are common | Depends on the data model; references or embedded data are common |
| Query language | SQL dialects | Database-specific APIs or query languages |
| Transactions | Supported by major relational databases | Depends on the product and data model |
| Scaling | Vertical and horizontal approaches | Often designed with distributed scaling in mind, depending on the product |
| Examples | MySQL, SQL Server, PostgreSQL | MongoDB, Redis, Cassandra, Neo4j |

Neither SQL nor NoSQL is universally better. The choice depends on data structure, consistency requirements, query patterns, scale, and operational needs.

## 5. Tables, Rows, Columns, and Collections

### Relational database structure

```text
Database: company_db
    |
    |-- Table: employees
    |       |
    |       |-- Columns: id, name, department
    |       |-- Row 1: 1, Arun, DevOps
    |       |-- Row 2: 2, Priya, Development
    |
    |-- Table: projects
            |
            |-- Columns: project_id, project_name
```

Definitions:

- **Database:** Contains database objects such as tables, views, and other objects.
- **Table:** Organizes related records.
- **Row:** Represents one record.
- **Column:** Represents one attribute.
- **Primary key:** Uniquely identifies a row.
- **Foreign key:** Represents a relationship to a key in another table.

Example:

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    department VARCHAR(100)
);
```

Here, `id`, `name`, and `department` are columns. Each employee inserted becomes a row.

### NoSQL collection and document structure

In MongoDB, the common hierarchy is:

```text
MongoDB deployment
    |
    |-- Database: company_db
            |
            |-- Collection: employees
                    |
                    |-- Document 1
                    |-- Document 2
```

- **Database:** Contains collections.
- **Collection:** Groups related documents.
- **Document:** Contains an individual record, typically represented using BSON, a binary representation of JSON-like data.

### SQL vs. MongoDB terminology

| Relational database | MongoDB |
|---|---|
| Database | Database |
| Table | Collection |
| Row | Document |
| Column | Field |
| Primary key | `_id` field, commonly generated automatically |

These are useful conceptual comparisons, not exact equivalences. A MongoDB collection does not have to enforce the same fixed table schema as a relational table.

## 6. What Is MySQL?

MySQL is a relational database management system that uses SQL.

It is widely used for websites, APIs, and application backends.

Example architecture:

```text
User / Browser
      |
      v
Web Application
      |
      | SQL queries over a database connection
      v
MySQL Server
      |
      v
Database: ecommerce_db
      |
      |-- users
      |-- products
      |-- orders
```

MySQL commonly listens on TCP port `3306`, unless configured otherwise.

### Install MySQL client on Linux

On Debian or Ubuntu:

```bash
sudo apt update
sudo apt install mysql-client
```

On RHEL-family systems, package names and repositories vary. The client may be provided by packages such as `mysql` or `mariadb`.

To install a MySQL server on a supported Debian/Ubuntu system:

```bash
sudo apt install mysql-server
```

Installing a client is different from installing a database server.

## 7. What Is Microsoft SQL Server?

Microsoft SQL Server is a relational database management system developed by Microsoft.

It is commonly used for business applications, reporting, analytics, and enterprise systems.

It uses Transact-SQL (T-SQL), Microsoft's SQL dialect.

Example:

```text
Application
    |
    | T-SQL queries
    v
Microsoft SQL Server
    |
    |-- Database: CompanyDB
            |
            |-- Tables
            |-- Views
            |-- Stored procedures
```

The default TCP port for a typical SQL Server default instance is `1433`, although instances can use other ports.

### MySQL vs. Microsoft SQL Server

| Feature | MySQL | Microsoft SQL Server |
|---|---|---|
| Vendor | Oracle | Microsoft |
| Query language | MySQL SQL dialect | T-SQL |
| Typical default TCP port | 3306 | 1433 for a default instance |
| Linux support | Yes | Yes |
| Common client | `mysql` | `sqlcmd` |
| Database creation | `CREATE DATABASE` | `CREATE DATABASE` |
| User management | Accounts such as `'appuser'@'localhost'` | Server logins and database users |
| Permission system | MySQL privileges | Server permissions and database roles/permissions |

Although both support SQL, their user management, data types, functions, and administrative commands are not identical.

## 8. Connect to a MySQL Database

Suppose you have these details:

```text
Database server: 192.168.1.20
Port: 3306
Username: appuser
Database: company_db
```

Connect from the terminal:

```bash
mysql -h 192.168.1.20 -P 3306 -u appuser -p company_db
```

Explanation:

- `-h` specifies the server hostname or IP address.
- `-P` specifies the TCP port. It is uppercase.
- `-u` specifies the username.
- `-p` prompts for the password.
- `company_db` specifies the database to select after connecting.

For a local MySQL server:

```bash
mysql -u root -p
```

After connecting, select a database if one wasn't specified:

```sql
USE company_db;
```

Useful commands:

```sql
SHOW DATABASES;
SHOW TABLES;
SELECT DATABASE();
```

Exit the MySQL client:

```sql
EXIT;
```

## 9. Connect to Microsoft SQL Server

The SQL Server command-line client is commonly `sqlcmd`.

Example:

```bash
sqlcmd -S 192.168.1.20,1433 -U appuser -d CompanyDB -N
```

Depending on the installed `sqlcmd` version and authentication configuration, options may differ. With many versions, `-N` enables encryption. The server certificate must be trusted or configured appropriately; do not disable certificate validation as a default security practice.

For an interactive connection, enter the password when prompted if your installed client supports that behavior. Check `sqlcmd -?` for the exact syntax of your version.

Once connected, run T-SQL commands and terminate each batch with `GO` when required:

```sql
SELECT name FROM sys.databases;
GO
```

List tables in the current database:

```sql
SELECT TABLE_SCHEMA, TABLE_NAME
FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_TYPE = 'BASE TABLE';
GO
```

Exit:

```text
QUIT
```

**Remember:** MySQL's `mysql` client and Microsoft's `sqlcmd` client are different tools.

## 10. Basic SQL Commands — CRUD

CRUD means Create, Read, Update, and Delete. In database work, these terms often refer to creating records, retrieving them, modifying them, and deleting them.

The following examples use MySQL-compatible SQL. Most also work in SQL Server with suitable data types and syntax adjustments.

### Create a database

```sql
CREATE DATABASE company_db;
```

### Select the database

```sql
USE company_db;
```

### Create a table

```sql
CREATE TABLE employees (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    department VARCHAR(100),
    salary DECIMAL(10, 2)
);
```

`AUTO_INCREMENT` is MySQL syntax. In SQL Server, a similar column can be defined using `IDENTITY(1,1)`.

### Insert records

```sql
INSERT INTO employees (name, department, salary)
VALUES
    ('Arun', 'DevOps', 50000.00),
    ('Priya', 'Development', 60000.00),
    ('Ravi', 'Testing', 45000.00);
```

### Read records

Retrieve all columns:

```sql
SELECT * FROM employees;
```

Retrieve selected columns:

```sql
SELECT name, department
FROM employees;
```

Filter rows:

```sql
SELECT *
FROM employees
WHERE department = 'DevOps';
```

Sort results:

```sql
SELECT *
FROM employees
ORDER BY salary DESC;
```

Count records:

```sql
SELECT COUNT(*) FROM employees;
```

### Update a record

```sql
UPDATE employees
SET salary = 55000.00
WHERE id = 1;
```

Always check the `WHERE` clause before running an update. Without it, every row may be updated.

### Delete a record

```sql
DELETE FROM employees
WHERE id = 3;
```

Without a `WHERE` clause, this deletes every row in the table.

### Delete a table or database

```sql
DROP TABLE employees;
```

```sql
DROP DATABASE company_db;
```

These commands are destructive. Verify the target and take appropriate backups before using them on important systems.

## 11. SQL Command Categories

| Category | Meaning | Examples |
|---|---|---|
| DDL | Data Definition Language | `CREATE`, `ALTER`, `DROP` |
| DML | Data Manipulation Language | `INSERT`, `UPDATE`, `DELETE` |
| DQL | Data Query Language (commonly used classification) | `SELECT` |
| DCL | Data Control Language | `GRANT`, `REVOKE` |
| TCL | Transaction Control Language | `COMMIT`, `ROLLBACK` |

Example:

```sql
ALTER TABLE employees
ADD email VARCHAR(255);
```

This adds an `email` column in MySQL. SQL Server also supports `ALTER TABLE ... ADD`, with appropriate data types and syntax.

## 12. Create a MySQL User and Grant Permissions

A database application should generally connect using its own restricted account instead of the database administrator account.

### Step 1: Log in as an administrator

```bash
mysql -u root -p
```

### Step 2: Create a database

```sql
CREATE DATABASE app_db;
```

### Step 3: Create an application user

For an application connecting from the same server:

```sql
CREATE USER 'appuser'@'localhost'
IDENTIFIED BY 'ReplaceWithA_StrongUniquePassword';
```

For an application connecting from a specific application server, use that application's actual source IP instead of `localhost`, for example:

```sql
CREATE USER 'appuser'@'192.168.1.50'
IDENTIFIED BY 'ReplaceWithA_StrongUniquePassword';
```

The MySQL account name includes both the username and host component. `'appuser'@'localhost'` and `'appuser'@'192.168.1.50'` are distinct accounts.

### Step 4: Grant permissions

Allow the user to read data:

```sql
GRANT SELECT
ON app_db.*
TO 'appuser'@'localhost';
```

Allow reading and modifying records:

```sql
GRANT SELECT, INSERT, UPDATE, DELETE
ON app_db.*
TO 'appuser'@'localhost';
```

For an application that also needs to create or modify tables, additional privileges may be needed. Grant only what the application requires.

### Step 5: Verify permissions

```sql
SHOW GRANTS FOR 'appuser'@'localhost';
```

### Step 6: Test the user

Exit the administrator session:

```sql
EXIT;
```

Connect as the application user:

```bash
mysql -u appuser -p app_db
```

Then run:

```sql
SELECT DATABASE();
SHOW TABLES;
```

If the user has only `SELECT` permission, write operations should fail unless other privileges have been granted.

**Security note:** Use a strong unique password. Do not commit database credentials to GitHub or store them directly in application source code. Use environment variables or a secrets manager appropriate to your deployment.

## 13. Create a SQL Server Login, Database User, and Grant Permissions

SQL Server distinguishes between a **server login** and a **database user**.

- A login authenticates a connection to the SQL Server instance.
- A database user maps that identity into a particular database.
- Database permissions determine what the user can do inside that database.

The following example uses SQL Server authentication. Run the administrative statements using an authorized administrator account.

### Step 1: Create a database

```sql
CREATE DATABASE CompanyDB;
GO
```

### Step 2: Create a server login

```sql
USE master;
GO

CREATE LOGIN appuser
WITH PASSWORD = 'ReplaceWithA_StrongUniquePassword';
GO
```

### Step 3: Create a database user

```sql
USE CompanyDB;
GO

CREATE USER appuser FOR LOGIN appuser;
GO
```

### Step 4: Grant permissions

For read-only access:

```sql
GRANT SELECT TO appuser;
GO
```

For typical CRUD access on tables covered by the `dbo` schema:

```sql
GRANT SELECT, INSERT, UPDATE, DELETE TO appuser;
GO
```

Permissions can also be scoped to particular objects. For example:

```sql
GRANT SELECT ON dbo.Employees TO appuser;
GO
```

### Step 5: Verify permissions

```sql
SELECT
    dp.name AS database_user,
    dp.type_desc
FROM sys.database_principals AS dp
WHERE dp.name = 'appuser';
GO
```

To inspect explicit database permissions:

```sql
SELECT
    USER_NAME(grantee_principal_id) AS principal_name,
    permission_name,
    state_desc
FROM sys.database_permissions
WHERE grantee_principal_id = DATABASE_PRINCIPAL_ID('appuser');
GO
```

**Important:** Creating a login alone does not automatically grant access to every database. You must create or map a database user and grant the necessary permissions.

## 14. Common MySQL Permission Levels

MySQL privileges can be granted at different scopes.

| Scope | Example | Meaning |
|---|---|---|
| Global | `*.*` | Applies across databases, subject to the privilege |
| Database | `app_db.*` | Applies to objects in a database |
| Table | `app_db.employees` | Applies to a specific table |
| Column | Particular columns | Applies only to specified columns for supported privileges |

Example of database-level access:

```sql
GRANT SELECT
ON app_db.*
TO 'appuser'@'localhost';
```

This is generally safer than giving an application global privileges.

Avoid granting `ALL PRIVILEGES` or administrator-level roles to ordinary application accounts unless a genuine, reviewed requirement exists.

## 15. How Does an Application Connect to a Database?

Consider a Flask application connected to MySQL.

```text
Browser
   |
   v
Flask Application
   |
   | Database hostname/IP + port + credentials
   v
MySQL Server
   |
   v
Database: app_db
   |
   v
Tables: users, products, orders
```

The application needs:

- Database hostname or IP address
- Database port
- Database name
- Username
- Password or another supported authentication method
- Network access to the database
- Required database permissions

Example connection settings:

```text
DB_HOST=192.168.1.20
DB_PORT=3306
DB_NAME=app_db
DB_USER=appuser
DB_PASSWORD=<retrieve securely>
```

These are example values. Configure the actual values for your environment.

Do not use a database server's `127.0.0.1` address from another machine to reach that server. `127.0.0.1` always refers to the machine making the connection. For remote access, use the database server's reachable hostname or IP address.

## 16. Common Database Ports

| Database/service | Common default port |
|---|---:|
| MySQL | 3306 |
| PostgreSQL | 5432 |
| Microsoft SQL Server | 1433 for a typical default instance |
| MongoDB | 27017 |
| Redis | 6379 |
| Oracle Database listener | 1521 |

These are defaults, not guarantees. Administrators can configure different ports.

Database ports should normally be reachable only from authorized application servers and administrative clients. Avoid exposing database services directly to the public internet.

## 17. Troubleshooting Database Connections

### Check whether the service is running

For MySQL on a system using systemd:

```bash
sudo systemctl status mysql
```

Some Linux distributions use a service name such as `mysqld` or `mariadb`.

For SQL Server on Linux, the service is commonly:

```bash
sudo systemctl status mssql-server
```

The service name and management approach depend on the installation.

### Check listening ports

```bash
sudo ss -lntp
```

Filter for MySQL:

```bash
sudo ss -lntp | grep ':3306'
```

Filter for SQL Server:

```bash
sudo ss -lntp | grep ':1433'
```

### Test network reachability

For MySQL:

```bash
nc -vz 192.168.1.20 3306
```

For SQL Server:

```bash
nc -vz 192.168.1.20 1433
```

Replace the IP address and port with the actual database endpoint.

A successful TCP connection does not prove that the credentials or database permissions are correct. It only confirms that a TCP connection could be established.

### Common errors

- **Connection refused:** The service may be stopped, listening on another address/port, or rejecting the connection.
- **Connection timed out:** A firewall, routing problem, or network policy may be blocking access.
- **Access denied / login failed:** Check the username, password, authentication method, and account configuration.
- **Unknown database / cannot open database:** Check the database name and the user's access to it.
- **Permission denied for query:** The account may lack the required privilege.

## 18. DevOps Best Practices

1. Use a separate database account for each application.
2. Follow the principle of least privilege.
3. Keep passwords and connection strings out of source control.
4. Use encrypted database connections when supported and required.
5. Restrict database ports with firewalls and network policies.
6. Back up important databases and periodically test restoration.
7. Use migrations to manage schema changes in deployed applications.
8. Monitor connections, query latency, errors, storage, and database health.
9. Use transactions where multiple related changes must succeed or fail together.
10. Avoid destructive queries without verifying their scope and having a recovery plan.

## 19. Practice Exercises

Try these in a disposable local database or a lab environment.

- [ ] Create a database named `devops_lab`.
- [ ] Create a table named `employees`.
- [ ] Insert three employee records.
- [ ] Retrieve all employee records.
- [ ] Retrieve only employees in the DevOps department.
- [ ] Update one employee's salary.
- [ ] Delete one test record.
- [ ] Create a restricted application user.
- [ ] Grant only the required database permissions.
- [ ] Connect as that user and verify which queries succeed.
- [ ] Inspect the database service's listening port.
- [ ] Test a database connection from an application server.

## Learning Log

**Topic:** Database Fundamentals, SQL, NoSQL, MySQL, and Microsoft SQL Server

**What I learned:**
- Difference between a database, DBMS, and SQL.
- Difference between relational SQL databases and NoSQL data models.
- Tables, rows, columns, collections, documents, and fields.
- Differences between MySQL and Microsoft SQL Server.
- How to connect using `mysql` and `sqlcmd`.
- How to create databases and tables and perform CRUD operations.
- How to create database users and grant restricted permissions.
- Difference between a SQL Server login and database user.
- Common database ports and database connectivity troubleshooting.
- Database security and DevOps operational best practices.

**DevOps relevance:** Databases are essential to deploying applications, configuring environment variables and secrets, connecting containers to database services, managing access, troubleshooting network connections, and building reliable production systems.
