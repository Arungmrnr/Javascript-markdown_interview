# PostgreSQL SQL Verbs Cheat Sheet

This cheat sheet covers common SQL verbs, their categories, and examples. Useful for **development**, **interview preparation**, and **building a blog app**.

---

## 1. Data Definition Language (DDL)

Used to **define or modify database structures** (tables, schemas, indexes, etc.)

| Verb       | Usage | Example |
|------------|-------|---------|
| CREATE     | Create table, database, schema, index, view | `CREATE TABLE users (id SERIAL PRIMARY KEY, username VARCHAR(50));` |
| ALTER      | Modify existing table or object | `ALTER TABLE users ADD COLUMN email VARCHAR(100);` |
| DROP       | Delete table, database, object permanently | `DROP TABLE users;` |
| TRUNCATE   | Remove all rows from a table (keeps structure) | `TRUNCATE TABLE blogs;` |
| RENAME     | Rename tables or columns | `ALTER TABLE users RENAME TO blog_users;` |
| COMMENT    | Add comment/description to table or column | `COMMENT ON COLUMN users.email IS 'User email address';` |

---

## 2. Data Manipulation Language (DML)

Used to **manipulate data inside tables**.

| Verb       | Usage | Example |
|------------|-------|---------|
| INSERT     | Insert new rows | `INSERT INTO users (username, email) VALUES ('alice', 'alice@example.com');` |
| UPDATE     | Update existing rows | `UPDATE users SET email='alice_new@example.com' WHERE id=1;` |
| DELETE     | Delete rows | `DELETE FROM users WHERE id=1;` |
| SELECT     | Retrieve rows (Data Query Language) | `SELECT * FROM users;` |
| MERGE / UPSERT | Insert or update depending on existence | `INSERT INTO users (id, username) VALUES (1, 'bob') ON CONFLICT (id) DO UPDATE SET username='bob';` |

---

## 3. Data Control Language (DCL)

Used to **manage access and permissions**.

| Verb       | Usage | Example |
|------------|-------|---------|
| GRANT      | Give privileges | `GRANT SELECT, INSERT ON users TO blog_user;` |
| REVOKE     | Remove privileges | `REVOKE DELETE ON users FROM blog_user;` |

---

## 4. Transaction Control Language (TCL)

Used to **control transactions**.

| Verb       | Usage | Example |
|------------|-------|---------|
| COMMIT     | Save transaction permanently | `COMMIT;` |
| ROLLBACK   | Undo current transaction | `ROLLBACK;` |
| SAVEPOINT  | Create a rollback point | `SAVEPOINT before_update;` |

---

## 5. Special Notes / Tips

- **DELETE vs TRUNCATE**  
  - `DELETE` → remove specific rows, logs each deletion  
  - `TRUNCATE` → remove all rows instantly, faster, no WHERE clause  

- **VARCHAR vs TEXT**  
  - `VARCHAR(n)` → enforce max length  
  - `TEXT` → unlimited length  

- **Many-to-many relationships**  
  - Use junction table (`blog_topics`) with foreign keys for blogs ↔ topics  

- **Storing images**  
  - Recommended: save file paths in `TEXT[]` column, store files on disk/cloud  
  - Enforce max image count and size in backend (Node.js)

---

## 6. Example Blog Schema Verbs

```sql
-- Create tables
CREATE TABLE users (...);
CREATE TABLE topics (...);
CREATE TABLE blogs (...);
CREATE TABLE blog_topics (...);

-- Insert data
INSERT INTO topics (name, description) VALUES ('tech', 'Technology updates');

-- Update data
UPDATE blogs SET status='published' WHERE id=1;

-- Delete data
DELETE FROM blog_topics WHERE blog_id=1;

-- Alter table
ALTER TABLE blogs ADD COLUMN images TEXT[];
