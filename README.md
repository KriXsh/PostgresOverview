# PostgreSQL: From Basics to Advanced

## 1. Introduction to PostgreSQL
### What is PostgreSQL?
- PostgreSQL is an advanced, open-source relational database management system (RDBMS).
- It supports SQL (Structured Query Language) and advanced features like JSON, window functions, and full-text search.
- It is ACID-compliant and supports multi-version concurrency control (MVCC).

### Installation
#### Linux (Ubuntu)
```sh
sudo apt update
sudo apt install postgresql postgresql-contrib
```
#### Mac (Homebrew)
```sh
brew install postgresql
```
#### Windows
- Download from [PostgreSQL official site](https://www.postgresql.org/download/)

### Starting & Managing PostgreSQL Service
```sh
sudo systemctl start postgresql
sudo systemctl enable postgresql
sudo systemctl status postgresql
```

### Accessing PostgreSQL
```sh
sudo -u postgres psql
```
Exit: `\q`

---

## 2. Basic Database & Table Operations
### Creating a Database
```sql
CREATE DATABASE mydb;
```
### Connecting to a Database
```sh
psql -d mydb -U postgres
```
### Creating a Table
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(150) UNIQUE,
    age INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
### Inserting Data
```sql
INSERT INTO users (name, email, age)
VALUES ('John Doe', 'john@example.com', 30);
```
### Selecting Data
```sql
SELECT * FROM users;
```
### Updating Data
```sql
UPDATE users SET age = 31 WHERE id = 1;
```
### Deleting Data
```sql
DELETE FROM users WHERE id = 1;
```

---

## 3. Advanced SQL Queries
### Joins
#### Inner Join
```sql
SELECT users.name, orders.amount
FROM users
INNER JOIN orders ON users.id = orders.user_id;
```
#### Left Join
```sql
SELECT users.name, orders.amount
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```
#### Right Join
```sql
SELECT users.name, orders.amount
FROM users
RIGHT JOIN orders ON users.id = orders.user_id;
```
#### Full Outer Join
```sql
SELECT users.name, orders.amount
FROM users
FULL OUTER JOIN orders ON users.id = orders.user_id;
```

### Aggregate Functions
```sql
SELECT COUNT(*) FROM users;
SELECT AVG(age) FROM users;
SELECT MAX(age) FROM users;
SELECT MIN(age) FROM users;
```

### Grouping Data
```sql
SELECT age, COUNT(*) FROM users GROUP BY age;
```

### Having Clause
```sql
SELECT age, COUNT(*)
FROM users
GROUP BY age
HAVING COUNT(*) > 1;
```

### Window Functions
```sql
SELECT name, age, RANK() OVER (ORDER BY age DESC) AS rank
FROM users;
```

---

## 4. Indexing for Performance
### Creating an Index
```sql
CREATE INDEX idx_users_email ON users(email);
```
### Removing an Index
```sql
DROP INDEX idx_users_email;
```
### Types of Indexes
- **B-tree Index** (default)
- **Hash Index**
- **GIN (Generalized Inverted Index)** for full-text search
- **BRIN (Block Range Index)** for large datasets

---

## 5. Transactions & Concurrency Control
### Transactions
```sql
BEGIN;
UPDATE users SET age = 35 WHERE id = 1;
COMMIT;
```
### Rolling Back a Transaction
```sql
BEGIN;
UPDATE users SET age = 40 WHERE id = 1;
ROLLBACK;
```

### Isolation Levels
```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;
```
Levels:
- Read Uncommitted
- Read Committed (Default)
- Repeatable Read
- Serializable

---

## 6. JSON & JSONB Support
### Creating a Table with JSONB
```sql
CREATE TABLE products (
    id SERIAL PRIMARY KEY,
    details JSONB
);
```
### Inserting JSON Data
```sql
INSERT INTO products (details) VALUES ('{"name": "Laptop", "price": 1200}');
```
### Querying JSON Data
```sql
SELECT details->>'name' FROM products;
```
### Indexing JSONB
```sql
CREATE INDEX idx_products_details ON products USING GIN(details);
```

---

## 7. Full-Text Search
### Enabling Full-Text Search
```sql
SELECT to_tsvector('english', 'PostgreSQL full-text search') @@ to_tsquery('PostgreSQL');
```
### Indexing for Full-Text Search
```sql
CREATE INDEX idx_users_name ON users USING GIN(to_tsvector('english', name));
```

---

## 8. Views & Materialized Views
### Creating a View
```sql
CREATE VIEW user_emails AS
SELECT name, email FROM users;
```
### Using a View
```sql
SELECT * FROM user_emails;
```
### Materialized View
```sql
CREATE MATERIALIZED VIEW user_summary AS
SELECT age, COUNT(*) FROM users GROUP BY age;
```
### Refreshing a Materialized View
```sql
REFRESH MATERIALIZED VIEW user_summary;
```

---

## 9. Stored Procedures & Functions
### Creating a Function
```sql
CREATE FUNCTION get_user_count() RETURNS INT AS $$
    SELECT COUNT(*) FROM users;
$$ LANGUAGE SQL;
```
### Calling a Function
```sql
SELECT get_user_count();
```
### Creating a Stored Procedure
```sql
CREATE PROCEDURE update_user_age(IN user_id INT, IN new_age INT)
LANGUAGE SQL
AS $$
    UPDATE users SET age = new_age WHERE id = user_id;
$$;
```
### Calling a Stored Procedure
```sql
CALL update_user_age(1, 32);
```

---

## 10. Backup & Restore
### Backing Up a Database
```sh
pg_dump -U postgres -d mydb -F c -f mydb_backup.sql
```
### Restoring a Database
```sh
pg_restore -U postgres -d mydb -F c mydb_backup.sql
```

---

## 11. Replication & High Availability
### Setting Up Streaming Replication
- Configure `postgresql.conf`
- Set `wal_level = replica`
- Configure `pg_hba.conf`
- Use `pg_basebackup` for replication

---

## 12. Security & Best Practices
### Creating a User
```sql
CREATE USER myuser WITH PASSWORD 'mypassword';
```
### Granting Privileges
```sql
GRANT ALL PRIVILEGES ON DATABASE mydb TO myuser;
```
### Revoking Privileges
```sql
REVOKE ALL PRIVILEGES ON DATABASE mydb FROM myuser;
```

---

## Conclusion
- PostgreSQL is powerful with advanced features like JSON support, full-text search, and replication.
- Performance tuning using indexing, caching, and optimized queries is essential.
- Security best practices include proper user management and backups.

