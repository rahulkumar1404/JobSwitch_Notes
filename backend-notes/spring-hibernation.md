# Hibernate Query Execution Notes  
## Hibernate mein Query Execute Kaise Hota Hai - Complete Notes

---

## Table of Contents

1. [Hibernate Query Execution Overview](#1-hibernate-query-execution-overview)
2. [Hibernate Architecture](#2-hibernate-architecture)
3. [Important Hibernate Objects](#3-important-hibernate-objects)
4. [Hibernate Query Execution Flow](#4-hibernate-query-execution-flow)
5. [SessionFactory](#5-sessionfactory)
6. [Session](#6-session)
7. [Transaction](#7-transaction)
8. [Hibernate Query Types](#8-hibernate-query-types)
9. [HQL Query Execution](#9-hql-query-execution)
10. [Native SQL Query Execution](#10-native-sql-query-execution)
11. [Criteria API Query Execution](#11-criteria-api-query-execution)
12. [Named Query Execution](#12-named-query-execution)
13. [Query Parameters](#13-query-parameters)
14. [Select Query Execution](#14-select-query-execution)
15. [Insert Operation in Hibernate](#15-insert-operation-in-hibernate)
16. [Update Operation in Hibernate](#16-update-operation-in-hibernate)
17. [Delete Operation in Hibernate](#17-delete-operation-in-hibernate)
18. [Hibernate First Level Cache](#18-hibernate-first-level-cache)
19. [Hibernate Second Level Cache](#19-hibernate-second-level-cache)
20. [Dirty Checking](#20-dirty-checking)
21. [Flush in Hibernate](#21-flush-in-hibernate)
22. [Lazy Loading and Query Execution](#22-lazy-loading-and-query-execution)
23. [N+1 Query Problem](#23-n1-query-problem)
24. [Query Pagination](#24-query-pagination)
25. [Query Performance Tips](#25-query-performance-tips)
26. [Hibernate Query Execution Interview Questions](#26-hibernate-query-execution-interview-questions)

---

# 1. Hibernate Query Execution Overview

## Hinglish Explanation

Hibernate ek ORM framework hai.

ORM ka full form hota hai:

> Object Relational Mapping

Hibernate Java objects ko database tables ke saath map karta hai.

Simple words mein:

- Java class = Database table
- Java object = Table row
- Class field = Table column

Hibernate ke through query execute karne ke liye mostly ye steps follow hote hain:

1. Configuration load hoti hai
2. SessionFactory create hota hai
3. Session open hota hai
4. Transaction start hoti hai
5. Query create hoti hai
6. Query execute hoti hai
7. Result return hota hai
8. Transaction commit hoti hai
9. Session close hota hai

---

## English Model Answer

Hibernate executes queries through the `Session` object. The application creates a query using HQL, Criteria API, or native SQL. Hibernate translates HQL or Criteria queries into SQL, sends the SQL to the database using JDBC, maps the result set back to Java objects, and returns the result to the application.

---

# 2. Hibernate Architecture

## Hinglish Explanation

Hibernate internally JDBC use karta hai. Developer directly JDBC code nahi likhta, Hibernate woh kaam internally handle karta hai.

## Architecture Flow

```text
Java Application
       |
       v
Hibernate API
       |
       v
SessionFactory
       |
       v
Session
       |
       v
Transaction
       |
       v
Query / HQL / Criteria
       |
       v
Hibernate Engine
       |
       v
JDBC
       |
       v
Database
```

## Simple Explanation

Jab hum Hibernate query execute karte hain:

```java
session.createQuery("from Student", Student.class).list();
```

Hibernate internally:

1. HQL parse karta hai
2. SQL generate karta hai
3. JDBC PreparedStatement banata hai
4. Database ko query bhejta hai
5. ResultSet receive karta hai
6. ResultSet ko Java objects mein convert karta hai
7. List return karta hai

---

# 3. Important Hibernate Objects

## 1. Configuration

Hibernate configuration load karta hai.

```java
Configuration configuration = new Configuration();
configuration.configure("hibernate.cfg.xml");
```

## 2. SessionFactory

SessionFactory heavy object hota hai. Ye application mein usually ek hi baar create hota hai.

```java
SessionFactory sessionFactory = configuration.buildSessionFactory();
```

## 3. Session

Session database ke saath connection represent karta hai.

```java
Session session = sessionFactory.openSession();
```

## 4. Transaction

Transaction data consistency maintain karta hai.

```java
Transaction transaction = session.beginTransaction();
```

## 5. Query

Query database operation perform karne ke liye use hoti hai.

```java
Query<Student> query = session.createQuery("from Student", Student.class);
```

---

# 4. Hibernate Query Execution Flow

## Complete Flow

```text
Step 1: Application query request karta hai
Step 2: Session query create karta hai
Step 3: Hibernate HQL/Criteria ko SQL mein convert karta hai
Step 4: Hibernate JDBC PreparedStatement create karta hai
Step 5: SQL database ko send hota hai
Step 6: Database query execute karta hai
Step 7: Database ResultSet return karta hai
Step 8: Hibernate ResultSet ko entity objects mein map karta hai
Step 9: Objects first-level cache mein store hote hain
Step 10: Result application ko return hota hai
```

## Example

```java
Session session = sessionFactory.openSession();

List<Student> students = session
        .createQuery("from Student", Student.class)
        .list();

session.close();
```

## Internally Kya Hota Hai?

```text
HQL: from Student

Hibernate converts it to:

SQL: select * from students
```

Then:

```text
Database ResultSet
       |
       v
Hibernate Entity Mapping
       |
       v
List<Student>
```

---

# 5. SessionFactory

## Hinglish Explanation

`SessionFactory` Hibernate ka heavy object hai. Isme database configuration, mapping metadata, cache configuration sab hota hai.

Usually ek application mein ek database ke liye ek `SessionFactory` hota hai.

## Example

```java
SessionFactory sessionFactory = new Configuration()
        .configure()
        .buildSessionFactory();
```

## Important Points

- Thread-safe hota hai
- Heavy object hota hai
- Application startup par create karna chahiye
- Baar-baar create nahi karna chahiye
- Session create karne ke liye use hota hai

## English Model Answer

`SessionFactory` is a heavyweight, thread-safe object used to create Hibernate `Session` instances. It is usually created once during application startup and reused throughout the application.

---

# 6. Session

## Hinglish Explanation

`Session` Hibernate ka main object hai jiske through database operations perform hote hain.

Session:

- Query create karta hai
- Entity save/update/delete karta hai
- First-level cache maintain karta hai
- Persistence context represent karta hai

## Example

```java
Session session = sessionFactory.openSession();
```

## Important Points

- Session lightweight hota hai
- Thread-safe nahi hota
- Har request ya transaction ke liye new session use karna chahiye
- Session ke andar first-level cache hota hai

## English Model Answer

Hibernate `Session` represents a single unit of work with the database. It is used to create queries, persist entities, retrieve data, delete records, and manage the first-level cache. It is not thread-safe and should not be shared between threads.

---

# 7. Transaction

## Hinglish Explanation

Transaction ek logical unit of work hota hai. Agar multiple database operations hain, to sab successful hone chahiye. Agar koi ek fail ho jaye, to rollback hona chahiye.

## Example

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

Student student = new Student();
student.setName("Rahul");

session.persist(student);

tx.commit();
session.close();
```

## Transaction Flow

```text
beginTransaction()
       |
       v
Database operations
       |
       v
commit() or rollback()
```

## English Model Answer

A transaction represents a unit of work. Hibernate uses transactions to ensure data consistency. If all operations are successful, the transaction is committed. If an error occurs, the transaction should be rolled back.

---

# 8. Hibernate Query Types

Hibernate mein query execute karne ke mainly 4 ways hote hain:

| Query Type | Meaning |
|---|---|
| HQL | Hibernate Query Language |
| Native SQL | Direct SQL query |
| Criteria API | Programmatic query |
| Named Query | Predefined query |

---

# 9. HQL Query Execution

## Hinglish Explanation

HQL ka full form hai:

> Hibernate Query Language

HQL SQL jaisa hota hai, but database table ke naam ke bajaye entity class ke naam use karta hai.

## SQL vs HQL

### SQL

```sql
SELECT * FROM students;
```

### HQL

```java
from Student
```

Yaha `Student` table ka naam nahi, entity class ka naam hai.

---

## HQL Select Query Example

```java
Session session = sessionFactory.openSession();

Query<Student> query = session.createQuery("from Student", Student.class);
List<Student> students = query.list();

session.close();
```

## Execution Flow

```text
HQL: from Student
       |
       v
Hibernate Parser
       |
       v
SQL: select s.id, s.name, s.email from students s
       |
       v
JDBC PreparedStatement
       |
       v
Database
       |
       v
ResultSet
       |
       v
List<Student>
```

---

## HQL With Where Clause

```java
Query<Student> query = session.createQuery(
        "from Student where city = :city",
        Student.class
);

query.setParameter("city", "Delhi");

List<Student> students = query.list();
```

## HQL Update Query

```java
Transaction tx = session.beginTransaction();

Query query = session.createQuery(
        "update Student set city = :city where id = :id"
);

query.setParameter("city", "Mumbai");
query.setParameter("id", 1L);

int rows = query.executeUpdate();

tx.commit();
```

## HQL Delete Query

```java
Transaction tx = session.beginTransaction();

Query query = session.createQuery(
        "delete from Student where id = :id"
);

query.setParameter("id", 1L);

int rows = query.executeUpdate();

tx.commit();
```

---

# 10. Native SQL Query Execution

## Hinglish Explanation

Native SQL ka matlab hai direct database SQL query execute karna.

Agar aapko database-specific query likhni hai, jaise MySQL/PostgreSQL specific function, tab native SQL use kar sakte ho.

## Example

```java
NativeQuery<Student> query = session.createNativeQuery(
        "SELECT * FROM students WHERE city = :city",
        Student.class
);

query.setParameter("city", "Delhi");

List<Student> students = query.list();
```

## Native SQL Execution Flow

```text
Native SQL
       |
       v
Hibernate
       |
       v
JDBC PreparedStatement
       |
       v
Database
       |
       v
ResultSet
       |
       v
Entity Mapping
       |
       v
Java Objects
```

## English Model Answer

Native SQL queries allow developers to execute database-specific SQL directly through Hibernate. Hibernate sends the SQL to the database using JDBC and maps the result to entity objects if an entity class is provided.

---

# 11. Criteria API Query Execution

## Hinglish Explanation

Criteria API query ko Java code ke through build karne ka tareeka hai. Isme query string nahi likhte, Java objects use karte hain.

Ye type-safe hota hai.

## Example

```java
CriteriaBuilder builder = session.getCriteriaBuilder();

CriteriaQuery<Student> criteria = builder.createQuery(Student.class);

Root<Student> root = criteria.from(Student.class);

criteria.select(root);

Query<Student> query = session.createQuery(criteria);

List<Student> students = query.getResultList();
```

## Where Condition

```java
CriteriaBuilder builder = session.getCriteriaBuilder();

CriteriaQuery<Student> criteria = builder.createQuery(Student.class);

Root<Student> root = criteria.from(Student.class);

criteria.select(root)
        .where(builder.equal(root.get("city"), "Delhi"));

List<Student> students = session.createQuery(criteria).getResultList();
```

## Execution Flow

```text
Criteria API Java Object Query
       |
       v
Hibernate converts to HQL/SQL
       |
       v
SQL generated
       |
       v
Database execution
       |
       v
Result mapped to entity objects
```

## English Model Answer

Criteria API is a programmatic and type-safe way to create queries in Hibernate. Hibernate converts Criteria queries into SQL and executes them through JDBC.

---

# 12. Named Query Execution

## Hinglish Explanation

Named Query predefined query hoti hai jo entity class ke upar define karte hain. Iska benefit hai query ek hi jagah maintain hoti hai.

## Entity Example

```java
@Entity
@NamedQuery(
        name = "Student.findByCity",
        query = "from Student where city = :city"
)
public class Student {

    @Id
    private Long id;

    private String name;

    private String city;
}
```

## Execution

```java
Query<Student> query = session.createNamedQuery(
        "Student.findByCity",
        Student.class
);

query.setParameter("city", "Delhi");

List<Student> students = query.list();
```

## English Model Answer

A named query is a predefined query declared using annotations or XML. It can be reused by name and is validated during application startup.

---

# 13. Query Parameters

## Hinglish Explanation

Query mein dynamic value pass karne ke liye parameters use karte hain.

Parameters SQL injection se protect karne mein help karte hain.

## Named Parameter

```java
Query<Student> query = session.createQuery(
        "from Student where email = :email",
        Student.class
);

query.setParameter("email", "test@gmail.com");

Student student = query.uniqueResult();
```

## Positional Parameter

```java
Query<Student> query = session.createQuery(
        "from Student where email = ?1",
        Student.class
);

query.setParameter(1, "test@gmail.com");

Student student = query.uniqueResult();
```

## Best Practice

Named parameters use karna better hota hai because readability achi hoti hai.

---

# 14. Select Query Execution

## `list()`

Multiple records ke liye use hota hai.

```java
List<Student> students = session
        .createQuery("from Student", Student.class)
        .list();
```

## `getResultList()`

JPA style method hai.

```java
List<Student> students = session
        .createQuery("from Student", Student.class)
        .getResultList();
```

## `uniqueResult()`

Single result ke liye use hota hai.

```java
Student student = session
        .createQuery("from Student where id = :id", Student.class)
        .setParameter("id", 1L)
        .uniqueResult();
```

## `getSingleResult()`

JPA style single result method.

```java
Student student = session
        .createQuery("from Student where id = :id", Student.class)
        .setParameter("id", 1L)
        .getSingleResult();
```

## Difference

| Method | Use |
|---|---|
| `list()` | Multiple records |
| `getResultList()` | Multiple records, JPA style |
| `uniqueResult()` | Single result, Hibernate style |
| `getSingleResult()` | Single result, JPA style |

---

# 15. Insert Operation in Hibernate

## Hinglish Explanation

Hibernate mein insert ke liye direct insert query usually nahi likhte. Entity object create karke `persist()` ya `save()` use karte hain.

## Example

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

Student student = new Student();
student.setName("Amit");
student.setCity("Delhi");

session.persist(student);

tx.commit();
session.close();
```

## Internally Kya Hota Hai?

```text
Student object created
       |
       v
session.persist(student)
       |
       v
Object becomes persistent
       |
       v
Hibernate generates INSERT SQL
       |
       v
SQL executes on flush/commit
```

## Generated SQL

```sql
INSERT INTO students (name, city) VALUES (?, ?);
```

## `save()` vs `persist()`

| Method | Meaning |
|---|---|
| `save()` | Hibernate-specific, returns generated id |
| `persist()` | JPA method, does not return id |

---

# 16. Update Operation in Hibernate

## Hinglish Explanation

Hibernate mein update ka sabse common way hai object fetch karo, field change karo, transaction commit karo. Hibernate automatically update query execute karega because of dirty checking.

## Example

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

Student student = session.get(Student.class, 1L);

student.setCity("Mumbai");

tx.commit();
session.close();
```

## Internally Kya Hota Hai?

```text
Student object loaded
       |
       v
Object is in persistent state
       |
       v
Field value changed
       |
       v
Hibernate dirty checking detects change
       |
       v
UPDATE SQL generated during flush/commit
```

## Generated SQL

```sql
UPDATE students SET city = ? WHERE id = ?;
```

---

# 17. Delete Operation in Hibernate

## Example

```java
Session session = sessionFactory.openSession();
Transaction tx = session.beginTransaction();

Student student = session.get(Student.class, 1L);

session.remove(student);

tx.commit();
session.close();
```

## Internally Kya Hota Hai?

```text
Entity loaded
       |
       v
session.remove(entity)
       |
       v
Entity marked for deletion
       |
       v
DELETE SQL executes during flush/commit
```

## Generated SQL

```sql
DELETE FROM students WHERE id = ?;
```

---

# 18. Hibernate First Level Cache

## Hinglish Explanation

First-level cache Session ke andar hota hai. Ye default enabled hota hai.

Agar same session mein same entity ko same id se multiple baar fetch karte ho, Hibernate database ko baar-baar query nahi bhejta.

## Example

```java
Session session = sessionFactory.openSession();

Student s1 = session.get(Student.class, 1L);
Student s2 = session.get(Student.class, 1L);

session.close();
```

## Query Kitni Baar Execute Hogi?

Only one time.

First call:

```text
Database query execute hogi
```

Second call:

```text
First-level cache se object milega
```

## English Model Answer

First-level cache is associated with the Hibernate `Session`. It is enabled by default and ensures that the same entity is not loaded multiple times from the database within the same session.

---

# 19. Hibernate Second Level Cache

## Hinglish Explanation

Second-level cache SessionFactory level par hota hai. Ye multiple sessions ke beech data share kar sakta hai.

By default enabled nahi hota. Iske liye Ehcache, Redis, Hazelcast jaise providers use karne padte hain.

## Flow

```text
Session 1 loads entity from DB
       |
       v
Entity stored in second-level cache
       |
       v
Session 2 requests same entity
       |
       v
Hibernate checks second-level cache
       |
       v
If found, DB query avoid hoti hai
```

## English Model Answer

Second-level cache is associated with the `SessionFactory` and can be shared across multiple sessions. It is not enabled by default and requires a cache provider.

---

# 20. Dirty Checking

## Hinglish Explanation

Dirty checking Hibernate ka automatic mechanism hai jisme Hibernate persistent object ke changes detect karta hai aur update query automatically generate karta hai.

## Example

```java
Transaction tx = session.beginTransaction();

Student student = session.get(Student.class, 1L);

student.setName("New Name");

tx.commit();
```

Yaha humne `update()` call nahi kiya, fir bhi update query execute hogi.

## Why?

Because object persistent state mein hai.

## Flow

```text
Entity loaded
       |
       v
Hibernate stores snapshot
       |
       v
Entity modified
       |
       v
Hibernate compares current state with snapshot
       |
       v
Change found
       |
       v
UPDATE query generated
```

## English Model Answer

Dirty checking is Hibernate's automatic mechanism to detect changes in persistent entities. During flush, Hibernate compares the current entity state with its original snapshot and generates update SQL if changes are detected.

---

# 21. Flush in Hibernate

## Hinglish Explanation

Flush ka matlab hai Hibernate memory ke changes ko database ke saath synchronize karta hai.

Important point:

> Flush SQL execute karta hai, but transaction commit nahi karta.

## Example

```java
Transaction tx = session.beginTransaction();

Student student = new Student();
student.setName("Ravi");

session.persist(student);

session.flush();

tx.commit();
```

## Flush Kab Hota Hai?

Hibernate flush kar sakta hai:

1. Transaction commit ke time
2. Query execute hone se pehle
3. Manually `session.flush()` call karne par

## Flush vs Commit

| Flush | Commit |
|---|---|
| SQL database ko send karta hai | Transaction permanently save karta hai |
| Rollback possible hai | Commit ke baad rollback possible nahi |
| Session-level sync hai | Transaction-level finalization hai |

## English Model Answer

Flush synchronizes the persistence context with the database by executing SQL statements. However, it does not commit the transaction. The transaction can still be rolled back after flush.

---

# 22. Lazy Loading and Query Execution

## Hinglish Explanation

Lazy loading ka matlab hai related data tabhi load hoga jab actually access karoge.

## Example

```java
@Entity
public class Student {

    @OneToMany(fetch = FetchType.LAZY)
    private List<Course> courses;
}
```

## Query Execution

```java
Student student = session.get(Student.class, 1L);
```

Is time only student data load hoga.

```java
student.getCourses().size();
```

Is line par courses ke liye separate query execute hogi.

## Flow

```text
Load Student
       |
       v
Only student table query
       |
       v
Access courses
       |
       v
Courses table query executes
```

## English Model Answer

Lazy loading delays loading associated entities until they are accessed. Hibernate uses proxy objects or persistent collections to trigger additional queries when the lazy association is accessed.

---

# 23. N+1 Query Problem

## Hinglish Explanation

N+1 problem tab hoti hai jab ek query parent records load karti hai aur phir har parent ke liye child records load karne ke liye extra query execute hoti hai.

## Example

```java
List<Student> students = session
        .createQuery("from Student", Student.class)
        .list();

for (Student student : students) {
    System.out.println(student.getCourses().size());
}
```

Agar 10 students hain:

```text
1 query students ke liye
10 queries courses ke liye
Total = 11 queries
```

## Solution 1: Fetch Join

```java
List<Student> students = session.createQuery(
        "select distinct s from Student s join fetch s.courses",
        Student.class
).list();
```

## Solution 2: Entity Graph

```java
@EntityGraph(attributePaths = {"courses"})
List<Student> findAll();
```

## Solution 3: Batch Size

```java
@BatchSize(size = 10)
@OneToMany
private List<Course> courses;
```

## English Model Answer

The N+1 query problem occurs when Hibernate executes one query to load parent entities and then executes additional queries for each parent entity to load child associations. It can be solved using fetch joins, entity graphs, or batch fetching.

---

# 24. Query Pagination

## Hinglish Explanation

Pagination ka matlab hai large data ko small pages mein fetch karna.

## Example

```java
Query<Student> query = session.createQuery("from Student", Student.class);

query.setFirstResult(0);
query.setMaxResults(10);

List<Student> students = query.list();
```

## Meaning

```text
setFirstResult(0) = Starting row
setMaxResults(10) = Maximum 10 records
```

## SQL Generated

Database ke according SQL generate hota hai.

MySQL example:

```sql
SELECT * FROM students LIMIT 10 OFFSET 0;
```

## English Model Answer

Pagination in Hibernate is performed using `setFirstResult()` and `setMaxResults()`. Hibernate translates these into database-specific pagination SQL such as `LIMIT` and `OFFSET`.

---

# 25. Query Performance Tips

## Best Practices

1. Use parameterized queries.
2. Avoid N+1 queries.
3. Use fetch join carefully.
4. Use pagination for large datasets.
5. Select only required columns when possible.
6. Use DTO projections for read-only data.
7. Avoid unnecessary eager loading.
8. Use indexes on frequently searched columns.
9. Enable SQL logging during debugging.
10. Use second-level cache only when needed.
11. Keep transaction scope small.
12. Avoid loading huge object graphs.
13. Use batch insert/update for bulk operations.
14. Prefer `getReference()` when only proxy is needed.
15. Use `@Transactional(readOnly = true)` for read operations in Spring.

---

## DTO Projection Example

```java
Query<StudentDto> query = session.createQuery(
        "select new com.example.StudentDto(s.id, s.name) from Student s",
        StudentDto.class
);

List<StudentDto> students = query.list();
```

## Benefit

Entity ke saare columns load nahi honge. Sirf required data fetch hoga.

---

# 26. Hibernate Query Execution Interview Questions

---

## Q1. Hibernate query execute kaise hoti hai?

### English Model Answer

Hibernate executes queries through the `Session` object. HQL or Criteria queries are first parsed and converted into SQL. Hibernate then uses JDBC to send the SQL to the database. The database returns a ResultSet, and Hibernate maps the ResultSet into entity objects before returning them to the application.

---

## Q2. HQL and SQL mein kya difference hai?

### English Model Answer

SQL works with database tables and columns, while HQL works with entity classes and their properties. Hibernate converts HQL into database-specific SQL before execution.

---

## Q3. Session ka role kya hai?

### English Model Answer

The Hibernate `Session` is used to interact with the database. It creates queries, saves, updates, deletes, and retrieves entities. It also manages the first-level cache and persistence context.

---

## Q4. SessionFactory ka role kya hai?

### English Model Answer

`SessionFactory` is a heavyweight, thread-safe object used to create `Session` objects. It holds configuration, mapping metadata, and cache settings.

---

## Q5. Query execute karne ke liye transaction zaroori hai kya?

### English Model Answer

For read-only select queries, a transaction may not always be strictly required depending on the environment, but it is recommended. For insert, update, and delete operations, a transaction is required to ensure data consistency.

---

## Q6. `list()` and `getResultList()` mein difference kya hai?

### English Model Answer

Both methods are used to retrieve multiple results. `list()` is Hibernate-specific, while `getResultList()` is part of the JPA standard.

---

## Q7. `uniqueResult()` and `getSingleResult()` mein difference kya hai?

### English Model Answer

Both are used to retrieve a single result. `uniqueResult()` is Hibernate-specific and may return null if no result is found. `getSingleResult()` is JPA standard and throws an exception if no result or multiple results are found.

---

## Q8. Hibernate internally JDBC use karta hai kya?

### English Model Answer

Yes. Hibernate internally uses JDBC to communicate with the database. It abstracts JDBC boilerplate code from the developer.

---

## Q9. First-level cache kya hota hai?

### English Model Answer

First-level cache is associated with the Hibernate `Session`. It is enabled by default and stores entities loaded during a session to avoid repeated database queries for the same entity.

---

## Q10. Second-level cache kya hota hai?

### English Model Answer

Second-level cache is associated with the `SessionFactory` and can be shared across multiple sessions. It is not enabled by default and requires a cache provider.

---

## Q11. Dirty checking kya hota hai?

### English Model Answer

Dirty checking is Hibernate's mechanism to automatically detect changes in persistent entities. During flush, Hibernate compares the current state with the original snapshot and generates update queries if changes are found.

---

## Q12. Flush kya hota hai?

### English Model Answer

Flush synchronizes the persistence context with the database by executing SQL statements. It does not commit the transaction.

---

## Q13. Flush and commit mein difference kya hai?

### English Model Answer

Flush sends SQL statements to the database but does not permanently save changes. Commit finalizes the transaction and makes the changes permanent.

---

## Q14. Lazy loading mein query kab execute hoti hai?

### English Model Answer

In lazy loading, the query for associated data is executed only when the association is accessed for the first time.

---

## Q15. N+1 query problem kya hai?

### English Model Answer

The N+1 query problem occurs when Hibernate executes one query to load parent entities and then executes additional queries for each parent to load child entities.

---

## Q16. N+1 problem solve kaise karte hain?

### English Model Answer

The N+1 problem can be solved using fetch joins, entity graphs, batch fetching, or by changing the fetching strategy carefully.

---

## Q17. Native query kab use karni chahiye?

### English Model Answer

Native queries should be used when we need database-specific SQL features, complex optimized queries, or queries that are difficult to express in HQL or Criteria API.

---

## Q18. Parameterized query ka benefit kya hai?

### English Model Answer

Parameterized queries improve readability, prevent SQL injection, and allow Hibernate to use prepared statements efficiently.

---

## Q19. Hibernate mein insert query kab execute hoti hai?

### English Model Answer

Hibernate usually executes insert SQL during flush or transaction commit, depending on the ID generation strategy and flush mode.

---

## Q20. Hibernate mein update query automatically kaise execute hoti hai?

### English Model Answer

When an entity is in persistent state, Hibernate tracks its original state. If any field changes, Hibernate detects the change during flush and automatically generates an update query.

---

# Quick Revision

## Hibernate Query Execution Short Flow

```text
Session creates query
       |
       v
Hibernate parses query
       |
       v
SQL generated
       |
       v
JDBC PreparedStatement created
       |
       v
Database executes SQL
       |
       v
ResultSet returned
       |
       v
Hibernate maps result to entity
       |
       v
Java object/list returned
```

---

## Important Methods

| Method | Use |
|---|---|
| `openSession()` | New session create karta hai |
| `beginTransaction()` | Transaction start karta hai |
| `createQuery()` | HQL/JPQL query create karta hai |
| `createNativeQuery()` | Native SQL query create karta hai |
| `setParameter()` | Dynamic value bind karta hai |
| `list()` | Multiple result return karta hai |
| `getResultList()` | Multiple result, JPA style |
| `uniqueResult()` | Single result, Hibernate style |
| `getSingleResult()` | Single result, JPA style |
| `persist()` | Entity insert ke liye |
| `merge()` | Detached entity update ke liye |
| `remove()` | Entity delete ke liye |
| `flush()` | SQL execute/sync karta hai |
| `commit()` | Transaction permanently save karta hai |
| `rollback()` | Transaction undo karta hai |

---

## Final Interview Answer

Hibernate executes queries using the `Session` object. When we create an HQL or Criteria query, Hibernate parses it and converts it into database-specific SQL. Then Hibernate uses JDBC internally to execute the SQL on the database. The database returns a ResultSet, and Hibernate maps that ResultSet into Java entity objects. Hibernate also uses first-level cache, dirty checking, flush, and transaction management during query execution.

---
