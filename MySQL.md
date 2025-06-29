Here are some common MySQL interview questions along with their answers:

### 1. **What are the different types of JOINs in MySQL?**
   **Answer:**
   - **INNER JOIN**: Returns records that have matching values in both tables.
   - **LEFT (OUTER) JOIN**: Returns all records from the left table and matched records from the right table. If no match, NULL is returned.
   - **RIGHT (OUTER) JOIN**: Returns all records from the right table and matched records from the left table. If no match, NULL is returned.
   - **FULL (OUTER) JOIN**: Returns records when there is a match in one of the tables. If there is no match, NULL is returned (not supported directly in MySQL, can be emulated using `UNION`).
   - **CROSS JOIN**: Returns the Cartesian product of two tables, combining each row from the first table with all rows in the second table.

### 2. **What is the difference between `CHAR` and `VARCHAR` in MySQL?**
   **Answer:**
   - **CHAR**: It is a fixed-length data type. If you define a column as `CHAR(10)`, it will always take up 10 bytes, padding with spaces if necessary.
   - **VARCHAR**: It is a variable-length data type. If you define a column as `VARCHAR(10)` and store "abc", it will only use 3 bytes, not 10. The maximum size can be up to 65,535 bytes.

### 3. **How can you retrieve unique records from a table in MySQL?**
   **Answer:**
   You can use the `DISTINCT` keyword in the `SELECT` statement to return only unique records.
   ```sql
   SELECT DISTINCT column_name FROM table_name;
   ```

### 4. **What is the difference between `MyISAM` and `InnoDB` storage engines?**
   **Answer:**
   - **MyISAM**:
     - Does not support foreign keys.
     - Offers table-level locking.
     - Faster for read-heavy operations but not as good for write-heavy operations.
   - **InnoDB**:
     - Supports foreign keys and transactions (ACID compliant).
     - Provides row-level locking.
     - Preferred for applications requiring data integrity and concurrency handling.

### 5. **What is a PRIMARY KEY in MySQL?**
   **Answer:**
   A **PRIMARY KEY** is a column or a combination of columns that uniquely identifies each row in a table. It must contain unique values and cannot contain `NULL`. A table can have only one primary key, and it is used to index rows for quick access.

### 6. **How can you optimize a MySQL query?**
   **Answer:**
   - Use proper **indexing** on frequently searched columns.
   - Avoid using `SELECT *`, only select the columns you need.
   - Use **JOINs** instead of subqueries.
   - Use **EXPLAIN** to analyze query performance.
   - Avoid unnecessary calculations or functions in the `WHERE` clause.
   - Normalize the database to reduce data redundancy.

### 7. **What is normalization? What are the different normal forms?**
   **Answer:**
   **Normalization** is the process of organizing the columns and tables of a database to reduce redundancy and dependency. It involves dividing a large table into smaller tables and defining relationships between them.
   - **1NF (First Normal Form)**: Ensures that each column contains atomic values, and each record is unique.
   - **2NF (Second Normal Form)**: Achieves 1NF and ensures that all non-key attributes are fully functional dependencies on the primary key.
   - **3NF (Third Normal Form)**: Achieves 2NF and ensures that no transitive dependencies exist between non-key attributes.

### 8. **What is the difference between `DELETE`, `TRUNCATE`, and `DROP`?**
   **Answer:**
   - **DELETE**: Removes records from a table. You can specify a `WHERE` clause to filter which records to delete. This is a DML (Data Manipulation Language) command and can be rolled back.
   - **TRUNCATE**: Removes all records from a table but keeps the structure for future use. It is faster than `DELETE` as it doesn't log individual row deletions. This is a DDL (Data Definition Language) command and cannot be rolled back.
   - **DROP**: Deletes the entire table along with its structure from the database.

### 9. **What is indexing in MySQL?**
   **Answer:**
   Indexing is a way to optimize the performance of a database by reducing the number of disk I/O operations required when a query is processed. Indexes allow the database to find data faster, particularly in large tables. Common types of indexes in MySQL are:
   - **Primary Index**: Automatically created when a primary key is defined.
   - **Unique Index**: Ensures all values in the indexed column are unique.
   - **Full-Text Index**: Used for text searching.
   - **Composite Index**: An index on multiple columns.

### 10. **What are transactions in MySQL?**
   **Answer:**
   A **transaction** is a sequence of database operations that are executed as a single unit of work. Transactions ensure data integrity and are used for controlling operations like insertions, updates, and deletions. A transaction has the following properties (ACID):
   - **Atomicity**: All operations in a transaction are executed or none at all.
   - **Consistency**: The database remains in a consistent state before and after the transaction.
   - **Isolation**: Transactions are isolated from each other.
   - **Durability**: Once a transaction is committed, the changes are permanent.

### 11. **What is a foreign key in MySQL?**
   **Answer:**
   A **foreign key** is a field in one table that refers to the primary key in another table. It is used to establish a link between the data in two tables, ensuring referential integrity. When a foreign key constraint is applied, it ensures that the value in the child table must match a value in the parent table, or be `NULL`.

### 12. **What is a view in MySQL?**
   **Answer:**
   A **view** is a virtual table based on the result of a `SELECT` query. It does not store the data itself but stores the query that defines the view. Views are used to simplify complex queries, secure data by restricting access to specific columns, and present data in a customized way.

### 13. **How can you create an index in MySQL?**
   **Answer:**
   To create an index, you can use the `CREATE INDEX` statement.
   ```sql
   CREATE INDEX index_name ON table_name (column_name);
   ```

### 14. **What is a stored procedure?**
   **Answer:**
   A **stored procedure** is a set of SQL statements that can be stored in the database and executed repeatedly. It is used to encapsulate complex SQL logic, improve performance, and ensure reusability.

   ```sql
   DELIMITER //
   CREATE PROCEDURE proc_name()
   BEGIN
     -- SQL statements
   END //
   DELIMITER ;
   ```

### 15. **What is a trigger in MySQL?**
   **Answer:**
   A **trigger** is a set of instructions that are automatically executed in response to specific events on a table, such as `INSERT`, `UPDATE`, or `DELETE`. Triggers are useful for enforcing business rules and maintaining data integrity.

   ```sql
   CREATE TRIGGER trigger_name
   BEFORE INSERT ON table_name
   FOR EACH ROW
   BEGIN
     -- SQL statements
   END;
   ```

These questions cover a wide range of MySQL concepts and are frequently asked in interviews.

-------

## Transaction isolation levels

Database **transaction isolation levels** define the degree to which the operations in one transaction are isolated from the operations in other concurrent transactions. They control how data is visible to other transactions before a transaction is committed and aim to balance the need for data consistency with the need for concurrent performance. The four commonly recognized isolation levels (as defined by the SQL standard) are:

### 1. **Read Uncommitted**
   - **Description**: The lowest level of isolation, where a transaction can read data that is not yet committed by other transactions. This is sometimes referred to as a "dirty read."
   - **Issues**:
     - **Dirty Reads**: You can read changes made by other transactions that are not yet committed. If those changes are rolled back, you may have read invalid data.
     - **Non-repeatable Reads** and **Phantom Reads** may also occur.

### 2. **Read Committed**
   - **Description**: A transaction can only read data that has been committed by other transactions. Changes made by other transactions are not visible until they are committed. This is a common default isolation level in many databases.
   - **Issues**:
     - **Non-repeatable Reads**: If a transaction reads the same row twice, it may see different values if another transaction commits changes between the two reads.
     - **Phantom Reads**: If a query is executed twice, new rows inserted by another transaction between the queries may appear in the second result set.

### 3. **Repeatable Read**
   - **Description**: Ensures that if a transaction reads a row, it will see the same value every time it reads that row again during the transaction, even if other transactions are committing changes. This level prevents **dirty reads** and **non-repeatable reads**.
   - **Issues**:
     - **Phantom Reads**: It does not prevent the possibility of new rows being added by other transactions that would match the query conditions if run again.

### 4. **Serializable**
   - **Description**: The highest isolation level, where transactions are completely isolated from one another. It ensures that transactions execute in such a way that the result is the same as if the transactions had been executed sequentially, one after another, without overlap.
   - **Issues**:
     - **Performance**: This level often requires locking the entire data set that a transaction interacts with, which can greatly reduce the number of transactions that can run concurrently. It ensures **no dirty reads**, **non-repeatable reads**, or **phantom reads**.
   
### Summary of Concurrency Phenomena

| Isolation Level   | Dirty Reads | Non-Repeatable Reads | Phantom Reads |
|-------------------|-------------|----------------------|---------------|
| Read Uncommitted  | Yes         | Yes                  | Yes           |
| Read Committed    | No          | Yes                  | Yes           |
| Repeatable Read   | No          | No                   | Yes           |
| Serializable      | No          | No                   | No            |

### Use Cases
- **Read Uncommitted**: Suitable for applications that can tolerate inconsistent data for the sake of performance, but it's rarely used due to the risks of dirty reads.
- **Read Committed**: Most commonly used level as it offers a balance between performance and consistency.
- **Repeatable Read**: Useful for situations where consistency is more critical, such as financial transactions.
- **Serializable**: Used in scenarios where data integrity is of utmost importance, and concurrency is less of a concern.

Each level is a trade-off between data consistency and performance. The choice depends on the specific requirements of the application in terms of reliability and throughput.
