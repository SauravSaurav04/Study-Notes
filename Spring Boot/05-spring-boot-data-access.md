# 🗄️ Spring Boot Data Access - MUST KNOW

**Priority:** 🔴 CRITICAL ⭐ HIGH INTERVIEW FOCUS | **Interview Weight:** EXTREMELY HIGH

---

## 📑 Table of Contents

1. [JDBC](#51-jdbc)
   - [What is JDBC?](#-what-is-jdbc)
   - [Why Do We Need JDBC?](#-why-do-we-need-jdbc)
   - [Important Core JDBC Methods](#-important-core-jdbc-methods--must-know)
     - [DriverManager Methods](#-1-drivermanager-methods)
     - [Connection Methods](#-2-connection-methods)
     - [Statement Interface](#-3-statement-interface-basic---avoid-in-production)
     - [PreparedStatement Interface](#-4-preparedstatement-interface--recommended)
     - [CallableStatement Interface](#-5-callablestatement-interface)
     - [ResultSet Interface](#-6-resultset-interface)
     - [Method Comparison](#-7-method-comparison-execute-vs-executequery-vs-executeupdate)
     - [Best Practices](#-8-important-best-practices-)
     - [Quick Reference Table](#-9-quick-reference-table)
     - [Interview Tips](#-10-interview-tips-)
   - [JDBC in Core Java](#-jdbc-in-core-java-traditional-approach)
   - [JDBC in Spring Boot](#-jdbc-in-spring-boot-jdbctemplate)
   - [Core Java JDBC vs Spring Boot JDBC](#-core-java-jdbc-vs-spring-boot-jdbc)
   - [Connection Pooling](#-connection-pooling)
   - [Key Interview Points](#-key-interview-points)
   - [Using H2 Database with JDBC](#-using-h2-database-with-jdbc)
2. [Spring Data JPA](#52-spring-data-jpa--critical)
   - [Setup](#-setup)
   - [JPA Repositories](#-jpa-repositories)
   - [Query Methods](#-query-methods-method-name-parsing)
   - [Custom Queries (@Query)](#-custom-queries-query)
3. [Hibernate/JPA Concepts](#53-hibernatejpa-concepts)
   - [Entity Mapping](#-entity-mapping)
   - [Relationships](#-relationships--extremely-important)
   - [Fetch Types](#-fetch-types-critical)
   - [N+1 Problem](#-n1-problem--critical-interview-topic)
4. [Transactions](#54-transactions)
   - [@Transactional](#-transactional--critical)
   - [Propagation Levels](#-propagation-levels)
   - [Isolation Levels](#-isolation-levels)
5. [Database Migration](#55-database-migration)
   - [Flyway](#-flyway-recommended)
   - [Liquibase](#-liquibase-alternative)
6. [Common Interview Questions](#-common-interview-questions)
7. [Quick Revision Checklist](#-quick-revision-checklist)
8. [Practice Exercise](#-practice-exercise)

---

## 5.1 JDBC

### 🔹 What is JDBC?

**JDBC (Java Database Connectivity)** is a Java API that enables Java applications to interact with relational databases. It provides a standard way to connect to databases, execute SQL queries, and process results.

**Key Components:**
1. **JDBC API** - Interfaces and classes in `java.sql` and `javax.sql` packages
2. **JDBC Driver Manager** - Manages database drivers
3. **JDBC Driver** - Database-specific implementation (MySQL Driver, Oracle Driver, etc.)
4. **Connection** - Represents a session with the database
5. **Statement/PreparedStatement** - Executes SQL queries
6. **ResultSet** - Holds data retrieved from database

**JDBC Architecture:**
```
┌─────────────────────────────────────────┐
│       Java Application                  │
│  (Your Code: DAO, Repository, etc.)     │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│         JDBC API                        │
│  (java.sql, javax.sql interfaces)       │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│      JDBC Driver Manager                │
│  (Loads and manages drivers)            │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│        JDBC Driver                      │
│  (MySQL, PostgreSQL, Oracle, etc.)      │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│           Database                      │
│  (MySQL, PostgreSQL, Oracle, etc.)      │
└─────────────────────────────────────────┘
```

---

### 🔹 Why Do We Need JDBC?

**Problems Without JDBC:**
1. ❌ Each database has different native APIs (MySQL C API, Oracle OCI, etc.)
2. ❌ Platform-dependent code (not portable)
3. ❌ Need to learn different APIs for different databases
4. ❌ Code is tightly coupled with specific database

**Benefits of JDBC:**
1. ✅ **Database Independence** - Same code works with any database (just change driver)
2. ✅ **Standard API** - Learn once, use with any RDBMS
3. ✅ **Platform Independence** - Pure Java solution (Write Once, Run Anywhere)
4. ✅ **Easy Migration** - Switch databases with minimal code changes
5. ✅ **Built into Java** - No external dependencies for basic functionality

---

### 🔹 Important Core JDBC Methods ⭐ MUST KNOW

Before diving into JDBC implementation, let's understand the **essential methods** that every Java developer must know!

---

#### 📌 1. DriverManager Methods

**Purpose:** Manages database drivers and establishes connections.

```java
// Get database connection
public static Connection getConnection(String url, String user, String password) 
    throws SQLException

// Example
Connection conn = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/mydb",
    "root",
    "password"
);
```

**Key Methods:**

| Method | Description | Example |
|--------|-------------|---------|
| `getConnection(String url)` | Connect with URL only | `getConnection("jdbc:h2:mem:test")` |
| `getConnection(String url, String user, String password)` | Connect with credentials | `getConnection(url, "root", "pass")` |
| `getConnection(String url, Properties props)` | Connect with properties | `getConnection(url, properties)` |

---

#### 📌 2. Connection Methods

**Purpose:** Represents a database session, creates statements, manages transactions.

```java
Connection conn = DriverManager.getConnection(url, user, password);
```

**Essential Methods:**

| Method | Return Type | Description | Use Case |
|--------|-------------|-------------|----------|
| `createStatement()` | `Statement` | Creates a basic SQL statement | Simple queries without parameters |
| `prepareStatement(String sql)` | `PreparedStatement` | Creates a precompiled SQL statement | Queries with parameters (RECOMMENDED) |
| `prepareCall(String sql)` | `CallableStatement` | Creates a callable statement | Calling stored procedures |
| `commit()` | `void` | Commits current transaction | Manual transaction management |
| `rollback()` | `void` | Rolls back current transaction | Undo changes on error |
| `setAutoCommit(boolean)` | `void` | Enable/disable auto-commit | Transaction control |
| `close()` | `void` | Closes the connection | Release resources |
| `isClosed()` | `boolean` | Checks if connection is closed | Validation |
| `getMetaData()` | `DatabaseMetaData` | Gets database metadata | Database information |

**Example:**
```java
Connection conn = DriverManager.getConnection(url, user, password);

// Create different types of statements
Statement stmt = conn.createStatement();
PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
CallableStatement cstmt = conn.prepareCall("{call getUserById(?)}");

// Transaction management
conn.setAutoCommit(false);  // Start manual transaction
try {
    // Execute multiple statements
    stmt.executeUpdate("INSERT INTO users ...");
    stmt.executeUpdate("UPDATE accounts ...");
    conn.commit();  // Commit if all succeed
} catch (SQLException e) {
    conn.rollback();  // Rollback on error
}

conn.close();  // Always close!
```

---

#### 📌 2.1. Transaction Management: `commit()` and `setAutoCommit()` ⭐ IMPORTANT

**Understanding Transactions:**

A **transaction** is a unit of work that must be completed entirely or not at all (all-or-nothing).

**ACID Properties:**
- **Atomicity**: All operations succeed or all fail
- **Consistency**: Database remains in valid state
- **Isolation**: Concurrent transactions don't interfere
- **Durability**: Committed changes are permanent

---

**`setAutoCommit(boolean autoCommit)` - Controls Transaction Mode**

```java
connection.setAutoCommit(true);   // Default - each statement auto-committed
connection.setAutoCommit(false);  // Manual mode - must call commit()
```

**Default Behavior (autoCommit = true):**
```java
Connection conn = DriverManager.getConnection(url, user, pass);
// autoCommit is TRUE by default

PreparedStatement pstmt = conn.prepareStatement("INSERT INTO users ...");
pstmt.executeUpdate();  // ✅ Immediately committed to database
// No need to call commit() - already saved!
```

**Manual Transaction Mode (autoCommit = false):**
```java
Connection conn = DriverManager.getConnection(url, user, pass);
conn.setAutoCommit(false);  // ← Enable manual transaction control

PreparedStatement pstmt = conn.prepareStatement("INSERT INTO users ...");
pstmt.executeUpdate();  // ⚠️ NOT yet committed - still in transaction
// Data visible to this connection, but NOT to others yet
// Must call commit() to make it permanent!

conn.commit();  // ✅ Now saved to database
```

---

**`commit()` - Makes Changes Permanent**

Makes all changes since last commit/rollback permanent. **Only works when `autoCommit = false`!**

---

**Why Are They Important?**

**Problem Without Transaction Control:**

```java
// Scenario: Transfer $100 from Account A to Account B
// What if power fails between these two operations?

executeUpdate("UPDATE accounts SET balance = balance - 100 WHERE id = 1"); // ✅ Succeeds
// 💥 POWER FAILURE HERE!
executeUpdate("UPDATE accounts SET balance = balance + 100 WHERE id = 2"); // ❌ Never executes

// Result: $100 disappeared! Account A lost money, Account B didn't receive it!
```

**Solution: Use Transactions**

```java
Connection conn = null;
try {
    conn = DriverManager.getConnection(url, user, pass);
    conn.setAutoCommit(false);  // 1️⃣ Start transaction
    
    // 2️⃣ Execute multiple related operations
    PreparedStatement pstmt1 = conn.prepareStatement(
        "UPDATE accounts SET balance = balance - ? WHERE id = ?");
    pstmt1.setBigDecimal(1, new BigDecimal("100"));
    pstmt1.setLong(2, 1L);
    pstmt1.executeUpdate();
    
    PreparedStatement pstmt2 = conn.prepareStatement(
        "UPDATE accounts SET balance = balance + ? WHERE id = ?");
    pstmt2.setBigDecimal(1, new BigDecimal("100"));
    pstmt2.setLong(2, 2L);
    pstmt2.executeUpdate();
    
    conn.commit();  // 3️⃣ All succeed - commit together!
    System.out.println("✅ Transaction committed successfully");
    
} catch (SQLException e) {
    if (conn != null) {
        try {
            conn.rollback();  // 4️⃣ Any failure - undo everything!
            System.out.println("❌ Transaction rolled back");
        } catch (SQLException ex) {
            ex.printStackTrace();
        }
    }
} finally {
    if (conn != null) {
        try {
            conn.setAutoCommit(true);  // 5️⃣ Restore default
            conn.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

---

**Complete Comparison**

| Aspect | `autoCommit = true` (Default) | `autoCommit = false` |
|--------|------------------------------|----------------------|
| **Each statement** | Automatically committed | Stays in transaction |
| **Requires commit()** | ❌ No | ✅ Yes |
| **Can rollback** | ❌ No | ✅ Yes |
| **Use case** | Single independent operations | Multiple related operations |
| **Example** | Simple SELECT, single INSERT | Bank transfer, order creation |
| **Performance** | Slower (commits each time) | Faster (batches commits) |
| **Safety** | Less safe for multi-step ops | Safe for multi-step operations |

---

**Practical Example: Creating Order with Items**

```java
Connection conn = DriverManager.getConnection(url, user, pass);
conn.setAutoCommit(false);

try {
    // Step 1: Insert order
    PreparedStatement orderStmt = conn.prepareStatement(
        "INSERT INTO orders (user_id, total_amount) VALUES (?, ?)",
        Statement.RETURN_GENERATED_KEYS);
    orderStmt.setLong(1, userId);
    orderStmt.setBigDecimal(2, totalAmount);
    orderStmt.executeUpdate();
    
    // Get generated order ID
    ResultSet rs = orderStmt.getGeneratedKeys();
    rs.next();
    long orderId = rs.getLong(1);
    
    // Step 2: Insert order items
    PreparedStatement itemStmt = conn.prepareStatement(
        "INSERT INTO order_items (order_id, product_id, quantity) VALUES (?, ?, ?)");
    
    for (OrderItem item : items) {
        itemStmt.setLong(1, orderId);
        itemStmt.setLong(2, item.getProductId());
        itemStmt.setInt(3, item.getQuantity());
        itemStmt.executeUpdate();
    }
    
    // Step 3: Update product inventory
    PreparedStatement invStmt = conn.prepareStatement(
        "UPDATE products SET stock = stock - ? WHERE id = ?");
    
    for (OrderItem item : items) {
        invStmt.setInt(1, item.getQuantity());
        invStmt.setLong(2, item.getProductId());
        invStmt.executeUpdate();
    }
    
    conn.commit();  // ✅ All steps succeed - commit!
    
} catch (SQLException e) {
    conn.rollback();  // ❌ Any failure - undo everything!
    throw e;
} finally {
    conn.setAutoCommit(true);
    conn.close();
}
```

---

**Key Interview Points:**

**Q: When do you need to call `commit()`?**
- Only when `autoCommit = false`
- For multi-step operations that must succeed/fail together
- When you want to control exactly when changes become permanent

**Q: What happens if you forget to call `commit()`?**
- Changes remain in transaction (not saved)
- Other connections cannot see your changes
- When connection closes, uncommitted changes are lost (rolled back)

**Q: When should you use `setAutoCommit(false)`?**
- ✅ Bank transfers (debit one account, credit another)
- ✅ Creating order with items
- ✅ Batch processing (better performance)
- ✅ Multi-table updates that must be atomic
- ❌ Simple SELECT queries
- ❌ Single INSERT/UPDATE/DELETE

**Q: What is `rollback()`?**
- Undoes all changes since last commit
- Only works when `autoCommit = false`
- Used in catch blocks to undo failed transactions

---

**Best Practices:**

```java
// ✅ GOOD: Proper transaction management
Connection conn = null;
try {
    conn = getConnection();
    conn.setAutoCommit(false);
    
    // Do multiple operations
    performOperation1(conn);
    performOperation2(conn);
    
    conn.commit();  // Success - save all
    
} catch (Exception e) {
    if (conn != null) {
        conn.rollback();  // Failure - undo all
    }
} finally {
    if (conn != null) {
        conn.setAutoCommit(true);  // Restore default
        conn.close();
    }
}

// ❌ BAD: Forgot to commit
conn.setAutoCommit(false);
pstmt.executeUpdate("INSERT ...");
// Forgot conn.commit()!
conn.close();  // Changes lost!

// ❌ BAD: Using transaction for single operation
conn.setAutoCommit(false);  // Unnecessary!
pstmt.executeQuery("SELECT * FROM users");
conn.commit();  // Overkill for simple read
```

---

#### 📌 3. Statement Interface (Basic - Avoid in Production!)

**Purpose:** Executes simple SQL queries without parameters.

**⚠️ Warning:** Vulnerable to SQL injection! Use `PreparedStatement` instead.

```java
Statement stmt = conn.createStatement();
```

**Key Methods:**

| Method | Return Type | Description | SQL Type |
|--------|-------------|-------------|----------|
| `executeQuery(String sql)` | `ResultSet` | Execute SELECT query | DQL (Data Query Language) |
| `executeUpdate(String sql)` | `int` | Execute INSERT/UPDATE/DELETE | DML (Data Manipulation Language) |
| `execute(String sql)` | `boolean` | Execute any SQL statement | Any SQL |
| `executeBatch()` | `int[]` | Execute batch of statements | Batch operations |
| `addBatch(String sql)` | `void` | Add SQL to batch | Batch preparation |
| `close()` | `void` | Close the statement | Resource cleanup |

**Example:**
```java
Statement stmt = conn.createStatement();

// 1️⃣ executeQuery() - For SELECT statements
ResultSet rs = stmt.executeQuery("SELECT * FROM users");
while (rs.next()) {
    System.out.println(rs.getString("username"));
}

// 2️⃣ executeUpdate() - For INSERT/UPDATE/DELETE
int rowsInserted = stmt.executeUpdate(
    "INSERT INTO users (username, email) VALUES ('john', 'john@example.com')"
);
System.out.println(rowsInserted + " row(s) inserted");

int rowsUpdated = stmt.executeUpdate(
    "UPDATE users SET email = 'newemail@example.com' WHERE id = 1"
);
System.out.println(rowsUpdated + " row(s) updated");

int rowsDeleted = stmt.executeUpdate("DELETE FROM users WHERE id = 1");
System.out.println(rowsDeleted + " row(s) deleted");

// 3️⃣ execute() - For any SQL (returns true if ResultSet, false if update count)
boolean hasResultSet = stmt.execute("SELECT * FROM users");
if (hasResultSet) {
    ResultSet rs2 = stmt.getResultSet();
    // Process ResultSet
}

// 4️⃣ Batch execution - Execute multiple statements at once
stmt.addBatch("INSERT INTO users (username) VALUES ('user1')");
stmt.addBatch("INSERT INTO users (username) VALUES ('user2')");
stmt.addBatch("INSERT INTO users (username) VALUES ('user3')");
int[] results = stmt.executeBatch();  // Returns array of update counts

stmt.close();
```

**❌ SQL Injection Vulnerability:**
```java
// DANGER! Never do this!
String userId = getUserInput();  // User enters: "1 OR 1=1"
String sql = "SELECT * FROM users WHERE id = " + userId;
ResultSet rs = stmt.executeQuery(sql);
// SQL becomes: SELECT * FROM users WHERE id = 1 OR 1=1
// Returns ALL users! Security breach!
```

---

#### 📌 4. PreparedStatement Interface ✅ RECOMMENDED!

**Purpose:** Executes precompiled SQL with parameters - **PREVENTS SQL INJECTION!**

**Why PreparedStatement is Better:**
1. ✅ **SQL Injection Safe** - Parameters are escaped automatically
2. ✅ **Performance** - Query is precompiled (faster for repeated execution)
3. ✅ **Readability** - Cleaner code with placeholders
4. ✅ **Type Safety** - Type-specific setter methods

```java
String sql = "SELECT * FROM users WHERE username = ? AND age > ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
//                                               ↑      ↑
//                                            Placeholder (?)
```

**Key Methods:**

| Method | Description | Example |
|--------|-------------|---------|
| `setString(int index, String value)` | Set String parameter | `pstmt.setString(1, "john")` |
| `setInt(int index, int value)` | Set int parameter | `pstmt.setInt(2, 25)` |
| `setLong(int index, long value)` | Set long parameter | `pstmt.setLong(1, 100L)` |
| `setDouble(int index, double value)` | Set double parameter | `pstmt.setDouble(1, 99.99)` |
| `setBoolean(int index, boolean value)` | Set boolean parameter | `pstmt.setBoolean(1, true)` |
| `setDate(int index, Date value)` | Set Date parameter | `pstmt.setDate(1, sqlDate)` |
| `setTimestamp(int index, Timestamp value)` | Set Timestamp parameter | `pstmt.setTimestamp(1, ts)` |
| `setNull(int index, int sqlType)` | Set NULL value | `pstmt.setNull(1, Types.VARCHAR)` |
| `setObject(int index, Object value)` | Set any object | `pstmt.setObject(1, obj)` |
| `executeQuery()` | Execute SELECT (no parameter!) | `ResultSet rs = pstmt.executeQuery()` |
| `executeUpdate()` | Execute INSERT/UPDATE/DELETE | `int count = pstmt.executeUpdate()` |
| `execute()` | Execute any SQL | `boolean result = pstmt.execute()` |
| `addBatch()` | Add to batch | `pstmt.addBatch()` |
| `executeBatch()` | Execute batch | `int[] results = pstmt.executeBatch()` |
| `clearParameters()` | Clear parameter values | `pstmt.clearParameters()` |

**Complete Example:**

```java
// ✅ SAFE - PreparedStatement prevents SQL injection
String sql = "SELECT * FROM users WHERE username = ? AND age > ?";
PreparedStatement pstmt = conn.prepareStatement(sql);

// Set parameters (index starts at 1, not 0!)
pstmt.setString(1, "john_doe");  // First ?
pstmt.setInt(2, 18);             // Second ?

// Execute query
ResultSet rs = pstmt.executeQuery();

while (rs.next()) {
    System.out.println(rs.getString("username"));
}

pstmt.close();
```

**INSERT Example:**
```java
String sql = "INSERT INTO users (username, email, age) VALUES (?, ?, ?)";
PreparedStatement pstmt = conn.prepareStatement(sql);

pstmt.setString(1, "john_doe");
pstmt.setString(2, "john@example.com");
pstmt.setInt(3, 25);

int rowsInserted = pstmt.executeUpdate();
System.out.println(rowsInserted + " row(s) inserted");
```

**UPDATE Example:**
```java
String sql = "UPDATE users SET email = ? WHERE id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);

pstmt.setString(1, "newemail@example.com");
pstmt.setLong(2, 1L);

int rowsUpdated = pstmt.executeUpdate();
System.out.println(rowsUpdated + " row(s) updated");
```

**DELETE Example:**
```java
String sql = "DELETE FROM users WHERE id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);

pstmt.setLong(1, 1L);

int rowsDeleted = pstmt.executeUpdate();
System.out.println(rowsDeleted + " row(s) deleted");
```

**Batch Processing with PreparedStatement:**
```java
String sql = "INSERT INTO users (username, email) VALUES (?, ?)";
PreparedStatement pstmt = conn.prepareStatement(sql);

// Add multiple batches
for (int i = 1; i <= 1000; i++) {
    pstmt.setString(1, "user" + i);
    pstmt.setString(2, "user" + i + "@example.com");
    pstmt.addBatch();  // Add to batch
    
    // Execute batch every 100 records (for performance)
    if (i % 100 == 0) {
        pstmt.executeBatch();
        pstmt.clearBatch();
    }
}

// Execute remaining
pstmt.executeBatch();
pstmt.close();
```

**Retrieving Auto-Generated Keys:**
```java
String sql = "INSERT INTO users (username, email) VALUES (?, ?)";
PreparedStatement pstmt = conn.prepareStatement(sql, Statement.RETURN_GENERATED_KEYS);

pstmt.setString(1, "john_doe");
pstmt.setString(2, "john@example.com");
pstmt.executeUpdate();

// Get generated ID
ResultSet generatedKeys = pstmt.getGeneratedKeys();
if (generatedKeys.next()) {
    long id = generatedKeys.getLong(1);
    System.out.println("Generated ID: " + id);
}
```

---

#### 📌 5. CallableStatement Interface

**Purpose:** Execute stored procedures and functions in the database.

```java
// Calling stored procedure
CallableStatement cstmt = conn.prepareCall("{call getUserById(?)}");
cstmt.setInt(1, 10);
ResultSet rs = cstmt.executeQuery();

// Calling function with OUT parameter
CallableStatement cstmt2 = conn.prepareCall("{? = call getUsername(?)}");
cstmt2.registerOutParameter(1, Types.VARCHAR);  // Return value
cstmt2.setInt(2, 10);  // Input parameter
cstmt2.execute();
String username = cstmt2.getString(1);
```

**Key Methods:**

| Method | Description | Example |
|--------|-------------|---------|
| `registerOutParameter(int index, int sqlType)` | Register OUT parameter | `cstmt.registerOutParameter(1, Types.INTEGER)` |
| `setInt(int index, int value)` | Set IN parameter | `cstmt.setInt(1, 100)` |
| `getInt(int index)` | Get OUT parameter | `int result = cstmt.getInt(1)` |
| `getString(int index)` | Get String OUT parameter | `String name = cstmt.getString(1)` |

---

#### 📌 6. ResultSet Interface

**Purpose:** Represents the result of a SELECT query (table of data).

```java
ResultSet rs = pstmt.executeQuery();
```

**Navigation Methods:**

| Method | Return Type | Description | Example |
|--------|-------------|-------------|---------|
| `next()` | `boolean` | Move to next row | `while (rs.next()) { ... }` |
| `previous()` | `boolean` | Move to previous row | `rs.previous()` |
| `first()` | `boolean` | Move to first row | `rs.first()` |
| `last()` | `boolean` | Move to last row | `rs.last()` |
| `absolute(int row)` | `boolean` | Move to specific row | `rs.absolute(5)` |
| `beforeFirst()` | `void` | Move before first row | `rs.beforeFirst()` |
| `afterLast()` | `void` | Move after last row | `rs.afterLast()` |
| `isFirst()` | `boolean` | Check if first row | `if (rs.isFirst())` |
| `isLast()` | `boolean` | Check if last row | `if (rs.isLast())` |

**Data Retrieval Methods (by Column Name):**

| Method | Return Type | Example |
|--------|-------------|---------|
| `getString(String columnName)` | `String` | `rs.getString("username")` |
| `getInt(String columnName)` | `int` | `rs.getInt("age")` |
| `getLong(String columnName)` | `long` | `rs.getLong("id")` |
| `getDouble(String columnName)` | `double` | `rs.getDouble("salary")` |
| `getBoolean(String columnName)` | `boolean` | `rs.getBoolean("active")` |
| `getDate(String columnName)` | `Date` | `rs.getDate("created_at")` |
| `getTimestamp(String columnName)` | `Timestamp` | `rs.getTimestamp("updated_at")` |
| `getBigDecimal(String columnName)` | `BigDecimal` | `rs.getBigDecimal("amount")` |
| `getBytes(String columnName)` | `byte[]` | `rs.getBytes("image")` |
| `getObject(String columnName)` | `Object` | `rs.getObject("data")` |

**Data Retrieval Methods (by Column Index - starts at 1!):**

```java
// ✅ By column name (RECOMMENDED - more readable)
String username = rs.getString("username");

// Also valid - by column index (starts at 1, not 0!)
String username = rs.getString(1);  // First column
```

**Complete Example:**

```java
String sql = "SELECT id, username, email, age, active, created_at FROM users WHERE age > ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setInt(1, 18);

ResultSet rs = pstmt.executeQuery();

// Process all rows
while (rs.next()) {
    long id = rs.getLong("id");
    String username = rs.getString("username");
    String email = rs.getString("email");
    int age = rs.getInt("age");
    boolean active = rs.getBoolean("active");
    Timestamp createdAt = rs.getTimestamp("created_at");
    
    System.out.println("User: " + username + ", Age: " + age + ", Active: " + active);
}

rs.close();
pstmt.close();
```

**Handling NULL values:**
```java
int age = rs.getInt("age");
if (rs.wasNull()) {
    System.out.println("Age is NULL");
}
```

**ResultSet Metadata:**
```java
ResultSetMetaData metaData = rs.getMetaData();

int columnCount = metaData.getColumnCount();
for (int i = 1; i <= columnCount; i++) {
    String columnName = metaData.getColumnName(i);
    String columnType = metaData.getColumnTypeName(i);
    System.out.println("Column " + i + ": " + columnName + " (" + columnType + ")");
}
```

---

#### 📌 7. Method Comparison: execute() vs executeQuery() vs executeUpdate()

**Most Confusing Topic - Let's Clear It Up!**

| Method | Use For | Returns | Example SQL |
|--------|---------|---------|-------------|
| `executeQuery()` | **SELECT** queries only | `ResultSet` | `SELECT * FROM users` |
| `executeUpdate()` | **INSERT/UPDATE/DELETE** | `int` (rows affected) | `INSERT INTO users ...` |
| `execute()` | **Any SQL** (when unknown) | `boolean` | Any SQL statement |

**Detailed Explanation:**

**1️⃣ executeQuery() - For SELECT statements**
```java
// ✅ Correct usage
ResultSet rs = stmt.executeQuery("SELECT * FROM users");

// ❌ Wrong - throws SQLException
stmt.executeQuery("INSERT INTO users ...");  // Error: Not a SELECT query!
```

**2️⃣ executeUpdate() - For INSERT/UPDATE/DELETE**
```java
// ✅ Correct usage for INSERT
int rowsInserted = stmt.executeUpdate("INSERT INTO users (username) VALUES ('john')");
System.out.println(rowsInserted + " row(s) inserted");  // Output: 1 row(s) inserted

// ✅ Correct usage for UPDATE
int rowsUpdated = stmt.executeUpdate("UPDATE users SET age = 30 WHERE id = 1");
System.out.println(rowsUpdated + " row(s) updated");  // Output: 1 row(s) updated

// ✅ Correct usage for DELETE
int rowsDeleted = stmt.executeUpdate("DELETE FROM users WHERE id = 1");
System.out.println(rowsDeleted + " row(s) deleted");  // Output: 1 row(s) deleted

// ❌ Wrong - throws SQLException
stmt.executeUpdate("SELECT * FROM users");  // Error: Not an UPDATE query!
```

**3️⃣ execute() - For any SQL (flexible but less common)**
```java
boolean hasResultSet = stmt.execute("SELECT * FROM users");

if (hasResultSet) {
    // It was a SELECT query
    ResultSet rs = stmt.getResultSet();
    while (rs.next()) {
        System.out.println(rs.getString("username"));
    }
} else {
    // It was INSERT/UPDATE/DELETE
    int updateCount = stmt.getUpdateCount();
    System.out.println(updateCount + " row(s) affected");
}
```

**When to Use Which?**

```java
// ✅ Use executeQuery() when you KNOW it's a SELECT
ResultSet rs = pstmt.executeQuery();

// ✅ Use executeUpdate() when you KNOW it's INSERT/UPDATE/DELETE
int count = pstmt.executeUpdate();

// ✅ Use execute() when SQL type is UNKNOWN at compile time
String sql = getUserInput();  // Could be SELECT or INSERT
boolean isResultSet = stmt.execute(sql);
```

---

#### 📌 8. Important Best Practices ⭐

**1. Always Use PreparedStatement (Not Statement)**
```java
// ❌ Bad - SQL injection vulnerable
Statement stmt = conn.createStatement();
stmt.executeQuery("SELECT * FROM users WHERE id = " + userId);

// ✅ Good - SQL injection safe
PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
pstmt.setInt(1, userId);
pstmt.executeQuery();
```

**2. Always Close Resources (Use try-with-resources)**
```java
// ❌ Bad - Resources might not close
Connection conn = DriverManager.getConnection(url, user, pass);
PreparedStatement pstmt = conn.prepareStatement(sql);
ResultSet rs = pstmt.executeQuery();
// If exception occurs, resources leak!

// ✅ Good - Auto-closes resources
try (Connection conn = DriverManager.getConnection(url, user, pass);
     PreparedStatement pstmt = conn.prepareStatement(sql);
     ResultSet rs = pstmt.executeQuery()) {
    
    while (rs.next()) {
        // Process data
    }
} catch (SQLException e) {
    e.printStackTrace();
}
// Resources automatically closed!
```

**3. Use Batch Processing for Multiple Operations**
```java
// ❌ Bad - One query per insert (slow!)
for (int i = 0; i < 1000; i++) {
    PreparedStatement pstmt = conn.prepareStatement("INSERT INTO users ...");
    pstmt.setString(1, "user" + i);
    pstmt.executeUpdate();  // 1000 database calls!
}

// ✅ Good - Batch processing (fast!)
PreparedStatement pstmt = conn.prepareStatement("INSERT INTO users ...");
for (int i = 0; i < 1000; i++) {
    pstmt.setString(1, "user" + i);
    pstmt.addBatch();
}
pstmt.executeBatch();  // 1 database call!
```

**4. Handle Transactions Properly**
```java
Connection conn = null;
try {
    conn = DriverManager.getConnection(url, user, pass);
    conn.setAutoCommit(false);  // Start transaction
    
    // Multiple operations
    pstmt1.executeUpdate();
    pstmt2.executeUpdate();
    
    conn.commit();  // Commit if all succeed
} catch (SQLException e) {
    if (conn != null) {
        conn.rollback();  // Rollback on error
    }
} finally {
    if (conn != null) {
        conn.setAutoCommit(true);  // Restore default
        conn.close();
    }
}
```

---

#### 📌 9. Quick Reference Table

| Task | Method | Example |
|------|--------|---------|
| **Connect to DB** | `DriverManager.getConnection()` | `Connection conn = DriverManager.getConnection(url, user, pass)` |
| **Create simple statement** | `conn.createStatement()` | `Statement stmt = conn.createStatement()` |
| **Create prepared statement** | `conn.prepareStatement()` | `PreparedStatement pstmt = conn.prepareStatement(sql)` |
| **Execute SELECT** | `executeQuery()` | `ResultSet rs = pstmt.executeQuery()` |
| **Execute INSERT/UPDATE/DELETE** | `executeUpdate()` | `int count = pstmt.executeUpdate()` |
| **Execute any SQL** | `execute()` | `boolean hasRS = stmt.execute(sql)` |
| **Set parameter** | `setXxx(index, value)` | `pstmt.setString(1, "john")` |
| **Get data from ResultSet** | `getXxx(columnName)` | `rs.getString("username")` |
| **Move to next row** | `next()` | `while (rs.next()) { ... }` |
| **Close resources** | `close()` | `rs.close(); pstmt.close(); conn.close()` |
| **Batch processing** | `addBatch()` + `executeBatch()` | `pstmt.addBatch(); pstmt.executeBatch()` |
| **Transaction commit** | `commit()` | `conn.commit()` |
| **Transaction rollback** | `rollback()` | `conn.rollback()` |

---

#### 📌 10. Interview Tips 🎯

**Must Remember:**
1. **PreparedStatement > Statement** (always prefer PreparedStatement!)
2. **executeQuery()** = SELECT (returns ResultSet)
3. **executeUpdate()** = INSERT/UPDATE/DELETE (returns int)
4. **execute()** = Any SQL (returns boolean)
5. **Always close resources** (use try-with-resources)
6. **Use batch processing** for multiple operations
7. **Set parameters using setXxx()** methods (never concatenate strings!)
8. **ResultSet starts at 1** (not 0!)

**Common Interview Questions:**
- Q: Difference between Statement and PreparedStatement?
  - A: PreparedStatement is precompiled, SQL injection safe, and faster for repeated execution
- Q: What does executeUpdate() return?
  - A: Number of rows affected (int)
- Q: What does executeQuery() return?
  - A: ResultSet containing query results
- Q: Why use PreparedStatement?
  - A: Prevents SQL injection, better performance, type-safe

---

### 🔹 JDBC in Core Java (Traditional Approach)

**Step-by-Step Implementation:**

**Step 1: Add JDBC Driver Dependency**

```xml
<!-- Maven dependency for MySQL -->
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <version>8.0.33</version>
</dependency>
```

**Step 2: Load and Register Driver (Optional in JDBC 4.0+)**

```java
// Old way (JDBC 3.0 and earlier) - Not needed anymore
Class.forName("com.mysql.cj.jdbc.Driver");

// JDBC 4.0+ automatically loads drivers from classpath
```

**Step 3: Establish Connection**

```java
String url = "jdbc:mysql://localhost:3306/mydb";
String username = "root";
String password = "secret";

Connection connection = DriverManager.getConnection(url, username, password);
```

**Step 4-7: Complete CRUD Example**

```java
package com.example.jdbc;

import java.sql.*;

public class UserDAO {
    
    // Database connection parameters
    private static final String URL = "jdbc:mysql://localhost:3306/mydb";
    private static final String USER = "root";
    private static final String PASSWORD = "secret";
    
    // 1️⃣ CREATE - Insert User
    public void createUser(String username, String email, String password) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        
        try {
            // Step 1: Get Connection
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            
            // Step 2: Create PreparedStatement
            String sql = "INSERT INTO users (username, email, password) VALUES (?, ?, ?)";
            pstmt = conn.prepareStatement(sql);
            
            // Step 3: Set Parameters
            pstmt.setString(1, username);
            pstmt.setString(2, email);
            pstmt.setString(3, password);
            
            // Step 4: Execute Query
            int rowsAffected = pstmt.executeUpdate();
            
            System.out.println(rowsAffected + " row(s) inserted.");
            
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            // Step 5: Close Resources (IMPORTANT!)
            try {
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    
    // 2️⃣ READ - Find User by ID
    public User findUserById(int id) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        User user = null;
        
        try {
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            
            String sql = "SELECT * FROM users WHERE id = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, id);
            
            // Step 4: Execute Query and Get ResultSet
            rs = pstmt.executeQuery();
            
            // Step 5: Process ResultSet
            if (rs.next()) {
                user = new User();
                user.setId(rs.getInt("id"));
                user.setUsername(rs.getString("username"));
                user.setEmail(rs.getString("email"));
                user.setPassword(rs.getString("password"));
            }
            
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if (rs != null) rs.close();
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        
        return user;
    }
    
    // 3️⃣ READ - Get All Users
    public List<User> findAllUsers() {
        Connection conn = null;
        Statement stmt = null;
        ResultSet rs = null;
        List<User> users = new ArrayList<>();
        
        try {
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            stmt = conn.createStatement();
            
            String sql = "SELECT * FROM users";
            rs = stmt.executeQuery(sql);
            
            while (rs.next()) {
                User user = new User();
                user.setId(rs.getInt("id"));
                user.setUsername(rs.getString("username"));
                user.setEmail(rs.getString("email"));
                user.setPassword(rs.getString("password"));
                users.add(user);
            }
            
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if (rs != null) rs.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
        
        return users;
    }
    
    // 4️⃣ UPDATE - Update User
    public void updateUser(int id, String username, String email) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        
        try {
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            
            String sql = "UPDATE users SET username = ?, email = ? WHERE id = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setString(1, username);
            pstmt.setString(2, email);
            pstmt.setInt(3, id);
            
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) updated.");
            
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    
    // 5️⃣ DELETE - Delete User
    public void deleteUser(int id) {
        Connection conn = null;
        PreparedStatement pstmt = null;
        
        try {
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            
            String sql = "DELETE FROM users WHERE id = ?";
            pstmt = conn.prepareStatement(sql);
            pstmt.setInt(1, id);
            
            int rowsAffected = pstmt.executeUpdate();
            System.out.println(rowsAffected + " row(s) deleted.");
            
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                if (pstmt != null) pstmt.close();
                if (conn != null) conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    
    // Main method to test
    public static void main(String[] args) {
        UserDAO dao = new UserDAO();
        
        // Create
        dao.createUser("john_doe", "john@example.com", "password123");
        
        // Read
        User user = dao.findUserById(1);
        System.out.println("Found user: " + user.getUsername());
        
        // Update
        dao.updateUser(1, "john_updated", "john_new@example.com");
        
        // Delete
        dao.deleteUser(1);
    }
}

// User Model Class
class User {
    private int id;
    private String username;
    private String email;
    private String password;
    
    // Getters and Setters
    public int getId() { return id; }
    public void setId(int id) { this.id = id; }
    
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
}
```

**7 Steps of JDBC (Traditional):**
1. **Load Driver** (automatic in JDBC 4.0+)
2. **Create Connection** - `DriverManager.getConnection()`
3. **Create Statement** - `conn.createStatement()` or `conn.prepareStatement()`
4. **Execute Query** - `executeQuery()` (SELECT) or `executeUpdate()` (INSERT/UPDATE/DELETE)
5. **Process ResultSet** - `rs.next()`, `rs.getString()`, etc.
6. **Close ResultSet** - `rs.close()`
7. **Close Statement & Connection** - `pstmt.close()`, `conn.close()`

**Problems with Traditional JDBC:**
1. ❌ **Boilerplate Code** - Too much repetitive code
2. ❌ **Resource Management** - Manual closing of connections (risk of leaks)
3. ❌ **Exception Handling** - Verbose try-catch-finally blocks
4. ❌ **SQL Injection Risk** - If not using PreparedStatement properly
5. ❌ **No Connection Pooling** - Creates new connection every time (slow!)
6. ❌ **Transaction Management** - Manual commit/rollback

---

### 🔹 JDBC in Spring Boot (JdbcTemplate)

Spring Boot provides **JdbcTemplate** - a powerful wrapper around traditional JDBC that eliminates most boilerplate code.

**Configuration:**

```xml
<!-- Maven Dependencies -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>
```

```properties
# application.properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

**Complete Implementation:**

```java
package com.example.repository;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;
import org.springframework.stereotype.Repository;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

@Repository
public class UserRepository {
    
    private final JdbcTemplate jdbcTemplate;
    
    // Constructor Injection (Recommended)
    public UserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    
    // 1️⃣ CREATE - Insert User
    public int createUser(User user) {
        String sql = "INSERT INTO users (username, email, password) VALUES (?, ?, ?)";
        return jdbcTemplate.update(sql, 
                user.getUsername(), 
                user.getEmail(), 
                user.getPassword());
    }
    
    // 2️⃣ READ - Find User by ID
    public User findById(Long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, this::mapRowToUser, id);
    }
    
    // 3️⃣ READ - Find All Users
    public List<User> findAll() {
        String sql = "SELECT * FROM users";
        return jdbcTemplate.query(sql, this::mapRowToUser);
    }
    
    // 4️⃣ READ - Find by Username (returns Optional)
    public User findByUsername(String username) {
        String sql = "SELECT * FROM users WHERE username = ?";
        List<User> users = jdbcTemplate.query(sql, this::mapRowToUser, username);
        return users.isEmpty() ? null : users.get(0);
    }
    
    // 5️⃣ UPDATE - Update User
    public int updateUser(User user) {
        String sql = "UPDATE users SET username = ?, email = ? WHERE id = ?";
        return jdbcTemplate.update(sql, 
                user.getUsername(), 
                user.getEmail(), 
                user.getId());
    }
    
    // 6️⃣ DELETE - Delete User by ID
    public int deleteById(Long id) {
        String sql = "DELETE FROM users WHERE id = ?";
        return jdbcTemplate.update(sql, id);
    }
    
    // 7️⃣ COUNT - Count all users
    public int countUsers() {
        String sql = "SELECT COUNT(*) FROM users";
        return jdbcTemplate.queryForObject(sql, Integer.class);
    }
    
    // 8️⃣ EXISTS - Check if user exists
    public boolean existsByEmail(String email) {
        String sql = "SELECT COUNT(*) FROM users WHERE email = ?";
        Integer count = jdbcTemplate.queryForObject(sql, Integer.class, email);
        return count != null && count > 0;
    }
    
    // 9️⃣ BATCH INSERT - Insert multiple users
    public int[] batchInsert(List<User> users) {
        String sql = "INSERT INTO users (username, email, password) VALUES (?, ?, ?)";
        return jdbcTemplate.batchUpdate(sql, users, users.size(),
                (ps, user) -> {
                    ps.setString(1, user.getUsername());
                    ps.setString(2, user.getEmail());
                    ps.setString(3, user.getPassword());
                });
    }
    
    // RowMapper - Maps ResultSet to User object
    private User mapRowToUser(ResultSet rs, int rowNum) throws SQLException {
        User user = new User();
        user.setId(rs.getLong("id"));
        user.setUsername(rs.getString("username"));
        user.setEmail(rs.getString("email"));
        user.setPassword(rs.getString("password"));
        return user;
    }
    
    // Alternative: Using RowMapper as separate class
    private static class UserRowMapper implements RowMapper<User> {
        @Override
        public User mapRow(ResultSet rs, int rowNum) throws SQLException {
            User user = new User();
            user.setId(rs.getLong("id"));
            user.setUsername(rs.getString("username"));
            user.setEmail(rs.getString("email"));
            user.setPassword(rs.getString("password"));
            return user;
        }
    }
}
```

**Service Layer:**

```java
package com.example.service;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

@Service
public class UserService {
    
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Transactional
    public User createUser(User user) {
        userRepository.createUser(user);
        return user;
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id);
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    @Transactional
    public void updateUser(User user) {
        userRepository.updateUser(user);
    }
    
    @Transactional
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
```

---

### 🔹 Core Java JDBC vs Spring Boot JDBC

| **Aspect** | **Core Java JDBC** | **Spring Boot JdbcTemplate** |
|------------|-------------------|------------------------------|
| **Boilerplate Code** | ❌ Lots of repetitive code | ✅ Minimal code (no boilerplate) |
| **Connection Management** | ❌ Manual `DriverManager.getConnection()` | ✅ Auto-managed (Spring handles it) |
| **Resource Cleanup** | ❌ Manual `close()` in finally block | ✅ Automatic cleanup |
| **Exception Handling** | ❌ Checked `SQLException` everywhere | ✅ Unchecked `DataAccessException` |
| **Connection Pooling** | ❌ No built-in pooling (create each time) | ✅ Built-in HikariCP pool |
| **Transaction Management** | ❌ Manual commit/rollback | ✅ Declarative `@Transactional` |
| **SQL Injection Safety** | ⚠️ Must use PreparedStatement | ✅ PreparedStatement by default |
| **Code Lines (CRUD)** | ~200 lines | ~50 lines (75% less!) |
| **Configuration** | ❌ Hardcoded connection params | ✅ Externalized (application.properties) |
| **Testing** | ❌ Difficult to mock | ✅ Easy to mock/test |
| **Production Ready** | ❌ Not recommended | ✅ Production ready |

**Code Comparison:**

**Traditional JDBC (15+ lines):**
```java
Connection conn = null;
PreparedStatement pstmt = null;
ResultSet rs = null;
try {
    conn = DriverManager.getConnection(URL, USER, PASSWORD);
    pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
    pstmt.setInt(1, id);
    rs = pstmt.executeQuery();
    if (rs.next()) {
        // map result
    }
} catch (SQLException e) {
    e.printStackTrace();
} finally {
    try {
        if (rs != null) rs.close();
        if (pstmt != null) pstmt.close();
        if (conn != null) conn.close();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

**Spring JdbcTemplate (1 line!):**
```java
return jdbcTemplate.queryForObject("SELECT * FROM users WHERE id = ?", this::mapRowToUser, id);
```

---

### 🔹 Connection Pooling

**Problem Without Connection Pooling:**
- Creating a new database connection is **expensive** (network overhead, authentication, etc.)
- Opening/closing connections for every request = **slow performance**
- Database has **limited connections** (e.g., MySQL default: 151 connections)

**Solution: Connection Pool**

A connection pool maintains a cache of database connections that can be reused.

**HikariCP (Default in Spring Boot):**

```properties
# Connection Pool Configuration
spring.datasource.hikari.maximum-pool-size=10          # Max connections
spring.datasource.hikari.minimum-idle=5                # Min idle connections
spring.datasource.hikari.connection-timeout=30000      # Wait 30s for connection
spring.datasource.hikari.idle-timeout=600000           # Remove idle after 10min
spring.datasource.hikari.max-lifetime=1800000          # Max connection life: 30min
spring.datasource.hikari.pool-name=MyHikariCP
```

**How It Works:**
```
Request 1 arrives → Get connection from pool → Execute query → Return to pool
Request 2 arrives → Reuse same connection → Execute query → Return to pool
Request 3 arrives → Get another connection → Execute query → Return to pool
```

**Benefits:**
- ✅ **Reuses connections** (avoids overhead)
- ✅ **Faster performance** (no connection creation delay)
- ✅ **Controls max connections** (prevents DB overload)
- ✅ **Handles failures** (removes bad connections)
- ✅ **Thread-safe** (multiple threads can use pool safely)

**Interview Tip:** Always mention HikariCP when discussing Spring Boot JDBC!

---

### 🔹 Key Interview Points

1. **JDBC** = Java Database Connectivity API (standard way to connect to databases)
2. **7 Steps of JDBC**: Load Driver → Connection → Statement → Execute → Process ResultSet → Close
3. **PreparedStatement** is preferred over Statement (prevents SQL injection)
4. **JdbcTemplate** eliminates boilerplate code and manages resources automatically
5. **Connection Pooling** (HikariCP) reuses connections for better performance
6. **Spring Boot auto-configures** JdbcTemplate and DataSource based on properties
7. **Most projects use Spring Data JPA** (not JdbcTemplate) - but JDBC is the foundation!

---

### 🔹 Using H2 Database with JDBC

**H2 Database** is a lightweight, fast, open-source Java SQL database that can be used with both Core Java JDBC and Spring Boot. It's perfect for development, testing, and learning.

**Why H2?**
- ✅ **Embedded** - Runs in-memory or file-based (no separate server needed)
- ✅ **Fast** - Extremely fast for development/testing
- ✅ **Zero Configuration** - Just add dependency and go
- ✅ **Small** - Only ~2.5 MB JAR file
- ✅ **Standard SQL** - Supports most SQL features
- ✅ **Web Console** - Built-in database browser UI

**H2 Modes:**
1. **In-Memory Mode** - Data lost when application stops (fastest, for testing)
2. **File-Based Mode** - Data persists to disk
3. **Server Mode** - Standalone server (multiple applications can connect)

---

#### 📌 H2 with Core Java JDBC

**Step 1: Add H2 Dependency**

```xml
<!-- Maven -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <version>2.2.224</version>
</dependency>
```

**Step 2: Complete Working Example**

```java
package com.example.h2jdbc;

import java.sql.*;

public class H2DatabaseExample {
    
    // H2 In-Memory Database URL
    private static final String URL = "jdbc:h2:mem:testdb";
    private static final String USER = "sa";
    private static final String PASSWORD = "";
    
    // H2 File-Based Database (data persists)
    // private static final String URL = "jdbc:h2:file:./data/testdb";
    
    public static void main(String[] args) {
        
        Connection conn = null;
        Statement stmt = null;
        PreparedStatement pstmt = null;
        ResultSet rs = null;
        
        try {
            // Step 1: Get Connection (H2 driver auto-loaded in JDBC 4.0+)
            conn = DriverManager.getConnection(URL, USER, PASSWORD);
            System.out.println("✅ Connected to H2 Database!");
            
            // Step 2: Create Table
            stmt = conn.createStatement();
            String createTableSQL = """
                CREATE TABLE IF NOT EXISTS users (
                    id INT AUTO_INCREMENT PRIMARY KEY,
                    username VARCHAR(50) NOT NULL,
                    email VARCHAR(100) NOT NULL,
                    age INT
                )
            """;
            stmt.execute(createTableSQL);
            System.out.println("✅ Table 'users' created!");
            
            // Step 3: Insert Data
            String insertSQL = "INSERT INTO users (username, email, age) VALUES (?, ?, ?)";
            pstmt = conn.prepareStatement(insertSQL);
            
            // Insert multiple users
            String[][] users = {
                {"john_doe", "john@example.com", "25"},
                {"jane_smith", "jane@example.com", "30"},
                {"bob_wilson", "bob@example.com", "28"}
            };
            
            for (String[] user : users) {
                pstmt.setString(1, user[0]);
                pstmt.setString(2, user[1]);
                pstmt.setInt(3, Integer.parseInt(user[2]));
                pstmt.executeUpdate();
            }
            System.out.println("✅ " + users.length + " users inserted!");
            
            // Step 4: Query Data
            String selectSQL = "SELECT * FROM users WHERE age > ?";
            pstmt = conn.prepareStatement(selectSQL);
            pstmt.setInt(1, 26);
            rs = pstmt.executeQuery();
            
            System.out.println("\n📋 Users with age > 26:");
            System.out.println("─────────────────────────────────────────");
            while (rs.next()) {
                int id = rs.getInt("id");
                String username = rs.getString("username");
                String email = rs.getString("email");
                int age = rs.getInt("age");
                System.out.printf("ID: %d | Username: %s | Email: %s | Age: %d%n", 
                                  id, username, email, age);
            }
            
            // Step 5: Update Data
            String updateSQL = "UPDATE users SET age = ? WHERE username = ?";
            pstmt = conn.prepareStatement(updateSQL);
            pstmt.setInt(1, 31);
            pstmt.setString(2, "jane_smith");
            int updated = pstmt.executeUpdate();
            System.out.println("\n✅ " + updated + " user(s) updated!");
            
            // Step 6: Delete Data
            String deleteSQL = "DELETE FROM users WHERE age < ?";
            pstmt = conn.prepareStatement(deleteSQL);
            pstmt.setInt(1, 27);
            int deleted = pstmt.executeUpdate();
            System.out.println("✅ " + deleted + " user(s) deleted!");
            
            // Step 7: Show remaining data
            rs = stmt.executeQuery("SELECT COUNT(*) as total FROM users");
            if (rs.next()) {
                System.out.println("📊 Total users remaining: " + rs.getInt("total"));
            }
            
        } catch (SQLException e) {
            System.err.println("❌ Database Error: " + e.getMessage());
            e.printStackTrace();
        } finally {
            // Close all resources
            try {
                if (rs != null) rs.close();
                if (pstmt != null) pstmt.close();
                if (stmt != null) stmt.close();
                if (conn != null) conn.close();
                System.out.println("\n✅ Connection closed.");
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

**Output:**
```
✅ Connected to H2 Database!
✅ Table 'users' created!
✅ 3 users inserted!

📋 Users with age > 26:
─────────────────────────────────────────
ID: 2 | Username: jane_smith | Email: jane@example.com | Age: 30
ID: 3 | Username: bob_wilson | Email: bob@example.com | Age: 28

✅ 1 user(s) updated!
✅ 1 user(s) deleted!
📊 Total users remaining: 2

✅ Connection closed.
```

---

#### 📌 H2 with Spring Boot JDBC

**Step 1: Add Dependencies**

```xml
<!-- pom.xml -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

**Step 2: Configure application.properties**

```properties
# H2 Database Configuration
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# H2 Console (Web UI at http://localhost:8080/h2-console)
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

# Show SQL queries in console
spring.jpa.show-sql=true
```

**Alternative: File-Based H2 (Data persists across restarts)**
```properties
spring.datasource.url=jdbc:h2:file:./data/testdb
```

**Step 3: Initialize Schema (Optional)**

Create `src/main/resources/schema.sql`:
```sql
-- schema.sql (automatically executed on startup)
CREATE TABLE IF NOT EXISTS users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    age INT
);
```

Create `src/main/resources/data.sql`:
```sql
-- data.sql (sample data)
INSERT INTO users (username, email, age) VALUES ('john_doe', 'john@example.com', 25);
INSERT INTO users (username, email, age) VALUES ('jane_smith', 'jane@example.com', 30);
INSERT INTO users (username, email, age) VALUES ('bob_wilson', 'bob@example.com', 28);
```

**Step 4: Repository with JdbcTemplate**

```java
package com.example.repository;

import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Repository;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

@Repository
public class UserRepository {
    
    private final JdbcTemplate jdbcTemplate;
    
    public UserRepository(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }
    
    public int createUser(String username, String email, int age) {
        String sql = "INSERT INTO users (username, email, age) VALUES (?, ?, ?)";
        return jdbcTemplate.update(sql, username, email, age);
    }
    
    public User findById(Long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, this::mapRowToUser, id);
    }
    
    public List<User> findAll() {
        String sql = "SELECT * FROM users";
        return jdbcTemplate.query(sql, this::mapRowToUser);
    }
    
    public List<User> findByAgeGreaterThan(int age) {
        String sql = "SELECT * FROM users WHERE age > ?";
        return jdbcTemplate.query(sql, this::mapRowToUser, age);
    }
    
    public int updateAge(Long id, int newAge) {
        String sql = "UPDATE users SET age = ? WHERE id = ?";
        return jdbcTemplate.update(sql, newAge, id);
    }
    
    public int deleteById(Long id) {
        String sql = "DELETE FROM users WHERE id = ?";
        return jdbcTemplate.update(sql, id);
    }
    
    private User mapRowToUser(ResultSet rs, int rowNum) throws SQLException {
        User user = new User();
        user.setId(rs.getLong("id"));
        user.setUsername(rs.getString("username"));
        user.setEmail(rs.getString("email"));
        user.setAge(rs.getInt("age"));
        return user;
    }
}
```

**Step 5: Service Layer**

```java
package com.example.service;

import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;

@Service
public class UserService {
    
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Transactional
    public void createUser(String username, String email, int age) {
        userRepository.createUser(username, email, age);
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id);
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public List<User> getUsersByAge(int minAge) {
        return userRepository.findByAgeGreaterThan(minAge);
    }
}
```

**Step 6: REST Controller (Optional)**

```java
package com.example.controller;

import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {
    
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @GetMapping
    public List<User> getAllUsers() {
        return userService.getAllUsers();
    }
    
    @GetMapping("/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.getUserById(id);
    }
    
    @PostMapping
    public String createUser(@RequestBody User user) {
        userService.createUser(user.getUsername(), user.getEmail(), user.getAge());
        return "User created successfully!";
    }
}
```

**Step 7: Test the Application**

```java
package com.example;

import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.Bean;

@SpringBootApplication
public class Application {
    
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
    
    @Bean
    public CommandLineRunner demo(UserService userService) {
        return (args) -> {
            System.out.println("\n📋 All Users:");
            userService.getAllUsers().forEach(user -> 
                System.out.println(user.getUsername() + " - " + user.getEmail())
            );
            
            System.out.println("\n📋 Users with age > 26:");
            userService.getUsersByAge(26).forEach(user -> 
                System.out.println(user.getUsername() + " (Age: " + user.getAge() + ")")
            );
        };
    }
}
```

---

#### 📌 Accessing H2 Console (Spring Boot)

1. **Start your Spring Boot application**
2. **Open browser:** http://localhost:8080/h2-console
3. **Login credentials:**
   - JDBC URL: `jdbc:h2:mem:testdb`
   - User Name: `sa`
   - Password: (leave empty)
4. **Click "Connect"** and you can browse tables, run SQL queries!

**H2 Console Screenshot:**
```
┌──────────────────────────────────────┐
│        H2 Console                    │
├──────────────────────────────────────┤
│ Saved Settings: Generic H2 (Embedded)│
│ Setting Name:   Generic H2 (Embedded)│
│ Driver Class:   org.h2.Driver        │
│ JDBC URL:       jdbc:h2:mem:testdb   │
│ User Name:      sa                   │
│ Password:       [empty]              │
│                                      │
│         [Test Connection] [Connect]  │
└──────────────────────────────────────┘
```

---

#### 📌 H2 Database URL Formats

```properties
# 1. In-Memory (data lost on shutdown) - FASTEST
spring.datasource.url=jdbc:h2:mem:testdb

# 2. File-Based (data persists)
spring.datasource.url=jdbc:h2:file:./data/testdb

# 3. File-Based with absolute path
spring.datasource.url=jdbc:h2:file:C:/Users/data/testdb

# 4. TCP Server Mode (multiple connections)
spring.datasource.url=jdbc:h2:tcp://localhost/~/testdb

# 5. In-Memory with MySQL compatibility mode
spring.datasource.url=jdbc:h2:mem:testdb;MODE=MySQL

# 6. In-Memory with options
spring.datasource.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

**Common H2 Options:**
- `DB_CLOSE_DELAY=-1` - Keep in-memory DB alive even when no connections
- `MODE=MySQL` - MySQL compatibility mode
- `MODE=PostgreSQL` - PostgreSQL compatibility mode
- `TRACE_LEVEL_SYSTEM_OUT=2` - Enable SQL logging

---

#### 📌 Core Java vs Spring Boot with H2

| **Aspect** | **Core Java JDBC + H2** | **Spring Boot + H2** |
|------------|------------------------|----------------------|
| **Setup** | Manual dependency, manual connection | Auto-configured by Spring Boot |
| **Connection** | Manual `DriverManager.getConnection()` | Automatic via `JdbcTemplate` |
| **Schema Creation** | Manual CREATE TABLE in code | Auto-execute `schema.sql` file |
| **Data Initialization** | Manual INSERT statements | Auto-execute `data.sql` file |
| **Web Console** | Need to start manually | Built-in at `/h2-console` |
| **Code Lines** | ~50 lines for simple CRUD | ~10 lines for simple CRUD |
| **Best For** | Learning JDBC basics | Production-ready apps |

---

#### 📌 When to Use H2?

**✅ Use H2 For:**
- Unit/Integration testing (fast, isolated)
- Local development (no DB server needed)
- Proof of concepts (quick setup)
- Learning JDBC/JPA
- CI/CD pipelines (automated testing)

**❌ Don't Use H2 For:**
- Production applications (use MySQL, PostgreSQL, etc.)
- Large datasets (limited performance compared to enterprise DBs)
- High concurrency scenarios

---

#### 📌 Quick Start Comparison

**Core Java JDBC:**
```java
// 1. Add H2 dependency
// 2. Create connection
Connection conn = DriverManager.getConnection("jdbc:h2:mem:testdb", "sa", "");
// 3. Execute SQL manually
Statement stmt = conn.createStatement();
stmt.execute("CREATE TABLE users (...)");
// 4. Close everything manually
```

**Spring Boot:**
```properties
# 1. Add spring-boot-starter-jdbc + h2 dependencies
# 2. Configure in application.properties
spring.datasource.url=jdbc:h2:mem:testdb
spring.h2.console.enabled=true
# 3. Create schema.sql and data.sql (optional)
# 4. Use JdbcTemplate - Spring handles everything!
```

**Winner:** Spring Boot (90% less code, production-ready!)

---

## 5.2 Spring Data JPA ⭐ CRITICAL

### 🔹 What is Spring Data JPA? (Complete Beginner's Guide)

**Spring Data JPA** is a framework that makes it incredibly easy to implement JPA-based (Java Persistence API) data access layers in Spring applications. Let's understand this step by step:

---

#### 📌 Understanding the Hierarchy: From Database to Spring Data JPA

```
DATABASE LAYER
     ↓
JDBC (Java Database Connectivity) - Low-level database connection API
     ↓
JPA (Java Persistence API) - Specification for ORM in Java
     ↓
HIBERNATE - Most popular JPA implementation
     ↓
SPRING DATA JPA - Abstraction on top of JPA/Hibernate
```

**Let's break each layer down:**

---

#### 1️⃣ **JDBC (Java Database Connectivity)**

**What it is:** Low-level API to connect Java applications to databases using SQL.

**Problem with JDBC:**
```java
// Traditional JDBC - Too much boilerplate code!
Connection conn = null;
PreparedStatement pstmt = null;
ResultSet rs = null;
try {
    conn = DriverManager.getConnection(url, user, password);
    pstmt = conn.prepareStatement("SELECT * FROM users WHERE id = ?");
    pstmt.setInt(1, id);
    rs = pstmt.executeQuery();
    if (rs.next()) {
        User user = new User();
        user.setId(rs.getInt("id"));
        user.setUsername(rs.getString("username"));
        // ... manual mapping
    }
} catch (SQLException e) {
    e.printStackTrace();
} finally {
    // Manual cleanup of resources
    try {
        if (rs != null) rs.close();
        if (pstmt != null) pstmt.close();
        if (conn != null) conn.close();
    } catch (SQLException e) {
        e.printStackTrace();
    }
}
```

**Issues:**
- ❌ Too much boilerplate code
- ❌ Manual resource management
- ❌ Manual object-to-database mapping
- ❌ SQL queries are strings (no compile-time checking)
- ❌ Difficult to maintain

---

#### 2️⃣ **ORM (Object-Relational Mapping)**

**What it is:** A technique that maps Java objects (entities) to database tables automatically.

**The Problem ORM Solves:**

```
Java World               Database World
───────────             ─────────────────
User.java        ←→     users table
  - id                    - id
  - username              - username
  - email                 - email
  
Order.java       ←→     orders table
  - id                    - id
  - userId                - user_id
  - amount                - amount
```

**Without ORM:**
```java
// Manual mapping - tedious and error-prone
User user = new User();
user.setId(rs.getInt("id"));
user.setUsername(rs.getString("username"));
user.setEmail(rs.getString("email"));
```

**With ORM:**
```java
// Automatic mapping - ORM framework does it!
User user = session.get(User.class, id);  // That's it!
```

---

#### 3️⃣ **JPA (Java Persistence API)**

**What it is:** A **specification** (not implementation) that defines how Java objects should be persisted to databases.

**Key Point:** JPA is just a set of interfaces and annotations. It defines:
- How to mark a class as entity (`@Entity`)
- How to map fields to columns (`@Column`)
- How to define relationships (`@OneToMany`, `@ManyToOne`, etc.)
- How to query entities (JPQL - Java Persistence Query Language)

**JPA Specification includes:**
```java
// Standard JPA annotations
@Entity          // Marks class as database entity
@Id              // Marks primary key
@Column          // Maps to database column
@OneToMany       // Defines relationships
@ManyToOne
@Query           // Custom queries
```

**Important:** JPA itself doesn't do anything - you need an **implementation**!

---

#### 4️⃣ **Hibernate - JPA Implementation**

**What it is:** The most popular **implementation** of the JPA specification.

**JPA Implementations Available:**
1. **Hibernate** (Most popular - 95% market share)
2. EclipseLink (Oracle's reference implementation)
3. OpenJPA (Apache)

**Analogy:**
```
JPA = Blueprint/Contract (Interface)
Hibernate = Actual Building (Implementation)

Like:
JDBC API = Interface (java.sql.*)
MySQL Driver = Implementation for MySQL
Oracle Driver = Implementation for Oracle
```

**What Hibernate Does:**
```java
@Entity
public class User {
    @Id
    private Long id;
    private String username;
}

// Hibernate automatically:
// 1. Creates/updates table schema
// 2. Converts Java objects to SQL INSERT/UPDATE
// 3. Converts SQL ResultSet to Java objects
// 4. Manages database connections
// 5. Caches frequently used data
// 6. Handles transactions
```

---

#### 5️⃣ **Spring Data JPA - The Ultimate Abstraction**

**What it is:** A Spring framework that provides an additional abstraction layer on top of JPA/Hibernate, making database operations extremely simple.

**The Magic of Spring Data JPA:**

Instead of writing this (JPA with Hibernate):
```java
@Repository
public class UserRepositoryImpl {
    
    @PersistenceContext
    private EntityManager entityManager;
    
    public User findById(Long id) {
        return entityManager.find(User.class, id);
    }
    
    public List<User> findAll() {
        return entityManager.createQuery("SELECT u FROM User u", User.class)
                           .getResultList();
    }
    
    public User save(User user) {
        if (user.getId() == null) {
            entityManager.persist(user);
            return user;
        } else {
            return entityManager.merge(user);
        }
    }
    
    public void delete(Long id) {
        User user = findById(id);
        if (user != null) {
            entityManager.remove(user);
        }
    }
    
    public List<User> findByUsername(String username) {
        return entityManager.createQuery(
            "SELECT u FROM User u WHERE u.username = :username", User.class)
            .setParameter("username", username)
            .getResultList();
    }
}
```

**You just write this (Spring Data JPA):**
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // That's it! No implementation needed!
    // Spring Data JPA automatically provides:
    // - save(entity)
    // - findById(id)
    // - findAll()
    // - deleteById(id)
    // - count()
    // - existsById(id)
    
    // Just declare method names - implementation is automatic!
    List<User> findByUsername(String username);
    List<User> findByAgeGreaterThan(int age);
    Optional<User> findByEmail(String email);
}
```

**Spring Data JPA automatically generates the implementation at runtime!**

---

#### 📌 Complete Architecture Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                     YOUR APPLICATION                         │
│  ┌────────────────────────────────────────────────────────┐  │
│  │            Service Layer                               │  │
│  │   @Service UserService { ... }                         │  │
│  └────────────────┬───────────────────────────────────────┘  │
│                   │                                          │
│  ┌────────────────▼───────────────────────────────────────┐  │
│  │       Spring Data JPA Repository Interface             │  │
│  │   public interface UserRepository                      │  │
│  │       extends JpaRepository<User, Long> { }            │  │
│  │                                                         │  │
│  │   [NO CODE NEEDED - Auto-generated at runtime!]        │  │
│  └────────────────┬───────────────────────────────────────┘  │
└───────────────────┼──────────────────────────────────────────┘
                    │
┌───────────────────▼──────────────────────────────────────────┐
│              SPRING DATA JPA FRAMEWORK                       │
│  - Generates repository implementation automatically         │
│  - Parses method names (findByUsername → SQL query)          │
│  - Handles transactions                                      │
│  - Provides pagination, sorting                              │
└───────────────────┬──────────────────────────────────────────┘
                    │
┌───────────────────▼──────────────────────────────────────────┐
│                  JPA (Specification)                         │
│  - EntityManager API                                         │
│  - JPQL (Java Persistence Query Language)                    │
│  - @Entity, @Id, @Column annotations                         │
└───────────────────┬──────────────────────────────────────────┘
                    │
┌───────────────────▼──────────────────────────────────────────┐
│              HIBERNATE (JPA Implementation)                  │
│  - Implements JPA specification                              │
│  - Object-Relational Mapping (ORM)                           │
│  - Session/EntityManager management                          │
│  - HQL to SQL translation                                    │
│  - Caching (1st level, 2nd level)                            │
│  - Lazy/Eager loading                                        │
└───────────────────┬──────────────────────────────────────────┘
                    │
┌───────────────────▼──────────────────────────────────────────┐
│                      JDBC                                    │
│  - Database connection management                            │
│  - Executes SQL queries                                      │
│  - Connection pooling (HikariCP)                             │
└───────────────────┬──────────────────────────────────────────┘
                    │
┌───────────────────▼──────────────────────────────────────────┐
│                    DATABASE                                  │
│              (MySQL, PostgreSQL, Oracle, etc.)               │
└──────────────────────────────────────────────────────────────┘
```

---

#### 📌 Evolution: How We Got to Spring Data JPA

**Stage 1: Pure JDBC (2000s)**
```java
// 50+ lines of code for one CRUD operation
Connection conn = DriverManager.getConnection(...);
PreparedStatement pstmt = conn.prepareStatement("INSERT INTO users ...");
// ... lots of boilerplate
```
**Problems:** Too much code, manual resource management, error-prone

---

**Stage 2: Spring JdbcTemplate (2004+)**
```java
// Reduced to 10 lines
jdbcTemplate.update("INSERT INTO users (username, email) VALUES (?, ?)", 
                    username, email);
```
**Better:** Less boilerplate, automatic resource management
**Still a Problem:** Still writing SQL, manual object mapping

---

**Stage 3: JPA + Hibernate (2006+)**
```java
// Object-oriented approach
@Entity
public class User { ... }

EntityManager em = ...;
em.persist(user);  // No SQL needed!
```
**Better:** No SQL, automatic object mapping, database-independent
**Still a Problem:** Need to write repository implementations

---

**Stage 4: Spring Data JPA (2011+)** ⭐ **Current Best Practice**
```java
// Just declare interface - implementation auto-generated!
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByUsername(String username);  // Method name → SQL query!
}
```
**Perfect!**
- ✅ No SQL code
- ✅ No implementation code
- ✅ No boilerplate
- ✅ Type-safe
- ✅ Compile-time checking
- ✅ Built-in pagination, sorting, transactions
- ✅ Automatic query generation from method names

---

#### 📌 Key Benefits of Spring Data JPA

| Feature | Description | Example |
|---------|-------------|---------|
| **No Boilerplate** | No need to write repetitive CRUD code | Just extend `JpaRepository` |
| **Query Generation** | Automatically generates queries from method names | `findByUsername(String username)` |
| **Type Safety** | Compile-time checking of queries | IDE autocomplete support |
| **Pagination** | Built-in pagination support | `findAll(Pageable pageable)` |
| **Sorting** | Built-in sorting support | `findAll(Sort.by("username"))` |
| **Transactions** | Automatic transaction management | `@Transactional` annotation |
| **Auditing** | Automatic created/modified timestamps | `@CreatedDate`, `@LastModifiedDate` |
| **Custom Queries** | Support for complex queries when needed | `@Query("SELECT u FROM User...")` |

---

#### 📌 When to Use What?

| Use Case | Technology | Reason |
|----------|-----------|--------|
| **Standard CRUD operations** | Spring Data JPA | Fastest development, minimal code |
| **Complex queries** | Spring Data JPA with `@Query` | Still type-safe, but more control |
| **Dynamic queries** | Spring Data JPA Specifications | Criteria API for runtime query building |
| **Bulk operations** | Spring Data JPA with `@Modifying` | Efficient batch updates/deletes |
| **Performance-critical** | JdbcTemplate or native SQL | Direct SQL control when needed |
| **Legacy database** | JdbcTemplate | When tables don't map well to objects |

**Rule of Thumb:** Start with Spring Data JPA. Only drop down to lower levels when absolutely necessary!

---

### 🔹 Setup

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
</dependency>
```

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=secret

# JPA Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect
spring.jpa.properties.hibernate.format_sql=true
```

**ddl-auto options:**
- `none` - No changes to DB schema
- `validate` - Validate schema, no changes
- `update` - Update schema (adds missing columns/tables)
- `create` - Drop and recreate schema every time
- `create-drop` - Create on startup, drop on shutdown

**Production:** Use `none` or `validate` + Flyway/Liquibase for migrations!

---

### 🔹 JPA Repositories (Step-by-Step Guide)

#### 📌 Step 1: Understanding the Entity Class

An **Entity** is a Java class that represents a database table. Each instance of the entity represents a row in that table.

```java
@Entity  // ← Tells JPA: "This class maps to a database table"
@Table(name = "users")  // ← Optional: Specify table name (default: class name in lowercase)
public class User {
    
    // PRIMARY KEY
    @Id  // ← Marks this field as the primary key
    @GeneratedValue(strategy = GenerationType.IDENTITY)  // ← Auto-increment in DB
    private Long id;
    
    // REGULAR COLUMNS
    @Column(nullable = false, unique = true, length = 50)  // ← Column constraints
    private String username;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    @Column(nullable = false)
    private String password;
    
    // TIMESTAMP COLUMNS
    @Column(name = "created_at", updatable = false)  // ← DB column name, never update
    private LocalDateTime createdAt;
    
    @Column(name = "updated_at")
    private LocalDateTime updatedAt;
    
    // LIFECYCLE CALLBACKS - Automatic timestamp management
    @PrePersist  // ← Called BEFORE inserting new record
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
    
    @PreUpdate  // ← Called BEFORE updating existing record
    protected void onUpdate() {
        updatedAt = LocalDateTime.now();
    }
    
    // CONSTRUCTORS
    public User() {
        // JPA requires no-arg constructor
    }
    
    public User(String username, String email, String password) {
        this.username = username;
        this.email = email;
        this.password = password;
    }
    
    // GETTERS AND SETTERS (Required by JPA)
    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    
    public String getUsername() { return username; }
    public void setUsername(String username) { this.username = username; }
    
    public String getEmail() { return email; }
    public void setEmail(String email) { this.email = email; }
    
    public String getPassword() { return password; }
    public void setPassword(String password) { this.password = password; }
    
    public LocalDateTime getCreatedAt() { return createdAt; }
    public LocalDateTime getUpdatedAt() { return updatedAt; }
    
    // OPTIONAL: toString() for debugging
    @Override
    public String toString() {
        return "User{id=" + id + ", username='" + username + "', email='" + email + "'}";
    }
}
```

**Important Annotations Explained:**

| Annotation | Purpose | Example |
|------------|---------|---------|
| `@Entity` | Marks class as JPA entity | `@Entity` |
| `@Table(name="...")` | Specifies table name | `@Table(name="users")` |
| `@Id` | Marks primary key field | `@Id private Long id;` |
| `@GeneratedValue` | Auto-generate primary key | `@GeneratedValue(strategy=IDENTITY)` |
| `@Column` | Maps field to column with constraints | `@Column(nullable=false, unique=true)` |
| `@PrePersist` | Callback before INSERT | `@PrePersist void onCreate() {...}` |
| `@PreUpdate` | Callback before UPDATE | `@PreUpdate void onUpdate() {...}` |
| `@Transient` | Field NOT persisted to DB | `@Transient private String temp;` |
| `@Lob` | Large Object (TEXT/BLOB) | `@Lob private String description;` |

---

#### 📌 Step 2: Creating the Repository Interface

**The Magic Interface - No Implementation Needed!**

```java
package com.example.repository;

import com.example.entity.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository  // ← Optional but recommended (marks it as Spring bean)
public interface UserRepository extends JpaRepository<User, Long> {
    //                                                    ↑     ↑
    //                                            Entity type   Primary key type
    
    // NO CODE HERE!
    // Spring Data JPA automatically provides these methods:
    
    // ============ SAVE/UPDATE ============
    // User save(User user)                - Insert or Update
    // List<User> saveAll(List<User> users) - Batch insert/update
    
    // ============ READ/FIND ============
    // Optional<User> findById(Long id)    - Find by primary key
    // List<User> findAll()                - Get all records
    // List<User> findAllById(List<Long> ids) - Find multiple by IDs
    // boolean existsById(Long id)         - Check if exists
    
    // ============ DELETE ============
    // void deleteById(Long id)            - Delete by ID
    // void delete(User user)              - Delete entity
    // void deleteAll()                    - Delete all records
    
    // ============ COUNT ============
    // long count()                        - Count all records
    
    // ============ PAGINATION & SORTING ============
    // Page<User> findAll(Pageable pageable)
    // List<User> findAll(Sort sort)
}
```

---

#### 📌 Step 3: Using the Repository (Service Layer)

```java
package com.example.service;

import com.example.entity.User;
import com.example.repository.UserRepository;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;
import java.util.List;
import java.util.Optional;

@Service  // ← Marks as service layer component
public class UserService {
    
    private final UserRepository userRepository;
    
    // Constructor Injection (Best Practice)
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    // ============ CREATE ============
    @Transactional
    public User createUser(User user) {
        // save() - inserts new record or updates existing (based on ID)
        return userRepository.save(user);
    }
    
    @Transactional
    public List<User> createMultipleUsers(List<User> users) {
        // saveAll() - batch insert/update
        return userRepository.saveAll(users);
    }
    
    // ============ READ ============
    public User getUserById(Long id) {
        // findById() returns Optional<User>
        return userRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("User not found with id: " + id));
    }
    
    public Optional<User> getUserByIdOptional(Long id) {
        // Return Optional - let caller handle absence
        return userRepository.findById(id);
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public List<User> getUsersByIds(List<Long> ids) {
        return userRepository.findAllById(ids);
    }
    
    public boolean userExists(Long id) {
        return userRepository.existsById(id);
    }
    
    public long getTotalUserCount() {
        return userRepository.count();
    }
    
    // ============ UPDATE ============
    @Transactional
    public User updateUser(Long id, User updatedUser) {
        User existingUser = getUserById(id);  // Throws exception if not found
        
        // Update fields
        existingUser.setUsername(updatedUser.getUsername());
        existingUser.setEmail(updatedUser.getEmail());
        
        // save() on existing entity → UPDATE in database
        return userRepository.save(existingUser);
    }
    
    // ============ DELETE ============
    @Transactional
    public void deleteUser(Long id) {
        // deleteById() - throws exception if not found
        userRepository.deleteById(id);
    }
    
    @Transactional
    public void deleteUserIfExists(Long id) {
        if (userRepository.existsById(id)) {
            userRepository.deleteById(id);
        }
    }
    
    @Transactional
    public void deleteAllUsers() {
        userRepository.deleteAll();
    }
}
```

---

#### 📌 Step 4: Using in REST Controller

```java
package com.example.controller;

import com.example.entity.User;
import com.example.service.UserService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/users")
public class UserController {
    
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    // POST /api/users - Create new user
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User createdUser = userService.createUser(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(createdUser);
    }
    
    // GET /api/users - Get all users
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        List<User> users = userService.getAllUsers();
        return ResponseEntity.ok(users);
    }
    
    // GET /api/users/{id} - Get user by ID
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);
    }
    
    // PUT /api/users/{id} - Update user
    @PutMapping("/{id}")
    public ResponseEntity<User> updateUser(@PathVariable Long id, @RequestBody User user) {
        User updatedUser = userService.updateUser(id, user);
        return ResponseEntity.ok(updatedUser);
    }
    
    // DELETE /api/users/{id} - Delete user
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
    
    // GET /api/users/count - Get total count
    @GetMapping("/count")
    public ResponseEntity<Long> getUserCount() {
        long count = userService.getTotalUserCount();
        return ResponseEntity.ok(count);
    }
}
```

---

#### 📌 Complete Flow Example

**Request:** `POST /api/users` with JSON body:
```json
{
  "username": "john_doe",
  "email": "john@example.com",
  "password": "secret123"
}
```

**What Happens Behind the Scenes:**

```
1. Request hits Controller
   ↓
2. Controller calls UserService.createUser(user)
   ↓
3. Service calls UserRepository.save(user)
   ↓
4. Spring Data JPA detects user.id is null (new entity)
   ↓
5. Calls @PrePersist method (sets createdAt)
   ↓
6. Hibernate generates SQL:
   INSERT INTO users (username, email, password, created_at) 
   VALUES ('john_doe', 'john@example.com', 'secret123', '2026-05-23 10:30:00')
   ↓
7. JDBC executes query on database
   ↓
8. Database generates ID (auto-increment) and returns it
   ↓
9. Hibernate sets the ID on the User object
   ↓
10. Returns User object with ID populated
    ↓
11. Service returns to Controller
    ↓
12. Controller returns ResponseEntity with HTTP 201 Created
```

---

#### 📌 Key Methods from JpaRepository

**Inheritance Hierarchy:**
```
JpaRepository (Spring Data JPA)
    ↓ extends
PagingAndSortingRepository
    ↓ extends
CrudRepository
    ↓ extends
Repository (marker interface)
```

**Methods Available:**

| Method | Return Type | Description | Example |
|--------|-------------|-------------|---------|
| `save(S entity)` | `S` | Insert or update | `save(user)` |
| `saveAll(Iterable<S>)` | `List<S>` | Batch save | `saveAll(userList)` |
| `findById(ID)` | `Optional<T>` | Find by primary key | `findById(1L)` |
| `existsById(ID)` | `boolean` | Check existence | `existsById(1L)` |
| `findAll()` | `List<T>` | Get all | `findAll()` |
| `findAllById(Iterable<ID>)` | `List<T>` | Find by IDs | `findAllById(List.of(1L, 2L))` |
| `count()` | `long` | Count all | `count()` |
| `deleteById(ID)` | `void` | Delete by ID | `deleteById(1L)` |
| `delete(T entity)` | `void` | Delete entity | `delete(user)` |
| `deleteAll()` | `void` | Delete all | `deleteAll()` |
| `flush()` | `void` | Sync with DB | `flush()` |
| `saveAndFlush(S)` | `S` | Save and sync immediately | `saveAndFlush(user)` |

---

#### 📌 save() vs saveAndFlush()

**save():**
```java
User user = new User("john", "john@example.com", "pass");
userRepository.save(user);
// Data in persistence context, NOT yet in database
// Will be written to DB at end of transaction or when flush() is called
```

**saveAndFlush():**
```java
User user = new User("john", "john@example.com", "pass");
userRepository.saveAndFlush(user);
// Data IMMEDIATELY written to database
// Use when you need the generated ID right away
```

**When to use saveAndFlush():**
- Need generated ID immediately for another operation
- Need to trigger database constraints validation
- Debugging (see exact SQL execution point)

**When to use save():**
- Normal operations (better performance)
- Let JPA optimize batch inserts
- Let transaction handle commit timing

---

### 🔹 Query Methods (Method Name Parsing) - The Magic! ✨

**One of the most powerful features of Spring Data JPA**: You can create database queries just by **declaring method names**! No implementation code needed!

---

#### 📌 How It Works: Method Name → SQL Query

Spring Data JPA **parses your method name** and automatically generates the SQL query!

```java
// You write this:
List<User> findByUsername(String username);

// Spring Data JPA reads it as:
// "find" → SELECT
// "By" → WHERE
// "Username" → username field
// 
// And generates this SQL:
// SELECT * FROM users WHERE username = ?
```

**The Pattern:**
```
[action][subject]By[property][operator][property]...

Examples:
findByUsername          → SELECT * FROM users WHERE username = ?
deleteByEmail           → DELETE FROM users WHERE email = ?
countByAgeGreaterThan   → SELECT COUNT(*) FROM users WHERE age > ?
existsByUsername        → SELECT EXISTS (SELECT 1 FROM users WHERE username = ?)
```

---

#### 📌 Complete Query Method Reference

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    // ===============================================
    // 1️⃣ BASIC QUERIES - Single Field
    // ===============================================
    
    // Find by exact match
    Optional<User> findByUsername(String username);
    // SQL: SELECT * FROM users WHERE username = ?
    
    Optional<User> findByEmail(String email);
    // SQL: SELECT * FROM users WHERE email = ?
    
    List<User> findByAge(int age);
    // SQL: SELECT * FROM users WHERE age = ?
    
    // ===============================================
    // 2️⃣ MULTIPLE CONDITIONS - AND / OR
    // ===============================================
    
    // AND - Both conditions must be true
    List<User> findByUsernameAndEmail(String username, String email);
    // SQL: SELECT * FROM users WHERE username = ? AND email = ?
    
    List<User> findByUsernameAndAgeAndCity(String username, int age, String city);
    // SQL: SELECT * FROM users WHERE username = ? AND age = ? AND city = ?
    
    // OR - At least one condition must be true
    List<User> findByUsernameOrEmail(String username, String email);
    // SQL: SELECT * FROM users WHERE username = ? OR email = ?
    
    List<User> findByUsernameOrEmailOrPhone(String username, String email, String phone);
    // SQL: SELECT * FROM users WHERE username = ? OR email = ? OR phone = ?
    
    // ===============================================
    // 3️⃣ STRING MATCHING - Like, Contains, Starts, Ends
    // ===============================================
    
    // LIKE - Manual wildcards (you provide % symbols)
    List<User> findByUsernameLike(String pattern);
    // Usage: findByUsernameLike("%john%")
    // SQL: SELECT * FROM users WHERE username LIKE ?
    
    // CONTAINING - Automatic wildcards (Spring adds % on both sides)
    List<User> findByUsernameContaining(String substring);
    // Usage: findByUsernameContaining("john")  → %john%
    // SQL: SELECT * FROM users WHERE username LIKE ?
    
    // STARTING WITH - Wildcard at end
    List<User> findByUsernameStartingWith(String prefix);
    // Usage: findByUsernameStartingWith("john")  → john%
    // SQL: SELECT * FROM users WHERE username LIKE ?
    
    // ENDING WITH - Wildcard at start
    List<User> findByUsernameEndingWith(String suffix);
    // Usage: findByUsernameEndingWith("doe")  → %doe
    // SQL: SELECT * FROM users WHERE username LIKE ?
    
    // NOT LIKE
    List<User> findByUsernameNotLike(String pattern);
    // SQL: SELECT * FROM users WHERE username NOT LIKE ?
    
    // ===============================================
    // 4️⃣ CASE SENSITIVITY - IgnoreCase
    // ===============================================
    
    // Single field ignore case
    Optional<User> findByUsernameIgnoreCase(String username);
    // Usage: findByUsernameIgnoreCase("JOHN")  → matches "john", "John", "JOHN"
    // SQL: SELECT * FROM users WHERE UPPER(username) = UPPER(?)
    
    // Multiple fields ignore case
    List<User> findByUsernameAndEmailAllIgnoreCase(String username, String email);
    // SQL: SELECT * FROM users WHERE UPPER(username) = UPPER(?) AND UPPER(email) = UPPER(?)
    
    // ===============================================
    // 5️⃣ COMPARISON OPERATORS - Greater, Less, Between
    // ===============================================
    
    // GREATER THAN
    List<User> findByAgeGreaterThan(int age);
    // SQL: SELECT * FROM users WHERE age > ?
    
    // GREATER THAN OR EQUAL
    List<User> findByAgeGreaterThanEqual(int age);
    // SQL: SELECT * FROM users WHERE age >= ?
    
    // LESS THAN
    List<User> findByAgeLessThan(int age);
    // SQL: SELECT * FROM users WHERE age < ?
    
    // LESS THAN OR EQUAL
    List<User> findByAgeLessThanEqual(int age);
    // SQL: SELECT * FROM users WHERE age <= ?
    
    // BETWEEN (inclusive)
    List<User> findByAgeBetween(int minAge, int maxAge);
    // SQL: SELECT * FROM users WHERE age BETWEEN ? AND ?
    
    // AFTER (for dates)
    List<User> findByCreatedAtAfter(LocalDateTime date);
    // SQL: SELECT * FROM users WHERE created_at > ?
    
    // BEFORE (for dates)
    List<User> findByCreatedAtBefore(LocalDateTime date);
    // SQL: SELECT * FROM users WHERE created_at < ?
    
    // ===============================================
    // 6️⃣ NULL CHECKS - IsNull, IsNotNull
    // ===============================================
    
    // IS NULL
    List<User> findByEmailIsNull();
    // SQL: SELECT * FROM users WHERE email IS NULL
    
    // IS NOT NULL
    List<User> findByEmailIsNotNull();
    // SQL: SELECT * FROM users WHERE email IS NOT NULL
    
    List<User> findByPhoneNumberIsNull();
    // SQL: SELECT * FROM users WHERE phone_number IS NULL
    
    // ===============================================
    // 7️⃣ IN / NOT IN - Multiple Values
    // ===============================================
    
    // IN - Match any value from list
    List<User> findByIdIn(List<Long> ids);
    // Usage: findByIdIn(List.of(1L, 2L, 3L))
    // SQL: SELECT * FROM users WHERE id IN (?, ?, ?)
    
    List<User> findByUsernameIn(List<String> usernames);
    // SQL: SELECT * FROM users WHERE username IN (?, ?, ?)
    
    // NOT IN - Exclude values from list
    List<User> findByUsernameNotIn(List<String> usernames);
    // SQL: SELECT * FROM users WHERE username NOT IN (?, ?, ?)
    
    List<User> findByAgeIn(List<Integer> ages);
    // SQL: SELECT * FROM users WHERE age IN (?, ?, ?)
    
    // ===============================================
    // 8️⃣ ORDERING - OrderBy (Sorting)
    // ===============================================
    
    // Order by single field - Ascending
    List<User> findByAgeOrderByUsernameAsc(int age);
    // SQL: SELECT * FROM users WHERE age = ? ORDER BY username ASC
    
    // Order by single field - Descending
    List<User> findByAgeOrderByUsernameDesc(int age);
    // SQL: SELECT * FROM users WHERE age = ? ORDER BY username DESC
    
    // Order by multiple fields
    List<User> findByActiveOrderByCreatedAtDescUsernameAsc(boolean active);
    // SQL: SELECT * FROM users WHERE active = ? ORDER BY created_at DESC, username ASC
    
    // Just ordering (no filter)
    List<User> findAllByOrderByCreatedAtDesc();
    // SQL: SELECT * FROM users ORDER BY created_at DESC
    
    // ===============================================
    // 9️⃣ LIMITING RESULTS - First, Top
    // ===============================================
    
    // Get first result
    User findFirstByOrderByCreatedAtDesc();
    // SQL: SELECT * FROM users ORDER BY created_at DESC LIMIT 1
    
    Optional<User> findFirstByUsername(String username);
    // SQL: SELECT * FROM users WHERE username = ? LIMIT 1
    
    // Get top N results
    List<User> findTop5ByOrderByCreatedAtDesc();
    // SQL: SELECT * FROM users ORDER BY created_at DESC LIMIT 5
    
    List<User> findTop10ByAgeGreaterThan(int age);
    // SQL: SELECT * FROM users WHERE age > ? LIMIT 10
    
    // First and Top are interchangeable
    List<User> findFirst3ByOrderByCreatedAtDesc();
    // Same as: findTop3ByOrderByCreatedAtDesc()
    
    // ===============================================
    // 🔟 EXISTS / COUNT - Checking & Counting
    // ===============================================
    
    // EXISTS - Returns boolean
    boolean existsByEmail(String email);
    // SQL: SELECT EXISTS(SELECT 1 FROM users WHERE email = ?)
    
    boolean existsByUsername(String username);
    // SQL: SELECT EXISTS(SELECT 1 FROM users WHERE username = ?)
    
    boolean existsByUsernameAndEmail(String username, String email);
    // SQL: SELECT EXISTS(SELECT 1 FROM users WHERE username = ? AND email = ?)
    
    // COUNT - Returns long
    long countByAge(int age);
    // SQL: SELECT COUNT(*) FROM users WHERE age = ?
    
    long countByAgeGreaterThan(int age);
    // SQL: SELECT COUNT(*) FROM users WHERE age > ?
    
    long countByActiveTrue();
    // SQL: SELECT COUNT(*) FROM users WHERE active = true
    
    // ===============================================
    // 1️⃣1️⃣ DELETE / REMOVE - Deletion Queries
    // ===============================================
    
    // DELETE by field (returns void)
    @Transactional
    void deleteByUsername(String username);
    // SQL: DELETE FROM users WHERE username = ?
    
    // DELETE and return count
    @Transactional
    long deleteByAgeGreaterThan(int age);
    // SQL: DELETE FROM users WHERE age > ?
    // Returns: number of deleted records
    
    @Transactional
    long removeByEmail(String email);  // "remove" = same as "delete"
    // SQL: DELETE FROM users WHERE email = ?
    
    // ===============================================
    // 1️⃣2️⃣ BOOLEAN FIELDS - True/False
    // ===============================================
    
    // Boolean field queries
    List<User> findByActiveTrue();
    // SQL: SELECT * FROM users WHERE active = true
    
    List<User> findByActiveFalse();
    // SQL: SELECT * FROM users WHERE active = false
    
    List<User> findByActive(boolean active);
    // SQL: SELECT * FROM users WHERE active = ?
    
    // ===============================================
    // 1️⃣3️⃣ DISTINCT - Unique Results
    // ===============================================
    
    List<User> findDistinctByAge(int age);
    // SQL: SELECT DISTINCT * FROM users WHERE age = ?
    
    // ===============================================
    // 1️⃣4️⃣ COMPLEX QUERIES - Combining Multiple Operators
    // ===============================================
    
    // Multiple conditions with different operators
    List<User> findByAgeGreaterThanAndUsernameStartingWith(int age, String prefix);
    // SQL: SELECT * FROM users WHERE age > ? AND username LIKE ?
    
    List<User> findByEmailIsNotNullAndAgeGreaterThan(int age);
    // SQL: SELECT * FROM users WHERE email IS NOT NULL AND age > ?
    
    List<User> findByUsernameContainingOrEmailContaining(String username, String email);
    // SQL: SELECT * FROM users WHERE username LIKE ? OR email LIKE ?
    
    Optional<User> findFirstByActiveOrderByCreatedAtDesc(boolean active);
    // SQL: SELECT * FROM users WHERE active = ? ORDER BY created_at DESC LIMIT 1
    
    List<User> findTop5ByAgeGreaterThanAndActiveTrueOrderByCreatedAtDesc(int age);
    // SQL: SELECT * FROM users WHERE age > ? AND active = true ORDER BY created_at DESC LIMIT 5
}
```

---

#### 📌 Method Naming Keywords Cheatsheet

| Keyword | SQL Equivalent | Example |
|---------|---------------|---------|
| `find...By` | `SELECT ... WHERE` | `findByUsername` |
| `read...By` | `SELECT ... WHERE` | `readByEmail` |
| `query...By` | `SELECT ... WHERE` | `queryByAge` |
| `get...By` | `SELECT ... WHERE` | `getByUsername` |
| `count...By` | `SELECT COUNT(*) WHERE` | `countByAge` |
| `exists...By` | `SELECT EXISTS WHERE` | `existsByEmail` |
| `delete...By` | `DELETE WHERE` | `deleteByUsername` |
| `remove...By` | `DELETE WHERE` | `removeByEmail` |
| `And` | `AND` | `findByNameAndAge` |
| `Or` | `OR` | `findByNameOrEmail` |
| `Between` | `BETWEEN` | `findByAgeBetween` |
| `LessThan` | `<` | `findByAgeLessThan` |
| `LessThanEqual` | `<=` | `findByAgeLessThanEqual` |
| `GreaterThan` | `>` | `findByAgeGreaterThan` |
| `GreaterThanEqual` | `>=` | `findByAgeGreaterThanEqual` |
| `After` | `>` (for dates) | `findByCreatedAtAfter` |
| `Before` | `<` (for dates) | `findByCreatedAtBefore` |
| `IsNull` | `IS NULL` | `findByEmailIsNull` |
| `IsNotNull` | `IS NOT NULL` | `findByEmailIsNotNull` |
| `Like` | `LIKE` | `findByNameLike` |
| `NotLike` | `NOT LIKE` | `findByNameNotLike` |
| `StartingWith` | `LIKE 'prefix%'` | `findByNameStartingWith` |
| `EndingWith` | `LIKE '%suffix'` | `findByNameEndingWith` |
| `Containing` | `LIKE '%value%'` | `findByNameContaining` |
| `OrderBy` | `ORDER BY` | `findByAgeOrderByNameAsc` |
| `Not` | `!=` | `findByAgeNot` |
| `In` | `IN (...)` | `findByIdIn` |
| `NotIn` | `NOT IN (...)` | `findByIdNotIn` |
| `True` | `= true` | `findByActiveTrue` |
| `False` | `= false` | `findByActiveFalse` |
| `IgnoreCase` | `UPPER(col) = UPPER(?)` | `findByNameIgnoreCase` |
| `First` | `LIMIT 1` | `findFirstByAge` |
| `Top` | `LIMIT n` | `findTop10ByAge` |
| `Distinct` | `DISTINCT` | `findDistinctByAge` |

---

#### 📌 Practical Examples with Real Use Cases

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    // ✅ Use Case: Login - Check if user exists with email and password
    Optional<User> findByEmailAndPassword(String email, String password);
    
    // ✅ Use Case: Registration - Check if username is already taken
    boolean existsByUsername(String username);
    
    // ✅ Use Case: Search users by partial name match
    List<User> findByUsernameContainingIgnoreCase(String searchTerm);
    
    // ✅ Use Case: Get recently registered users
    List<User> findTop10ByOrderByCreatedAtDesc();
    
    // ✅ Use Case: Find active users in a specific age range
    List<User> findByActiveTrueAndAgeBetween(int minAge, int maxAge);
    
    // ✅ Use Case: Find users who haven't verified email
    List<User> findByEmailVerifiedFalseAndCreatedAtBefore(LocalDateTime date);
    
    // ✅ Use Case: Find users by multiple possible emails
    List<User> findByEmailIn(List<String> emails);
    
    // ✅ Use Case: Admin - Find users who haven't logged in recently
    List<User> findByLastLoginBeforeAndActiveTrue(LocalDateTime date);
    
    // ✅ Use Case: Delete inactive users (cleanup job)
    @Transactional
    long deleteByActiveFalseAndCreatedAtBefore(LocalDateTime date);
    
    // ✅ Use Case: Get premium users sorted by subscription date
    List<User> findBySubscriptionTypeOrderBySubscriptionDateDesc(String type);
}
```

---

#### 📌 Important Notes & Best Practices

**1. Return Types:**
```java
// Single result - returns null if not found
User findByUsername(String username);

// Single result - returns Optional (RECOMMENDED)
Optional<User> findByUsername(String username);

// Multiple results
List<User> findByAge(int age);
Set<User> findByAge(int age);      // Also valid
Stream<User> findByAge(int age);   // For large datasets

// Counting
long countByAge(int age);

// Checking existence
boolean existsByEmail(String email);
```

**2. @Transactional for Delete Operations:**
```java
// DELETE queries MUST be @Transactional
@Transactional
void deleteByUsername(String username);

@Transactional
long deleteByAgeGreaterThan(int age);
```

**3. Method Name Limits:**
- Method names can get VERY long for complex queries
- If method name becomes unreadable, use `@Query` instead

**Too Long:**
```java
// ❌ Hard to read
List<User> findByAgeGreaterThanAndEmailIsNotNullAndActiveTrueAndCreatedAtBeforeOrderByUsernameAsc(
    int age, LocalDateTime date);

// ✅ Better - use @Query
@Query("SELECT u FROM User u WHERE u.age > :age AND u.email IS NOT NULL " +
       "AND u.active = true AND u.createdAt < :date ORDER BY u.username ASC")
List<User> findActiveUsersCreatedBefore(@Param("age") int age, @Param("date") LocalDateTime date);
```

**Interview Tip:** Memorize common keywords: `And`, `Or`, `Between`, `LessThan`, `GreaterThan`, `Like`, `Containing`, `In`, `OrderBy`, `IgnoreCase`

---

### 🔹 Complete Working Example: User Management System

Let's build a complete working application to see how everything fits together!

#### 📌 Project Structure

```
src/main/java/com/example/
├── entity/
│   └── User.java
├── repository/
│   └── UserRepository.java
├── service/
│   └── UserService.java
├── controller/
│   └── UserController.java
└── Application.java

src/main/resources/
└── application.properties
```

---

#### 📌 Complete Application Code

**application.properties:**
```properties
# H2 in-memory database
spring.datasource.url=jdbc:h2:mem:userdb
spring.datasource.username=sa
spring.datasource.password=
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.h2.console.enabled=true
```

**Entity (User.java):**
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, unique = true)
    private String username;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    private int age;
    private boolean active = true;
    
    @Column(updatable = false)
    private LocalDateTime createdAt;
    
    @PrePersist
    protected void onCreate() {
        createdAt = LocalDateTime.now();
    }
    
    // Constructors, getters, setters...
}
```

**Repository (UserRepository.java):**
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // Authentication
    Optional<User> findByEmail(String email);
    
    // Validation
    boolean existsByUsername(String username);
    boolean existsByEmail(String email);
    
    // Search
    List<User> findByUsernameContainingIgnoreCase(String searchTerm);
    
    // Filtering
    List<User> findByActiveTrue();
    List<User> findByAgeBetween(int minAge, int maxAge);
    List<User> findTop10ByOrderByCreatedAtDesc();
}
```

**Service (UserService.java):**
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    @Transactional
    public User createUser(User user) {
        if (userRepository.existsByUsername(user.getUsername())) {
            throw new IllegalArgumentException("Username already exists");
        }
        return userRepository.save(user);
    }
    
    public User getUserById(Long id) {
        return userRepository.findById(id)
                .orElseThrow(() -> new RuntimeException("User not found"));
    }
    
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
    
    public List<User> searchUsers(String term) {
        return userRepository.findByUsernameContainingIgnoreCase(term);
    }
    
    @Transactional
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }
}
```

**Controller (UserController.java):**
```java
@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    @PostMapping
    public ResponseEntity<User> createUser(@RequestBody User user) {
        User created = userService.createUser(user);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }
    
    @GetMapping
    public ResponseEntity<List<User>> getAllUsers() {
        return ResponseEntity.ok(userService.getAllUsers());
    }
    
    @GetMapping("/{id}")
    public ResponseEntity<User> getUserById(@PathVariable Long id) {
        return ResponseEntity.ok(userService.getUserById(id));
    }
    
    @GetMapping("/search")
    public ResponseEntity<List<User>> searchUsers(@RequestParam String term) {
        return ResponseEntity.ok(userService.searchUsers(term));
    }
    
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteUser(@PathVariable Long id) {
        userService.deleteUser(id);
        return ResponseEntity.noContent().build();
    }
}
```

---

#### 📌 Testing the API

```bash
# Create a user
curl -X POST http://localhost:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{"username":"john_doe","email":"john@example.com","age":25}'

# Get all users
curl http://localhost:8080/api/users

# Get user by ID
curl http://localhost:8080/api/users/1

# Search users
curl http://localhost:8080/api/users/search?term=john

# Delete user
curl -X DELETE http://localhost:8080/api/users/1
```

---

#### 📌 What Happens Behind the Scenes?

**When you call `POST /api/users`:**

```
1. Request → UserController.createUser()
   ↓
2. Controller → UserService.createUser()
   ↓
3. Service validates: existsByUsername()
   Spring Data JPA generates: SELECT COUNT(*) FROM users WHERE username = ?
   ↓
4. Service calls: userRepository.save(user)
   ↓
5. Hibernate detects new entity (id is null)
   ↓
6. @PrePersist callback executed (sets createdAt)
   ↓
7. Hibernate generates SQL:
   INSERT INTO users (username, email, age, active, created_at)
   VALUES ('john_doe', 'john@example.com', 25, true, '2026-05-23...')
   ↓
8. Database generates ID and returns it
   ↓
9. Hibernate sets ID on User object
   ↓
10. Returns User with ID to Controller
    ↓
11. Controller returns HTTP 201 Created with User JSON
```

---

#### 📌 Key Takeaways

1. ✅ **Entity** - Java class maps to database table
2. ✅ **Repository Interface** - No implementation code needed!
3. ✅ **Service Layer** - Business logic and transactions
4. ✅ **Controller** - REST API endpoints
5. ✅ **Automatic SQL** - Method names become queries
6. ✅ **Type Safety** - Compile-time checking
7. ✅ **90% Less Code** - Compared to traditional JDBC

**This is why Spring Data JPA is the industry standard!** 🎉

---

### 🔹 Custom Queries (@Query)

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    // JPQL (Java Persistence Query Language)
    @Query("SELECT u FROM User u WHERE u.email = ?1")
    Optional<User> findByEmailJPQL(String email);
    
    // JPQL with named parameters
    @Query("SELECT u FROM User u WHERE u.username = :username AND u.age > :age")
    List<User> findByUsernameAndAgeGreaterThan(
        @Param("username") String username,
        @Param("age") int age
    );
    
    // Native SQL Query
    @Query(value = "SELECT * FROM users WHERE email = ?1", nativeQuery = true)
    Optional<User> findByEmailNative(String email);
    
    // Projection (select specific columns)
    @Query("SELECT u.username FROM User u WHERE u.id = ?1")
    String findUsernameById(Long id);
    
    // Update query
    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.email = :email WHERE u.id = :id")
    int updateEmail(@Param("id") Long id, @Param("email") String email);
    
    // Delete query
    @Modifying
    @Transactional
    @Query("DELETE FROM User u WHERE u.age < :age")
    int deleteUsersYoungerThan(@Param("age") int age);
    
    // Join query
    @Query("SELECT u FROM User u JOIN u.orders o WHERE o.status = :status")
    List<User> findUsersWithOrderStatus(@Param("status") String status);
    
    // Pagination
    @Query("SELECT u FROM User u WHERE u.age > :age")
    Page<User> findByAgeGreaterThan(@Param("age") int age, Pageable pageable);
}
```

**JPQL vs Native SQL:**
- **JPQL** - Works with entity classes (database-independent)
- **Native SQL** - Direct SQL (database-specific, faster for complex queries)

---

## 5.3 Hibernate/JPA Concepts

### 🔹 What is Hibernate and JPA? (From Absolute Basics)

#### 📌 Understanding ORM (Object-Relational Mapping)

**The Fundamental Problem:**

In application development, we have two different worlds:
- **Object-Oriented World** (Java) - Objects, classes, inheritance
- **Relational World** (Database) - Tables, rows, foreign keys

**The Mismatch:**
```
Java Code:                       Database:
─────────                       ──────────
User user = new User()     ←→   users table
  - Long id                       - id (bigint)
  - String name                   - name (varchar)
  - List<Order> orders            - ??? (Can't store list!)
  
Order order = new Order()  ←→   orders table
  - Long id                       - id (bigint)
  - User user                     - user_id (foreign key)
```

**ORM Solution:** Automatically bridges the gap between objects and tables!

---

#### 📌 What is JPA (Java Persistence API)?

**JPA** is a **specification** (like a contract/interface) that defines:
- How Java objects should be mapped to database tables
- How to perform CRUD operations on entities
- How to handle relationships between entities
- Query language (JPQL) for database-independent queries

**Key Point:** JPA doesn't do anything by itself - it's just a set of rules/interfaces!

**JPA Specification includes:**
```java
// Annotations defined by JPA
@Entity          // javax.persistence.Entity
@Id              // javax.persistence.Id
@Column          // javax.persistence.Column
@OneToMany       // javax.persistence.OneToMany
@ManyToOne       // javax.persistence.ManyToOne
@Query           // org.springframework.data.jpa.repository.Query
```

**Analogy:**
```
JPA is like:
- JDBC API (java.sql.*)         → Just interfaces
- Servlet API                    → Just interfaces

Implementation needed:
- JDBC → MySQL Driver, Oracle Driver
- JPA → Hibernate, EclipseLink
```

---

#### 📌 What is Hibernate?

**Hibernate** is the **implementation** of the JPA specification. It's the actual code that does the work!

**Hibernate is:**
1. **ORM Framework** - Maps Java objects to database tables
2. **JPA Provider** - Implements JPA specification (95% market share!)
3. **Feature-Rich** - Offers more features than JPA specification requires

**What Hibernate Does:**

```java
// You write this Java code:
User user = new User("john", "john@example.com");
entityManager.persist(user);  // JPA method

// Hibernate automatically:
// 1. Generates SQL: INSERT INTO users (username, email) VALUES (?, ?)
// 2. Executes SQL via JDBC
// 3. Gets generated ID from database
// 4. Sets ID back on Java object
// 5. Manages transaction
// 6. Caches the entity (first-level cache)
```

**Hibernate Architecture:**
```
┌──────────────────────────────────────┐
│      Your Application Code           │
│   (Entities, Repositories)           │
└─────────────┬────────────────────────┘
              │
┌─────────────▼────────────────────────┐
│         JPA API (Interfaces)         │
│   EntityManager, Query, etc.         │
└─────────────┬────────────────────────┘
              │
┌─────────────▼────────────────────────┐
│      Hibernate (Implementation)      │
│  ┌────────────────────────────────┐  │
│  │  Session Factory               │  │
│  │  - Creates sessions            │  │
│  │  - Manages connections         │  │
│  └────────────────────────────────┘  │
│  ┌────────────────────────────────┐  │
│  │  Session (EntityManager)       │  │
│  │  - Manages entities            │  │
│  │  - Persistence context         │  │
│  │  - First-level cache           │  │
│  └────────────────────────────────┘  │
│  ┌────────────────────────────────┐  │
│  │  Transaction Manager           │  │
│  │  - Begin, commit, rollback     │  │
│  └────────────────────────────────┘  │
│  ┌────────────────────────────────┐  │
│  │  Query Translator              │  │
│  │  - HQL/JPQL → SQL              │  │
│  └────────────────────────────────┘  │
│  ┌────────────────────────────────┐  │
│  │  Second-Level Cache (Optional) │  │
│  └────────────────────────────────┘  │
└─────────────┬────────────────────────┘
              │
┌─────────────▼────────────────────────┐
│            JDBC                      │
└─────────────┬────────────────────────┘
              │
┌─────────────▼────────────────────────┐
│          Database                    │
└──────────────────────────────────────┘
```

---

#### 📌 Entity Lifecycle States

Understanding entity states is crucial for working with Hibernate!

**Four States of an Entity:**

```
1. TRANSIENT (New)
   ↓
2. PERSISTENT (Managed)
   ↓
3. DETACHED (No longer managed)
   ↓
4. REMOVED (Marked for deletion)
```

**Detailed Explanation:**

```java
// 1️⃣ TRANSIENT - Object exists in memory, NOT in database
User user = new User("john", "john@example.com");
// State: TRANSIENT
// - Not associated with any Session/EntityManager
// - No database representation
// - Changes are NOT tracked

// 2️⃣ PERSISTENT - Object managed by Hibernate AND in database
entityManager.persist(user);  // or save()
// State: PERSISTENT (MANAGED)
// - Tracked by persistence context
// - Changes automatically synced to database
// - Exists in first-level cache
user.setEmail("newemail@example.com");  // Auto-UPDATE in DB!

// 3️⃣ DETACHED - Object was persistent, but Session closed
entityManager.detach(user);  // or session closes
// State: DETACHED
// - Object still exists in memory
// - NO longer tracked by Hibernate
// - Changes NOT synced to database
user.setEmail("another@example.com");  // NOT saved to DB!

// Reattach to make it persistent again
entityManager.merge(user);  // Back to PERSISTENT state

// 4️⃣ REMOVED - Marked for deletion
entityManager.remove(user);
// State: REMOVED
// - Still in persistence context
// - Will be DELETED from database on transaction commit
```

**Visual State Transitions:**
```
    new User()  →  TRANSIENT
         ↓ persist()
    PERSISTENT (Managed)
         ↓ detach() / close session
    DETACHED
         ↓ merge()
    PERSISTENT
         ↓ remove()
    REMOVED
         ↓ commit()
    [DELETED FROM DB]
```

---

#### 📌 Persistence Context (Session)

**What is Persistence Context?**
- A "container" that holds all entity instances during a transaction
- Also known as **First-Level Cache**
- Tracks all changes to entities automatically

**Example:**
```java
@Transactional
public void updateUser() {
    User user = userRepository.findById(1L).get();
    // Persistence Context now contains: [User(id=1)]
    
    user.setUsername("new_name");
    // Change is tracked automatically!
    
    // NO explicit save() needed!
    // Hibernate auto-generates UPDATE at end of transaction
}
```

**Benefits of Persistence Context:**
1. **Automatic Dirty Checking** - Tracks changes, auto-generates SQL
2. **Identity Map** - Ensures only one instance per ID (prevents duplicates)
3. **Write-Behind** - Batches SQL statements for performance
4. **First-Level Cache** - Avoids duplicate database queries

**Demonstration:**
```java
@Transactional
public void demonstratePersistenceContext() {
    // First access - queries database
    User user1 = entityManager.find(User.class, 1L);
    System.out.println(user1.getUsername());  // Executes SQL
    
    // Second access - returns from cache (no SQL!)
    User user2 = entityManager.find(User.class, 1L);
    System.out.println(user2.getUsername());  // NO SQL!
    
    // Both references point to SAME object!
    System.out.println(user1 == user2);  // true
}
```

---

### 🔹 Entity Mapping (Complete Guide)

**Entity mapping** defines how Java classes map to database tables.

#### 📌 Basic Entity Mapping

```java
@Entity  // ← Required: Marks this class as a JPA entity
@Table(name = "products",  // ← Optional: Customize table name
       schema = "inventory",  // ← Optional: Database schema
       catalog = "mydb",  // ← Optional: Database catalog
       indexes = {
           @Index(name = "idx_product_name", columnList = "name"),
           @Index(name = "idx_product_price", columnList = "price")
       },
       uniqueConstraints = {
           @UniqueConstraint(columnNames = {"name", "category"})
       })
public class Product {
    
    // ============================================
    // PRIMARY KEY STRATEGIES
    // ============================================
    
    @Id  // ← Required: Marks primary key
    @GeneratedValue(strategy = GenerationType.IDENTITY)  // ← Auto-increment
    private Long id;
    
    // Other primary key generation strategies:
    // GenerationType.AUTO - JPA chooses strategy
    // GenerationType.SEQUENCE - Uses database sequence
    // GenerationType.TABLE - Uses separate table for ID generation
    // GenerationType.IDENTITY - Database auto-increment (MySQL, PostgreSQL)
    
    // ============================================
    // BASIC COLUMN MAPPING
    // ============================================
    
    @Column(
        name = "product_name",  // Database column name
        nullable = false,       // NOT NULL constraint
        unique = true,          // UNIQUE constraint
        length = 100,          // VARCHAR(100)
        precision = 10,        // For decimal numbers (total digits)
        scale = 2,             // For decimal numbers (digits after decimal)
        insertable = true,     // Include in INSERT statements
        updatable = true,      // Include in UPDATE statements
        columnDefinition = "VARCHAR(100) DEFAULT 'Unknown'"  // Custom SQL
    )
    private String name;
    
    // Simple column (uses field name as column name)
    @Column(nullable = false)
    private String category;
    
    // ============================================
    // NUMERIC TYPES
    // ============================================
    
    @Column(precision = 10, scale = 2)  // DECIMAL(10,2)
    private BigDecimal price;
    
    private Integer quantity;  // INT
    private Long stockCount;   // BIGINT
    private Double weight;     // DOUBLE
    
    // ============================================
    // ENUM MAPPING
    // ============================================
    
    @Enumerated(EnumType.STRING)  // ← RECOMMENDED: Stores "ACTIVE", "INACTIVE"
    private ProductStatus status;
    
    // @Enumerated(EnumType.ORDINAL)  // ❌ AVOID: Stores 0, 1, 2 (breaks if enum order changes!)
    
    // ============================================
    // DATE/TIME MAPPING
    // ============================================
    
    // Java 8 Date/Time API (Recommended)
    @Column(name = "created_at", updatable = false)
    private LocalDateTime createdAt;
    
    private LocalDate manufacturingDate;
    private LocalTime processingTime;
    private Instant lastUpdated;
    
    // Old Java Date API (Legacy code only)
    @Temporal(TemporalType.TIMESTAMP)  // DATETIME
    private Date legacyCreatedAt;
    
    @Temporal(TemporalType.DATE)  // DATE only
    private Date legacyDate;
    
    @Temporal(TemporalType.TIME)  // TIME only
    private Date legacyTime;
    
    // ============================================
    // LARGE OBJECTS
    // ============================================
    
    @Lob  // ← Large Object: TEXT or LONGTEXT
    @Column(columnDefinition = "TEXT")
    private String description;
    
    @Lob  // ← BLOB (Binary Large Object) for images, files
    private byte[] productImage;
    
    // ============================================
    // BOOLEAN MAPPING
    // ============================================
    
    @Column(name = "is_available")
    private Boolean available;  // Maps to TINYINT(1) or BOOLEAN
    
    private boolean featured;  // Primitive boolean
    
    // ============================================
    // TRANSIENT FIELDS (NOT PERSISTED)
    // ============================================
    
    @Transient  // ← Not saved to database
    private String temporaryData;
    
    @Transient
    private Double calculatedTax;  // Calculated at runtime
    
    // ============================================
    // VERSION (OPTIMISTIC LOCKING)
    // ============================================
    
    @Version  // ← Automatic versioning for optimistic locking
    private Long version;
    // Hibernate auto-increments this on every update
    // Prevents lost updates in concurrent environments
    
    // ============================================
    // LIFECYCLE CALLBACKS
    // ============================================
    
    @PrePersist  // ← Called BEFORE INSERT
    protected void onCreate() {
        createdAt = LocalDateTime.now();
        if (status == null) {
            status = ProductStatus.ACTIVE;
        }
    }
    
    @PreUpdate  // ← Called BEFORE UPDATE
    protected void onUpdate() {
        lastUpdated = Instant.now();
    }
    
    @PostPersist  // ← Called AFTER INSERT
    protected void afterCreate() {
        System.out.println("Product created with ID: " + id);
    }
    
    @PostLoad  // ← Called AFTER SELECT
    protected void afterLoad() {
        // Calculate derived values after loading from DB
        calculatedTax = price.multiply(new BigDecimal("0.1")).doubleValue();
    }
    
    @PreRemove  // ← Called BEFORE DELETE
    protected void beforeDelete() {
        System.out.println("About to delete product: " + name);
    }
    
    @PostRemove  // ← Called AFTER DELETE
    protected void afterDelete() {
        System.out.println("Product deleted successfully");
    }
    
    // Constructors, Getters, Setters...
    public Product() {
        // JPA requires no-arg constructor
    }
    
    public Product(String name, BigDecimal price) {
        this.name = name;
        this.price = price;
    }
    
    // Getters and Setters (omitted for brevity)
}

// Enum for Product Status
public enum ProductStatus {
    ACTIVE,
    INACTIVE,
    DISCONTINUED,
    OUT_OF_STOCK
}
```

---

#### 📌 All Entity Lifecycle Callbacks

| Annotation | Timing | Use Case | Example |
|-----------|--------|----------|---------|
| `@PrePersist` | Before INSERT | Set created timestamp, default values | `createdAt = LocalDateTime.now()` |
| `@PostPersist` | After INSERT | Logging, notifications | `log.info("Created: " + id)` |
| `@PreUpdate` | Before UPDATE | Set modified timestamp | `updatedAt = LocalDateTime.now()` |
| `@PostUpdate` | After UPDATE | Audit logging, cache invalidation | `clearCache()` |
| `@PreRemove` | Before DELETE | Cleanup, validation | `validateDeletion()` |
| `@PostRemove` | After DELETE | Cleanup external resources | `deleteFromFileSystem()` |
| `@PostLoad` | After SELECT | Calculate derived fields | `fullName = firstName + lastName` |

---

#### 📌 Primary Key Generation Strategies

```java
// 1️⃣ IDENTITY - Auto-increment (MySQL, PostgreSQL)
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
// SQL: id BIGINT AUTO_INCREMENT

// 2️⃣ SEQUENCE - Database sequence (Oracle, PostgreSQL)
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
@SequenceGenerator(name = "user_seq", sequenceName = "user_sequence", 
                   allocationSize = 1, initialValue = 1)
private Long id;
// SQL: CREATE SEQUENCE user_sequence START WITH 1 INCREMENT BY 1

// 3️⃣ TABLE - Separate table for ID generation (database-independent)
@Id
@GeneratedValue(strategy = GenerationType.TABLE, generator = "user_gen")
@TableGenerator(name = "user_gen", table = "id_generator",
                pkColumnName = "gen_name", valueColumnName = "gen_value",
                pkColumnValue = "user_id", allocationSize = 1)
private Long id;
// Creates separate table: id_generator (gen_name, gen_value)

// 4️⃣ AUTO - Let JPA choose (not recommended)
@Id
@GeneratedValue(strategy = GenerationType.AUTO)
private Long id;
// Hibernate decides based on database dialect

// 5️⃣ UUID - Unique identifier (no database support needed)
@Id
@GeneratedValue(generator = "UUID")
@GenericGenerator(name = "UUID", strategy = "org.hibernate.id.UUIDGenerator")
@Column(updatable = false, nullable = false)
private UUID id;

// 6️⃣ Manual ID assignment
@Id
private Long id;  // You set the ID manually before saving
```

**Which Strategy to Use?**

| Database | Recommended Strategy | Reason |
|----------|---------------------|--------|
| MySQL | `IDENTITY` | Native auto-increment support |
| PostgreSQL | `SEQUENCE` or `IDENTITY` | Both work well, SEQUENCE is more efficient |
| Oracle | `SEQUENCE` | No auto-increment, uses sequences |
| H2 | `IDENTITY` | Simple and fast |
| All | `UUID` | Distributed systems, no coordination needed |

---

#### 📌 Composite Primary Keys

**When to use:** Multiple columns form the primary key (e.g., OrderItem with orderId + productId)

**Method 1: @IdClass**
```java
// Composite Key Class
public class OrderItemId implements Serializable {
    private Long orderId;
    private Long productId;
    
    // Must have: equals(), hashCode(), no-arg constructor
    public OrderItemId() {}
    
    public OrderItemId(Long orderId, Long productId) {
        this.orderId = orderId;
        this.productId = productId;
    }
    
    // equals() and hashCode() are REQUIRED!
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        OrderItemId that = (OrderItemId) o;
        return Objects.equals(orderId, that.orderId) &&
               Objects.equals(productId, that.productId);
    }
    
    @Override
    public int hashCode() {
        return Objects.hash(orderId, productId);
    }
}

// Entity with Composite Key
@Entity
@IdClass(OrderItemId.class)
public class OrderItem {
    @Id
    private Long orderId;
    
    @Id
    private Long productId;
    
    private Integer quantity;
    private BigDecimal price;
    
    // Getters and Setters
}

// Usage
OrderItem item = new OrderItem();
item.setOrderId(1L);
item.setProductId(100L);
item.setQuantity(5);
repository.save(item);

// Finding by composite key
OrderItemId id = new OrderItemId(1L, 100L);
OrderItem found = repository.findById(id).get();
```

**Method 2: @EmbeddedId (Recommended)**
```java
// Composite Key Class (Embeddable)
@Embeddable
public class OrderItemId implements Serializable {
    private Long orderId;
    private Long productId;
    
    // Constructors, equals(), hashCode()
}

// Entity
@Entity
public class OrderItem {
    @EmbeddedId
    private OrderItemId id;
    
    private Integer quantity;
    private BigDecimal price;
    
    // Getters and Setters
}

// Usage
OrderItemId id = new OrderItemId(1L, 100L);
OrderItem item = new OrderItem();
item.setId(id);
item.setQuantity(5);
repository.save(item);
```

---

### 🔹 Relationships ⭐ EXTREMELY IMPORTANT

Understanding relationships is **CRITICAL** for JPA! This is one of the most asked topics in interviews.

---

#### 📌 The Four Types of Relationships

```
1. @OneToOne   - One entity relates to exactly one other entity
2. @OneToMany  - One entity relates to many other entities
3. @ManyToOne  - Many entities relate to one entity
4. @ManyToMany - Many entities relate to many other entities
```

**Real-World Examples:**
- **OneToOne**: User ←→ UserProfile (One user has one profile)
- **OneToMany**: User ←→ Orders (One user has many orders)
- **ManyToOne**: Orders ←→ User (Many orders belong to one user)
- **ManyToMany**: Students ←→ Courses (Many students take many courses)

---

#### 📌 1️⃣ @OneToOne Relationship

**Scenario:** One User has exactly one UserProfile.

**Database Structure:**
```
users table           user_profiles table
─────────────        ────────────────────
id (PK)              id (PK)
username             user_id (FK) ← Points to users.id
email                bio
                     phone_number
```

**Unidirectional @OneToOne (User → Profile):**
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String username;
    private String email;
    
    @OneToOne(
        cascade = CascadeType.ALL,  // Operations on User cascade to Profile
        fetch = FetchType.LAZY,      // Don't load profile until accessed
        orphanRemoval = true         // Delete profile if removed from User
    )
    @JoinColumn(
        name = "profile_id",          // Foreign key column in users table
        referencedColumnName = "id",  // References user_profiles.id
        unique = true                 // Ensures one-to-one relationship
    )
    private UserProfile profile;
    
    // Getters and Setters
}

@Entity
@Table(name = "user_profiles")
public class UserProfile {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String bio;
    private String phoneNumber;
    
    // No reference to User (unidirectional)
    
    // Getters and Setters
}

// Usage
User user = new User();
user.setUsername("john_doe");

UserProfile profile = new UserProfile();
profile.setBio("Software Developer");
profile.setPhoneNumber("555-1234");

user.setProfile(profile);
userRepository.save(user);  // Saves both user and profile
```

**Bidirectional @OneToOne (User ↔ Profile):**
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String username;
    
    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    //        ↑ "user" is the field name in UserProfile class
    private UserProfile profile;
    
    // Helper method for bidirectional relationship
    public void setProfile(UserProfile profile) {
        this.profile = profile;
        profile.setUser(this);  // Set both sides!
    }
}

@Entity
public class UserProfile {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String bio;
    
    @OneToOne
    @JoinColumn(name = "user_id")  // Foreign key in user_profiles table
    private User user;  // The "owning" side
    
    // Getters and Setters
}

// Usage
User user = new User();
user.setUsername("john_doe");

UserProfile profile = new UserProfile();
profile.setBio("Software Developer");

user.setProfile(profile);  // Sets both sides of relationship
userRepository.save(user);
```

**Key Concepts:**
- **Owning Side**: The side with `@JoinColumn` (owns the foreign key)
- **Inverse Side**: The side with `mappedBy` (doesn't own the foreign key)
- **mappedBy**: Tells JPA "the foreign key is on the OTHER side"

---

#### 📌 2️⃣ @OneToMany / @ManyToOne Relationship ⭐ MOST COMMON

**Scenario:** One User has many Orders. Each Order belongs to one User.

**Database Structure:**
```
users table          orders table
───────────         ─────────────────
id (PK)             id (PK)
username            user_id (FK) ← Points to users.id
email               order_date
                    total_amount
                    status
```

**Bidirectional @OneToMany / @ManyToOne (Recommended):**

```java
// Parent Side (One)
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String username;
    
    @OneToMany(
        mappedBy = "user",           // "user" field in Order class
        cascade = CascadeType.ALL,   // Operations cascade to orders
        orphanRemoval = true,        // Delete order if removed from list
        fetch = FetchType.LAZY       // Don't load orders until accessed
    )
    private List<Order> orders = new ArrayList<>();
    
    // ============================================
    // IMPORTANT: Helper Methods for Bidirectional Relationship
    // ============================================
    
    public void addOrder(Order order) {
        orders.add(order);
        order.setUser(this);  // Set both sides!
    }
    
    public void removeOrder(Order order) {
        orders.remove(order);
        order.setUser(null);  // Clear both sides!
    }
    
    // Getters and Setters
}

// Child Side (Many)
@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private LocalDateTime orderDate;
    private BigDecimal totalAmount;
    private String status;
    
    @ManyToOne(
        fetch = FetchType.LAZY,     // Don't load user until accessed
        optional = false            // user cannot be null
    )
    @JoinColumn(
        name = "user_id",            // Foreign key column in orders table
        nullable = false             // NOT NULL constraint
    )
    private User user;  // The "owning" side
    
    // Getters and Setters
}

// Usage
User user = new User();
user.setUsername("john_doe");

Order order1 = new Order();
order1.setOrderDate(LocalDateTime.now());
order1.setTotalAmount(new BigDecimal("99.99"));
order1.setStatus("PENDING");

Order order2 = new Order();
order2.setOrderDate(LocalDateTime.now());
order2.setTotalAmount(new BigDecimal("149.99"));
order2.setStatus("CONFIRMED");

// Use helper methods to maintain both sides
user.addOrder(order1);
user.addOrder(order2);

userRepository.save(user);  // Saves user and both orders
```

**Unidirectional @ManyToOne (Only Child knows Parent):**
```java
@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;
}

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String username;
    // No orders field (unidirectional)
}

// Can still query orders by user:
List<Order> orders = orderRepository.findByUser(user);
```

**Unidirectional @OneToMany (Only Parent knows Children - NOT RECOMMENDED):**
```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToMany(cascade = CascadeType.ALL)
    @JoinColumn(name = "user_id")  // Creates foreign key in orders table
    private List<Order> orders = new ArrayList<>();
}

@Entity
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    // No user field
}

// ❌ Problem: Less intuitive, harder to query
// ✅ Prefer bidirectional with @ManyToOne on the "many" side
```

---

#### 📌 3️⃣ @ManyToMany Relationship

**Scenario:** Many Students take many Courses. Each Course has many Students.

**Database Structure:**
```
students table       student_course (join table)      courses table
──────────────       ───────────────────────────      ─────────────
id (PK)              student_id (FK) ────────────→   id (PK)
name                 course_id (FK)  ────────────→   name
email                PRIMARY KEY(student_id, course_id) description
                                                       credits
```

**Bidirectional @ManyToMany:**

```java
// Owning Side (Student)
@Entity
@Table(name = "students")
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private String email;
    
    @ManyToMany(
        cascade = {CascadeType.PERSIST, CascadeType.MERGE},  // Don't cascade DELETE!
        fetch = FetchType.LAZY
    )
    @JoinTable(
        name = "student_course",              // Join table name
        joinColumns = @JoinColumn(name = "student_id"),          // FK to this entity
        inverseJoinColumns = @JoinColumn(name = "course_id")     // FK to other entity
    )
    private Set<Course> courses = new HashSet<>();  // Use Set to avoid duplicates
    
    // Helper methods
    public void enrollInCourse(Course course) {
        courses.add(course);
        course.getStudents().add(this);  // Set both sides!
    }
    
    public void dropCourse(Course course) {
        courses.remove(course);
        course.getStudents().remove(this);  // Clear both sides!
    }
    
    // Getters and Setters
}

// Inverse Side (Course)
@Entity
@Table(name = "courses")
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    private String description;
    private Integer credits;
    
    @ManyToMany(
        mappedBy = "courses",  // "courses" field in Student class
        fetch = FetchType.LAZY
    )
    private Set<Student> students = new HashSet<>();
    
    // Getters and Setters
}

// Usage
Student student1 = new Student();
student1.setName("John Doe");
student1.setEmail("john@example.com");

Student student2 = new Student();
student2.setName("Jane Smith");
student2.setEmail("jane@example.com");

Course javaCourse = new Course();
javaCourse.setName("Java Programming");
javaCourse.setCredits(4);

Course pythonCourse = new Course();
pythonCourse.setName("Python Basics");
pythonCourse.setCredits(3);

// Enroll students in courses
student1.enrollInCourse(javaCourse);
student1.enrollInCourse(pythonCourse);
student2.enrollInCourse(javaCourse);

studentRepository.save(student1);
studentRepository.save(student2);
```

**Why Use Set instead of List for @ManyToMany?**
```java
// ❌ List - Can cause duplicate entries, inefficient
@ManyToMany
private List<Course> courses = new ArrayList<>();

// ✅ Set - Prevents duplicates, more efficient
@ManyToMany
private Set<Course> courses = new HashSet<>();
```

---

#### 📌 @ManyToMany with Extra Columns (Join Entity Pattern)

**Problem:** What if the join table needs extra columns?
```
student_course table needs:
- student_id (FK)
- course_id (FK)
- enrollment_date ← Extra column!
- grade ← Extra column!
```

**Solution:** Create a separate entity for the join table!

```java
// Join Entity (Enrollment)
@Entity
@Table(name = "enrollments")
public class Enrollment {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "student_id")
    private Student student;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "course_id")
    private Course course;
    
    // Extra columns
    private LocalDateTime enrollmentDate;
    private String grade;
    private String status;
    
    // Getters and Setters
}

@Entity
public class Student {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @OneToMany(mappedBy = "student", cascade = CascadeType.ALL)
    private Set<Enrollment> enrollments = new HashSet<>();
    
    // Helper method
    public void enrollInCourse(Course course) {
        Enrollment enrollment = new Enrollment();
        enrollment.setStudent(this);
        enrollment.setCourse(course);
        enrollment.setEnrollmentDate(LocalDateTime.now());
        enrollment.setStatus("ACTIVE");
        enrollments.add(enrollment);
        course.getEnrollments().add(enrollment);
    }
}

@Entity
public class Course {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;
    
    @OneToMany(mappedBy = "course", cascade = CascadeType.ALL)
    private Set<Enrollment> enrollments = new HashSet<>();
}

// Usage
Student student = new Student();
student.setName("John Doe");

Course course = new Course();
course.setName("Java Programming");

student.enrollInCourse(course);
studentRepository.save(student);
```

---

#### 📌 CascadeType Explained

**CascadeType** determines which operations should cascade from parent to child.

```java
@OneToMany(cascade = CascadeType.ALL)
private List<Order> orders;
```

**Available Cascade Types:**

| CascadeType | Description | When it Applies | Example |
|-------------|-------------|----------------|---------|
| `PERSIST` | Cascade save operations | `entityManager.persist(parent)` | Saving user saves their orders |
| `MERGE` | Cascade update operations | `entityManager.merge(parent)` | Updating user updates their orders |
| `REMOVE` | Cascade delete operations | `entityManager.remove(parent)` | Deleting user deletes their orders |
| `REFRESH` | Cascade refresh operations | `entityManager.refresh(parent)` | Refreshing user refreshes their orders |
| `DETACH` | Cascade detach operations | `entityManager.detach(parent)` | Detaching user detaches their orders |
| `ALL` | All of the above | Any operation | Apply all cascade types |

**Practical Examples:**

```java
// 1️⃣ Cascade PERSIST - Save parent and children together
@OneToMany(cascade = CascadeType.PERSIST)
private List<Order> orders;

User user = new User();
Order order = new Order();
user.addOrder(order);
entityManager.persist(user);  // Saves both user AND order

// 2️⃣ Cascade REMOVE - Delete parent and children together
@OneToMany(cascade = CascadeType.REMOVE)
private List<Order> orders;

entityManager.remove(user);  // Deletes user AND all orders

// 3️⃣ Multiple Cascades
@OneToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
private List<Order> orders;
// Only cascades save and update, NOT delete

// 4️⃣ No Cascade (Manual management)
@OneToMany  // No cascade specified
private List<Order> orders;

// Must save each order manually
orderRepository.save(order1);
orderRepository.save(order2);
userRepository.save(user);
```

**When to Use Which Cascade?**

| Relationship | Recommended Cascade | Reason |
|--------------|---------------------|--------|
| **User → Orders** | `ALL` | Orders exist only for a user (strong ownership) |
| **User → Profile** | `ALL` | Profile exists only for a user |
| **Order → OrderItems** | `ALL` | Order items exist only for an order |
| **Student ↔ Course** | `PERSIST, MERGE` | ❌ Never `REMOVE`! Deleting student shouldn't delete course! |
| **Post → Comments** | `ALL` | Comments belong to post |

---

#### 📌 orphanRemoval Explained

**orphanRemoval** = Automatically delete child entities when removed from parent collection.

```java
@OneToMany(mappedBy = "user", orphanRemoval = true)
private List<Order> orders;
```

**How it works:**

```java
// WITH orphanRemoval = true
User user = userRepository.findById(1L).get();
Order order = user.getOrders().get(0);
user.getOrders().remove(order);  // Remove from collection
userRepository.save(user);
// Result: Order is DELETED from database automatically!

// WITHOUT orphanRemoval (default: false)
user.getOrders().remove(order);
userRepository.save(user);
// Result: Order still exists in DB, but user_id is set to NULL
```

**orphanRemoval vs CascadeType.REMOVE:**

| Feature | orphanRemoval | CascadeType.REMOVE |
|---------|---------------|-------------------|
| **Triggers when** | Child removed from collection | Parent is deleted |
| **Example** | `list.remove(child)` | `entityManager.remove(parent)` |
| **Use case** | Remove individual children | Delete parent and all children |

**When to Use orphanRemoval:**
- ✅ Strong ownership (child cannot exist without parent)
- ✅ User → Orders (removing order from user should delete it)
- ✅ Post → Comments (removing comment from post should delete it)
- ❌ Student ↔ Course (removing student from course shouldn't delete course!)

---

#### 📌 Relationship Best Practices

**1. Always use FetchType.LAZY (explained in next section):**
```java
@OneToMany(fetch = FetchType.LAZY)  // ✅ Good
@OneToMany(fetch = FetchType.EAGER) // ❌ Bad (performance issues)
```

**2. Use helper methods for bidirectional relationships:**
```java
public void addOrder(Order order) {
    orders.add(order);
    order.setUser(this);  // Always set both sides!
}
```

**3. Use Set for @ManyToMany (not List):**
```java
@ManyToMany
private Set<Course> courses = new HashSet<>();  // ✅ Good
private List<Course> courses = new ArrayList<>(); // ❌ Can cause duplicates
```

**4. Be careful with CascadeType.REMOVE in @ManyToMany:**
```java
@ManyToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})  // ✅ Good
@ManyToMany(cascade = CascadeType.ALL)  // ❌ Bad (deletes shared entities!)
```

**5. Override equals() and hashCode() for entities with relationships:**
```java
@Entity
public class Order {
    @Id
    @GeneratedValue
    private Long id;
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Order)) return false;
        Order order = (Order) o;
        return id != null && id.equals(order.getId());
    }
    
    @Override
    public int hashCode() {
        return getClass().hashCode();
    }
}
```

---

#### 📌 Common Relationship Mistakes

**❌ Mistake 1: Not Setting Both Sides of Bidirectional Relationship**
```java
// Wrong!
user.getOrders().add(order);  // Only sets one side
userRepository.save(user);
// Result: order.getUser() returns null!

// Correct!
user.addOrder(order);  // Helper method sets both sides
userRepository.save(user);
```

**❌ Mistake 2: Using CascadeType.REMOVE with @ManyToMany**
```java
// Wrong!
@ManyToMany(cascade = CascadeType.ALL)
private Set<Course> courses;

studentRepository.delete(student);
// Result: Deletes ALL courses (even for other students)!

// Correct!
@ManyToMany(cascade = {CascadeType.PERSIST, CascadeType.MERGE})
private Set<Course> courses;
```

**❌ Mistake 3: Forgetting @JoinColumn on Owning Side**
```java
// Wrong!
@ManyToOne
private User user;  // JPA uses default column name (user_id)

// Better! (Explicit is better than implicit)
@ManyToOne
@JoinColumn(name = "user_id")  // Explicitly specify column name
private User user;
```

---

### 🔹 Fetch Types (CRITICAL!) ⭐

**FetchType** determines **WHEN** related entities are loaded from the database.

---

#### 📌 FetchType.LAZY vs FetchType.EAGER

**FetchType.LAZY** (Recommended ✅)
- Related entities are **NOT** loaded immediately
- Loaded only when you **access** them
- Better performance (fewer queries)

**FetchType.EAGER** (Avoid ❌)
- Related entities are loaded **immediately** with parent
- Always fetches data even if you don't need it
- Performance problems (unnecessary queries)

---

#### 📌 Detailed Comparison

**Example Setup:**
```java
@Entity
public class User {
    @Id
    private Long id;
    private String username;
    
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
    private List<Order> orders = new ArrayList<>();
}

@Entity
public class Order {
    @Id
    private Long id;
    private BigDecimal amount;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id")
    private User user;
}
```

**Scenario 1: FetchType.LAZY (Default)**

```java
// Query user
User user = userRepository.findById(1L).get();
// SQL executed:
// SELECT * FROM users WHERE id = 1

System.out.println(user.getUsername());  // No additional query

// Access orders (LAZY loading triggers here!)
List<Order> orders = user.getOrders();
for (Order order : orders) {
    System.out.println(order.getAmount());
}
// SQL executed NOW:
// SELECT * FROM orders WHERE user_id = 1
```

**Scenario 2: FetchType.EAGER**

```java
@OneToMany(mappedBy = "user", fetch = FetchType.EAGER)
private List<Order> orders;

// Query user
User user = userRepository.findById(1L).get();
// SQL executed IMMEDIATELY:
// SELECT * FROM users WHERE id = 1
// SELECT * FROM orders WHERE user_id = 1  ← ALWAYS fetched!

// Even if you never use orders, they're already loaded!
System.out.println(user.getUsername());  // Orders were loaded unnecessarily
```

---

#### 📌 Default Fetch Types

**JPA Defaults:**

| Relationship | Default FetchType | Recommendation |
|-------------|-------------------|----------------|
| `@OneToOne` | **EAGER** ❌ | Change to LAZY! |
| `@ManyToOne` | **EAGER** ❌ | Change to LAZY! |
| `@OneToMany` | **LAZY** ✅ | Keep LAZY |
| `@ManyToMany` | **LAZY** ✅ | Keep LAZY |

**Always Override to LAZY:**
```java
// ✅ Good - Explicit LAZY
@ManyToOne(fetch = FetchType.LAZY)
private User user;

@OneToOne(fetch = FetchType.LAZY)
private UserProfile profile;

// ❌ Bad - Default EAGER (performance issues)
@ManyToOne  // Defaults to EAGER!
private User user;
```

---

#### 📌 LazyInitializationException Problem

**The Problem:**
```java
@Transactional
public User getUserById(Long id) {
    return userRepository.findById(id).get();
}  // ← Transaction ends here, Session closes

// In controller or view
User user = userService.getUserById(1L);
List<Order> orders = user.getOrders();  // ❌ LazyInitializationException!
// Error: Session is closed, cannot load orders!
```

**Why it happens:**
- LAZY relationships are loaded through "proxy" objects
- Proxies need an active Hibernate Session
- Session closes when `@Transactional` method ends
- Accessing LAZY relationship outside transaction = Exception!

---

#### 📌 Solutions to LazyInitializationException

**Solution 1: Fetch within Transaction (Recommended)**

```java
@Transactional
public User getUserWithOrders(Long id) {
    User user = userRepository.findById(id).get();
    user.getOrders().size();  // Force loading within transaction
    return user;
}

// Or use JOIN FETCH
@Query("SELECT u FROM User u LEFT JOIN FETCH u.orders WHERE u.id = :id")
Optional<User> findByIdWithOrders(@Param("id") Long id);
```

**Solution 2: @EntityGraph**

```java
@EntityGraph(attributePaths = {"orders"})
Optional<User> findById(Long id);

// Or for specific methods
@EntityGraph(attributePaths = {"orders", "profile"})
@Query("SELECT u FROM User u WHERE u.id = :id")
Optional<User> findByIdWithOrdersAndProfile(@Param("id") Long id);
```

**Solution 3: Open Session in View (NOT RECOMMENDED)**

```properties
# application.properties
spring.jpa.open-in-view=true  # Default in Spring Boot
# Keeps session open until view is rendered
# ❌ Bad practice: Hides performance issues, database connections stay open longer
```

**Solution 4: DTOs with Projections**

```java
// DTO
public class UserWithOrdersDTO {
    private Long id;
    private String username;
    private List<OrderDTO> orders;
    
    // Constructor, getters, setters
}

// Repository
@Query("SELECT new com.example.dto.UserWithOrdersDTO(u.id, u.username, o) " +
       "FROM User u LEFT JOIN u.orders o WHERE u.id = :id")
UserWithOrdersDTO findUserWithOrdersDTO(@Param("id") Long id);
```

---

#### 📌 When to Use EAGER Fetching

**Very Rare Cases:**
```java
// ✅ Acceptable: Always need the related data
@Entity
public class Order {
    @Id
    private Long id;
    
    @ManyToOne(fetch = FetchType.EAGER)
    private OrderStatus status;  // ALWAYS need status with order
}

// ❌ Bad: Not always needed
@Entity
public class User {
    @OneToMany(fetch = FetchType.EAGER)
    private List<Order> orders;  // Might have thousands of orders!
}
```

**Rule of Thumb:**
- **99% of cases**: Use LAZY
- **EAGER only if**: Related data is **ALWAYS** needed AND is **small**

---

#### 📌 Practical Examples with Performance Impact

**Bad Code (EAGER everywhere):**
```java
@Entity
public class User {
    @Id
    private Long id;
    
    @OneToMany(fetch = FetchType.EAGER)  // ❌ Bad
    private List<Order> orders;
    
    @OneToMany(fetch = FetchType.EAGER)  // ❌ Bad
    private List<Address> addresses;
    
    @OneToOne(fetch = FetchType.EAGER)  // ❌ Bad
    private UserProfile profile;
}

// Query ONE user
User user = userRepository.findById(1L).get();

// SQL executed:
// 1. SELECT * FROM users WHERE id = 1
// 2. SELECT * FROM orders WHERE user_id = 1
// 3. SELECT * FROM addresses WHERE user_id = 1
// 4. SELECT * FROM user_profiles WHERE user_id = 1
// Result: 4 queries even if you only need username!
```

**Good Code (LAZY + Explicit Fetching):**
```java
@Entity
public class User {
    @Id
    private Long id;
    
    @OneToMany(fetch = FetchType.LAZY)  // ✅ Good
    private List<Order> orders;
    
    @OneToMany(fetch = FetchType.LAZY)  // ✅ Good
    private List<Address> addresses;
    
    @OneToOne(fetch = FetchType.LAZY)  // ✅ Good
    private UserProfile profile;
}

// Query user (only need username)
User user = userRepository.findById(1L).get();
System.out.println(user.getUsername());
// SQL: SELECT * FROM users WHERE id = 1
// Result: 1 query! Perfect!

// Query user with orders (explicit)
@Query("SELECT u FROM User u LEFT JOIN FETCH u.orders WHERE u.id = :id")
User userWithOrders = userRepository.findByIdWithOrders(1L);
// SQL: SELECT u.*, o.* FROM users u LEFT JOIN orders o ON u.id = o.user_id WHERE u.id = 1
// Result: 1 query with join! Efficient!
```

---

#### 📌 Performance Comparison

**Scenario:** Fetch 10 users

**With EAGER Fetching:**
```java
@OneToMany(fetch = FetchType.EAGER)
private List<Order> orders;

List<User> users = userRepository.findAll();  // 10 users
// SQL executed:
// 1. SELECT * FROM users
// 2. SELECT * FROM orders WHERE user_id = 1
// 3. SELECT * FROM orders WHERE user_id = 2
// ...
// 11. SELECT * FROM orders WHERE user_id = 10
// Total: 11 queries!
```

**With LAZY Fetching:**
```java
@OneToMany(fetch = FetchType.LAZY)
private List<Order> orders;

List<User> users = userRepository.findAll();  // 10 users
System.out.println(users.get(0).getUsername());
// SQL: SELECT * FROM users
// Total: 1 query! (orders not loaded)
```

**With LAZY + JOIN FETCH (Best!):**
```java
@Query("SELECT DISTINCT u FROM User u LEFT JOIN FETCH u.orders")
List<User> findAllWithOrders();

List<User> users = userRepository.findAllWithOrders();
// SQL: SELECT u.*, o.* FROM users u LEFT JOIN orders o ON u.id = o.user_id
// Total: 1 query! (everything loaded efficiently)
```

---

### 🔹 N+1 Problem ⭐ CRITICAL INTERVIEW TOPIC

The **N+1 Problem** is one of the **most common performance issues** in JPA applications!

---

#### 📌 What is the N+1 Problem?

**Problem Definition:**
- Execute **1 query** to fetch parent entities (e.g., all users)
- Execute **N queries** to fetch related entities for each parent (e.g., orders for each user)
- Total: **1 + N queries** instead of **1 query**!

---

#### 📌 Example: The N+1 Problem

**Setup:**
```java
@Entity
public class User {
    @Id
    private Long id;
    private String username;
    
    @OneToMany(mappedBy = "user", fetch = FetchType.LAZY)
    private List<Order> orders;
}
```

**Code with N+1 Problem:**
```java
// Fetch all users
List<User> users = userRepository.findAll();  // 1 query

// Process each user's orders
for (User user : users) {  // Loop N times
    System.out.println(user.getUsername());
    System.out.println(user.getOrders().size());  // N queries (one per user)!
}
```

**SQL Executed:**
```sql
-- Query 1: Fetch all users
SELECT * FROM users

-- Query 2-11: Fetch orders for each user (N queries!)
SELECT * FROM orders WHERE user_id = 1
SELECT * FROM orders WHERE user_id = 2
SELECT * FROM orders WHERE user_id = 3
SELECT * FROM orders WHERE user_id = 4
SELECT * FROM orders WHERE user_id = 5
SELECT * FROM orders WHERE user_id = 6
SELECT * FROM orders WHERE user_id = 7
SELECT * FROM orders WHERE user_id = 8
SELECT * FROM orders WHERE user_id = 9
SELECT * FROM orders WHERE user_id = 10

-- Total: 1 + 10 = 11 queries!
```

**Impact:**
- 10 users = 11 queries
- 100 users = 101 queries ❌
- 1000 users = 1001 queries ❌❌❌
- **Extremely slow for large datasets!**

---

#### 📌 Solution 1: JOIN FETCH (Best Solution ✅)

**JOIN FETCH** fetches parent and children in a **single query** using SQL JOIN.

```java
// Repository method
@Query("SELECT DISTINCT u FROM User u LEFT JOIN FETCH u.orders")
List<User> findAllWithOrders();

// Or for specific user
@Query("SELECT u FROM User u LEFT JOIN FETCH u.orders WHERE u.id = :id")
Optional<User> findByIdWithOrders(@Param("id") Long id);
```

**Usage:**
```java
List<User> users = userRepository.findAllWithOrders();

for (User user : users) {
    System.out.println(user.getUsername());
    System.out.println(user.getOrders().size());  // No additional query!
}
```

**SQL Executed:**
```sql
-- Single query with JOIN!
SELECT DISTINCT u.*, o.*
FROM users u
LEFT JOIN orders o ON u.id = o.user_id

-- Total: 1 query! Perfect!
```

**Why LEFT JOIN FETCH?**
- `LEFT JOIN` - Includes users even if they have no orders
- `FETCH` - Tells JPA to populate the `orders` collection
- `DISTINCT` - Removes duplicate users (caused by JOIN)

---

#### 📌 Solution 2: @EntityGraph

**@EntityGraph** is an annotation-based way to specify eager fetching.

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    
    @EntityGraph(attributePaths = {"orders"})
    List<User> findAll();
    
    @EntityGraph(attributePaths = {"orders"})
    Optional<User> findById(Long id);
    
    // Multiple relationships
    @EntityGraph(attributePaths = {"orders", "addresses", "profile"})
    @Query("SELECT u FROM User u WHERE u.id = :id")
    Optional<User> findByIdWithAllRelationships(@Param("id") Long id);
}
```

**Usage:**
```java
List<User> users = userRepository.findAll();
// Automatically fetches orders with users in one query!
```

**Pros:**
- ✅ No need to write JOIN FETCH query
- ✅ Clean, annotation-based
- ✅ Can specify multiple relationships

**Cons:**
- ❌ Less control than custom queries
- ❌ Always uses LEFT OUTER JOIN (cannot use INNER JOIN)

---

#### 📌 Solution 3: Batch Fetching

**Batch Fetching** reduces N queries to **ceil(N/batch_size)** queries.

```properties
# application.properties
spring.jpa.properties.hibernate.default_batch_fetch_size=10
```

**How it works:**
```java
List<User> users = userRepository.findAll();  // 100 users

for (User user : users) {
    user.getOrders().size();  // Triggers batch loading
}
```

**SQL Executed with Batch Size 10:**
```sql
-- Query 1: Fetch all users
SELECT * FROM users

-- Query 2: Fetch orders for users 1-10 (batch!)
SELECT * FROM orders WHERE user_id IN (1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

-- Query 3: Fetch orders for users 11-20 (batch!)
SELECT * FROM orders WHERE user_id IN (11, 12, 13, 14, 15, 16, 17, 18, 19, 20)

-- ... continues in batches of 10

-- Total: 1 + ceil(100/10) = 1 + 10 = 11 queries
-- Much better than 1 + 100 = 101 queries!
```

**Pros:**
- ✅ Easy to configure (global setting)
- ✅ Works for all relationships automatically
- ✅ Reduces queries significantly

**Cons:**
- ❌ Still multiple queries (not as good as JOIN FETCH)
- ❌ Can be tricky to debug

---

#### 📌 Solution 4: DTO Projections

**Avoid entities entirely** - fetch only what you need into DTOs.

```java
// DTO
public class UserSummaryDTO {
    private Long id;
    private String username;
    private Long orderCount;
    
    public UserSummaryDTO(Long id, String username, Long orderCount) {
        this.id = id;
        this.username = username;
        this.orderCount = orderCount;
    }
    
    // Getters
}

// Repository
@Query("SELECT new com.example.dto.UserSummaryDTO(u.id, u.username, COUNT(o)) " +
       "FROM User u LEFT JOIN u.orders o GROUP BY u.id, u.username")
List<UserSummaryDTO> findAllUserSummaries();
```

**SQL Executed:**
```sql
SELECT u.id, u.username, COUNT(o.id)
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.id, u.username

-- Single efficient query!
```

**Pros:**
- ✅ Most efficient (only needed columns)
- ✅ Single query
- ✅ No entity overhead

**Cons:**
- ❌ More code (DTOs, constructors)
- ❌ Lose entity features (change tracking, etc.)

---

#### 📌 Solution 5: Subselect Fetching

```java
@Entity
public class User {
    @Id
    private Long id;
    
    @OneToMany(mappedBy = "user")
    @Fetch(FetchMode.SUBSELECT)  // Hibernate annotation
    private List<Order> orders;
}
```

**How it works:**
```java
List<User> users = userRepository.findAll();

for (User user : users) {
    user.getOrders().size();
}
```

**SQL Executed:**
```sql
-- Query 1: Fetch all users
SELECT * FROM users

-- Query 2: Fetch ALL orders for ALL users in one subquery
SELECT * FROM orders
WHERE user_id IN (SELECT id FROM users)

-- Total: 2 queries (regardless of number of users!)
```

---

#### 📌 Comparison of Solutions

| Solution | Queries | Pros | Cons | Best For |
|----------|---------|------|------|----------|
| **JOIN FETCH** | 1 | ✅ Most efficient<br>✅ Full control | ❌ Requires custom query | **Most cases** |
| **@EntityGraph** | 1 | ✅ Annotation-based<br>✅ Simple | ❌ Less flexible | **Simple cases** |
| **Batch Fetching** | 1 + N/batch_size | ✅ Easy config<br>✅ Global | ❌ Still multiple queries | **Quick fix** |
| **DTO Projection** | 1 | ✅ Most efficient<br>✅ Only needed data | ❌ More code | **Read-only data** |
| **Subselect** | 2 | ✅ Simple<br>✅ Automatic | ❌ Not standard JPA | **Hibernate only** |

---

#### 📌 Real-World Example: E-Commerce Orders

**Problem:**
```java
// Display all orders with customer names
List<Order> orders = orderRepository.findAll();  // 1 query

for (Order order : orders) {
    // N+1 problem!
    System.out.println(order.getCustomer().getName());  // N queries
}
```

**Solution with JOIN FETCH:**
```java
@Query("SELECT o FROM Order o JOIN FETCH o.customer")
List<Order> findAllWithCustomers();

List<Order> orders = orderRepository.findAllWithCustomers();  // 1 query

for (Order order : orders) {
    System.out.println(order.getCustomer().getName());  // No additional query!
}
```

---

#### 📌 How to Detect N+1 Problems

**1. Enable SQL Logging:**
```properties
# application.properties
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
logging.level.org.hibernate.type.descriptor.sql.BasicBinder=TRACE
```

**2. Look for Pattern:**
```
Hibernate: select * from users
Hibernate: select * from orders where user_id = ?
Hibernate: select * from orders where user_id = ?  ← N+1 problem!
Hibernate: select * from orders where user_id = ?
```

**3. Use Performance Monitoring Tools:**
- Spring Boot Actuator
- Hibernate Statistics
- APM tools (New Relic, AppDynamics)
- Database query logs

---

#### 📌 Interview Tips for N+1 Problem

**Must Know:**
1. **Definition**: 1 query for parent + N queries for children
2. **Cause**: LAZY fetching + accessing relationships in loop
3. **Solution**: JOIN FETCH or @EntityGraph
4. **Detection**: Enable SQL logging, look for repeated queries

**Interview Answer Template:**
```
"The N+1 problem occurs when fetching parent entities executes 1 query,
then accessing their LAZY relationships in a loop triggers N additional queries.

Example: Fetching 100 users, then accessing orders for each user results in 
101 queries instead of 1.

Best solution is using JOIN FETCH in JPQL:
@Query("SELECT u FROM User u LEFT JOIN FETCH u.orders")

This fetches everything in a single query using SQL JOIN, eliminating
the performance issue."
```

**Interview Tip:** Always mention "N+1 problem" when discussing FetchType.LAZY!

---

## 5.4 Transactions

### 🔹 @Transactional ⭐ CRITICAL

```java
@Service
public class UserService {
    
    private final UserRepository userRepository;
    private final OrderRepository orderRepository;
    
    // Simple transaction
    @Transactional
    public User createUser(User user) {
        return userRepository.save(user);
        // Commits if successful, rolls back on exception
    }
    
    // Multiple operations in one transaction
    @Transactional
    public void transferFunds(Long fromId, Long toId, BigDecimal amount) {
        User fromUser = userRepository.findById(fromId)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        User toUser = userRepository.findById(toId)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
        
        fromUser.setBalance(fromUser.getBalance().subtract(amount));
        toUser.setBalance(toUser.getBalance().add(amount));
        
        userRepository.save(fromUser);
        userRepository.save(toUser);
        // Both updates happen in same transaction - all or nothing!
    }
    
    // Read-only transaction (optimization)
    @Transactional(readOnly = true)
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }
}
```

---

### 🔹 Propagation Levels

```java
@Service
public class OrderService {
    
    // REQUIRED (default) - Use existing transaction or create new
    @Transactional(propagation = Propagation.REQUIRED)
    public void method1() { }
    
    // REQUIRES_NEW - Always create new transaction (suspend existing)
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void method2() { }
    
    // MANDATORY - Must have existing transaction (else exception)
    @Transactional(propagation = Propagation.MANDATORY)
    public void method3() { }
    
    // NEVER - Must NOT have transaction (else exception)
    @Transactional(propagation = Propagation.NEVER)
    public void method4() { }
    
    // SUPPORTS - Use transaction if exists, else non-transactional
    @Transactional(propagation = Propagation.SUPPORTS)
    public void method5() { }
}
```

**Most Common:** REQUIRED (default) and REQUIRES_NEW

---

### 🔹 Isolation Levels

```java
@Service
public class OrderService {
    
    // READ_UNCOMMITTED - Lowest isolation, dirty reads possible
    @Transactional(isolation = Isolation.READ_UNCOMMITTED)
    public void method1() { }
    
    // READ_COMMITTED - Prevents dirty reads (default in most DBs)
    @Transactional(isolation = Isolation.READ_COMMITTED)
    public void method2() { }
    
    // REPEATABLE_READ - Prevents dirty + non-repeatable reads
    @Transactional(isolation = Isolation.REPEATABLE_READ)
    public void method3() { }
    
    // SERIALIZABLE - Highest isolation, prevents all anomalies
    @Transactional(isolation = Isolation.SERIALIZABLE)
    public void method4() { }
}
```

**Trade-off:** Higher isolation = Better consistency but Lower performance

---

## 5.5 Database Migration

### 🔹 Flyway (Recommended)

```xml
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-core</artifactId>
</dependency>
<dependency>
    <groupId>org.flywaydb</groupId>
    <artifactId>flyway-mysql</artifactId>
</dependency>
```

```properties
spring.flyway.enabled=true
spring.flyway.baseline-on-migrate=true
spring.jpa.hibernate.ddl-auto=validate  # Don't let Hibernate manage schema!
```

**Migration Files: src/main/resources/db/migration/**

```sql
-- V1__create_users_table.sql
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- V2__add_age_column.sql
ALTER TABLE users ADD COLUMN age INT;

-- V3__create_orders_table.sql
CREATE TABLE orders (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT NOT NULL,
    total_amount DECIMAL(10,2),
    status VARCHAR(20),
    FOREIGN KEY (user_id) REFERENCES users(id)
);
```

**Naming Convention:** `V{version}__{description}.sql`

---

### 🔹 Liquibase (Alternative)

```xml
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
</dependency>
```

**db/changelog/db.changelog-master.yaml:**
```yaml
databaseChangeLog:
  - changeSet:
      id: 1
      author: developer
      changes:
        - createTable:
            tableName: users
            columns:
              - column:
                  name: id
                  type: BIGINT
                  autoIncrement: true
                  constraints:
                    primaryKey: true
              - column:
                  name: username
                  type: VARCHAR(50)
                  constraints:
                    nullable: false
                    unique: true
```

---

## 🔥 Common Interview Questions

### Q1: What is JPA and Hibernate?
**A:** 
- **JPA** - Java Persistence API (specification/interface)
- **Hibernate** - ORM framework that implements JPA (most popular implementation)

### Q2: What does @Transactional do?
**A:** Marks method for transaction management - commits on success, rolls back on exception. Ensures ACID properties for database operations.

### Q3: Explain N+1 problem and solutions
**A:** Fetching parent entity triggers 1 query, then N queries for children. Solutions:
- JOIN FETCH in JPQL
- @EntityGraph
- Batch fetching

### Q4: Lazy vs Eager loading?
**A:**
- **LAZY** - Loads data only when accessed (better performance, recommended)
- **EAGER** - Loads data immediately (can cause performance issues)

### Q5: Difference between save() and saveAndFlush()?
**A:**
- `save()` - Persists to persistence context (may not hit DB immediately)
- `saveAndFlush()` - Persists AND immediately flushes to database

### Q6: What is CascadeType?
**A:** Defines operations that cascade from parent to child:
- `ALL` - All operations
- `PERSIST` - Save operations
- `MERGE` - Update operations
- `REMOVE` - Delete operations
- `REFRESH` - Refresh from DB

### Q7: What is orphanRemoval?
**A:** When set to true, automatically deletes child entities when removed from parent's collection.

### Q8: JPQL vs Native SQL?
**A:**
- **JPQL** - Works with entities, database-independent
- **Native SQL** - Direct SQL, database-specific, better for complex queries

---

## 📝 Quick Revision Checklist

- [ ] Understand JpaRepository methods
- [ ] Master query method naming conventions
- [ ] Know @Query with JPQL and native SQL
- [ ] Understand all relationship types (@OneToOne, @OneToMany, @ManyToOne, @ManyToMany)
- [ ] Know LAZY vs EAGER loading
- [ ] Understand N+1 problem and solutions
- [ ] Master @Transactional annotation
- [ ] Know propagation and isolation levels
- [ ] Understand CascadeType and orphanRemoval
- [ ] Know Flyway/Liquibase basics

---

## 💻 Practice Exercise

Create a complete JPA application:
1. Entities: User, Order, Product with proper relationships
2. Repository with custom query methods
3. Service layer with @Transactional
4. Solve N+1 problem with JOIN FETCH
5. Implement pagination and sorting
6. Add Flyway migrations
7. Write integration tests for repositories

**This topic is CRITICAL - 85% of backend roles require strong JPA/Hibernate knowledge!**
