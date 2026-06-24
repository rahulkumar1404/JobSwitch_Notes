# 3-Years Experience Java Backend Interview — Model Answers

> In-depth, interview-ready answers. Each answer is written so you can speak confidently and handle cross-questions without being caught off guard.

---

## Table of Contents
1. [Java / Spring Boot](#java--spring-boot)
2. [Hibernate / JPA / MySQL](#hibernate--jpa--mysql)
3. [Spring Security / OAuth2 / JWT](#spring-security--oauth2--jwt)
4. [Microservices / REST APIs](#microservices--rest-apis)
5. [Kafka](#kafka)
6. [React / JavaScript](#react--javascript)
7. [AWS / Docker / Kubernetes / CI-CD](#aws--docker--kubernetes--cicd)
8. [Testing / Clean Code / Practical](#testing--clean-code--practical)

---

## Java / Spring Boot

### 1. Difference between @Component, @Service, @Repository, and @Controller

All four are **stereotype annotations** built on top of `@Component`. They all register a class as a Spring bean in the application context. The difference is **semantic intent** plus some **technical behavior**:

| Annotation | Layer | Special Behavior |
|------------|-------|------------------|
| `@Component` | Generic | Base annotation; marks any class as a Spring-managed bean. |
| `@Service` | Business/Service layer | No extra behavior technically, but communicates business logic intent. AOP-friendly marker. |
| `@Repository` | Persistence/DAO layer | Adds **exception translation** — converts native persistence exceptions (like `SQLException`) into Spring's `DataAccessException` hierarchy. |
| `@Controller` | Web/Presentation layer | Marks the class as a web controller; works with `@RequestMapping` to handle HTTP requests and return views. |

**Key technical point (likely cross-question):** Only `@Repository` and `@Controller` add real functionality. `@Service` and `@Component` are technically identical at runtime. We separate them for **readability, layering, and to allow targeted AOP/pointcuts**.

> **Cross-question — "Can I use @Component instead of @Service everywhere?"**
> Yes, technically the app will work. But you lose clarity of layering, and you can't write clean pointcuts like "apply transactions to all @Service beans." It's a best practice, not a hard requirement.

---

### 2. How does Dependency Injection work in Spring? Why is it preferred?

**Dependency Injection (DI)** is a design pattern where an object's dependencies are **provided by an external container** (the Spring IoC container) instead of the object creating them itself.

**How Spring does it:**
1. Spring scans for beans (`@Component`, `@Service`, etc.).
2. It creates instances and stores them in the **ApplicationContext (IoC container)**.
3. When a bean needs another bean, Spring **injects** it — via constructor, setter, or field.

**Three types of injection:**
- **Constructor injection** (recommended) — dependencies passed via constructor. Ensures immutability and mandatory dependencies.
- **Setter injection** — for optional dependencies.
- **Field injection** (`@Autowired` on field) — convenient but discouraged (hard to test, hides dependencies).

```java
@Service
public class OrderService {
    private final PaymentService paymentService;

    // Constructor injection — preferred
    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

**Why preferred:**
- **Loose coupling** — class depends on abstractions, not concrete implementations.
- **Testability** — easy to inject mocks in unit tests.
- **Maintainability** — swap implementations without changing dependent code.
- **Single Responsibility** — class focuses on its job, not on building dependencies.

> **Cross-question — "Why is constructor injection preferred over field injection?"**
> 1. Enables `final` fields → immutability. 2. Makes dependencies explicit and mandatory. 3. Easy to test without reflection (just `new OrderService(mock)`). 4. Detects circular dependencies at startup.

---

### 3. Difference between Spring Boot and Spring MVC

**Spring MVC** is a **web framework** — a module within the Spring Framework for building web applications using the Model-View-Controller pattern.

**Spring Boot** is an **opinionated framework on top of Spring** that simplifies setup and configuration. It's not a replacement; it *uses* Spring MVC internally.

| Aspect | Spring MVC | Spring Boot |
|--------|-----------|-------------|
| Purpose | Web framework (MVC pattern) | Rapid application development |
| Configuration | Manual (XML or Java config) | Auto-configuration |
| Server | Need to deploy WAR to external Tomcat | Embedded server (Tomcat/Jetty) |
| Dependencies | Add each manually | Starter dependencies (e.g., `spring-boot-starter-web`) |
| Boilerplate | High | Minimal |

**Summary line for interview:** "Spring MVC is the web layer framework; Spring Boot is a tool that makes building Spring (including Spring MVC) applications faster through auto-configuration, starters, and embedded servers."

> **Cross-question — "Does Spring Boot replace Spring MVC?"**
> No. Spring Boot uses Spring MVC under the hood for web apps. `spring-boot-starter-web` pulls in Spring MVC.

---

### 4. Exception handling in Spring Boot + global exception handling

**Local handling:** `@ExceptionHandler` inside a specific controller.

**Global handling (recommended):** `@RestControllerAdvice` (or `@ControllerAdvice`) — applies across all controllers.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ResourceNotFoundException ex) {
        ErrorResponse error = new ErrorResponse(HttpStatus.NOT_FOUND.value(), ex.getMessage());
        return new ResponseEntity<>(error, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        String msg = ex.getBindingResult().getFieldError().getDefaultMessage();
        ErrorResponse error = new ErrorResponse(HttpStatus.BAD_REQUEST.value(), msg);
        return new ResponseEntity<>(error, HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        ErrorResponse error = new ErrorResponse(500, "Internal server error");
        return new ResponseEntity<>(error, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```

**Benefits:** Centralized handling, consistent error responses, separation of concerns, cleaner controllers.

> **Cross-question — "What's the difference between @ControllerAdvice and @RestControllerAdvice?"**
> `@RestControllerAdvice` = `@ControllerAdvice` + `@ResponseBody`. Use it for REST APIs so responses are serialized to JSON automatically.

---

### 5. Checked vs Unchecked exceptions

| Aspect | Checked Exception | Unchecked Exception |
|--------|-------------------|---------------------|
| Parent | `Exception` (excluding RuntimeException) | `RuntimeException` |
| Compile-time check | Yes — must catch or declare with `throws` | No |
| Examples | `IOException`, `SQLException` | `NullPointerException`, `IllegalArgumentException`, `ArrayIndexOutOfBoundsException` |
| When | Recoverable conditions (file not found, network) | Programming errors / logic bugs |

**Checked** — the compiler forces you to handle them. **Unchecked** — runtime errors, usually bugs, no compulsion to handle.

> **Cross-question — "Should I wrap checked exceptions into unchecked in Spring?"**
> Often yes. Spring itself does this (`DataAccessException` is unchecked). It keeps method signatures clean and lets a global handler deal with them. Wrap only when you can't meaningfully recover at that layer.

---

### 6. Design a REST API for user management with proper status codes

| Operation | HTTP Method | Endpoint | Success Status | Notes |
|-----------|-------------|----------|----------------|-------|
| Create user | POST | `/api/users` | `201 Created` | Return `Location` header |
| Get all users | GET | `/api/users` | `200 OK` | Support pagination |
| Get one user | GET | `/api/users/{id}` | `200 OK` / `404 Not Found` | |
| Update user (full) | PUT | `/api/users/{id}` | `200 OK` / `404` | |
| Partial update | PATCH | `/api/users/{id}` | `200 OK` | |
| Delete user | DELETE | `/api/users/{id}` | `204 No Content` | |

**Other status codes:** `400 Bad Request` (validation), `401 Unauthorized`, `403 Forbidden`, `409 Conflict` (duplicate email), `500 Internal Server Error`.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @PostMapping
    public ResponseEntity<UserDto> create(@Valid @RequestBody CreateUserRequest req) {
        UserDto created = userService.create(req);
        return ResponseEntity.status(HttpStatus.CREATED).body(created);
    }

    @GetMapping("/{id}")
    public ResponseEntity<UserDto> getById(@PathVariable Long id) {
        return ResponseEntity.ok(userService.getById(id));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> delete(@PathVariable Long id) {
        userService.delete(id);
        return ResponseEntity.noContent().build();
    }
}
```

**REST principles to mention:** noun-based URLs, plural resources, statelessness, proper HTTP verbs, versioning (`/api/v1/users`).

---

### 7. Role of DTOs in Spring Boot

**DTO (Data Transfer Object)** is an object used to **transfer data between layers/over the network**, decoupled from the persistence entity.

**Why use DTOs:**
- **Security** — don't expose internal entity fields (e.g., password hash, internal flags).
- **Decoupling** — API contract is independent of the database schema. Schema can change without breaking the API.
- **Control** — shape the response precisely (combine fields, hide fields, rename).
- **Validation** — request DTOs carry validation annotations.
- **Avoid lazy-loading issues** — prevents serializing JPA proxies and `LazyInitializationException`.

```java
public record UserDto(Long id, String name, String email) {}
// Entity has password, createdBy, audit fields — DTO exposes only what's needed.
```

> **Cross-question — "Isn't mapping entity↔DTO extra boilerplate?"**
> Yes, but tools like **MapStruct** or **ModelMapper** generate the mapping. The decoupling and security benefits outweigh the cost in real applications.

---

### 8. Difference between @RestController and @Controller

- **`@Controller`** — traditional MVC controller. Returns **view names** (JSP/Thymeleaf). To return data, each method needs `@ResponseBody`.
- **`@RestController`** — `@Controller` + `@ResponseBody`. Every method's return value is **serialized directly into the response body** (JSON/XML). Used for REST APIs.

```java
@RestController  // = @Controller + @ResponseBody
public class ApiController {
    @GetMapping("/data")
    public MyData getData() { return new MyData(); } // auto-serialized to JSON
}
```

**One-liner:** "Use `@Controller` for server-side rendered views, `@RestController` for REST APIs returning JSON."

---

### 9. How Spring Data JPA reduces boilerplate

Spring Data JPA generates repository implementations **at runtime** from interface definitions. You write the interface; Spring writes the SQL/JPQL.

**What it eliminates:**
- No need to write `EntityManager` CRUD code.
- **Derived query methods** — method name → query (`findByEmailAndStatus`).
- Built-in **pagination and sorting** (`Pageable`, `Sort`).
- Built-in CRUD via `JpaRepository` (`save`, `findById`, `delete`, `findAll`).

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);          // auto-generated
    List<User> findByStatusOrderByCreatedAtDesc(String status);

    @Query("SELECT u FROM User u WHERE u.age > :age")   // custom JPQL
    List<User> findOlderThan(@Param("age") int age);
}
```

> **Cross-question — "What if I need a complex query?"**
> Use `@Query` (JPQL or native SQL), Specifications/Criteria API for dynamic queries, or `@Query(nativeQuery=true)` for raw SQL.

---

### 10. Difference between save() and saveAndFlush() in JPA

- **`save()`** — schedules the entity to be persisted. The actual SQL may be **deferred** until the transaction commits or the persistence context is flushed.
- **`saveAndFlush()`** — persists **and immediately flushes** to the database, executing the SQL right away within the current transaction.

**When to use `saveAndFlush()`:**
- You need the change visible to a subsequent **native query** in the same transaction.
- You want **DB-generated values** (IDs, triggers, defaults) immediately.
- You need to trigger a **constraint violation early** to handle it.

> **Cross-question — "Does save() always hit the DB immediately?"**
> No. With Hibernate's write-behind, `save()` may just register the entity in the persistence context. The INSERT/UPDATE can be batched and run at flush/commit. `saveAndFlush()` forces it now.

---

## Hibernate / JPA / MySQL

### 11. Lazy loading vs Eager loading. When does lazy cause problems?

- **Lazy loading** — the associated data is loaded **on demand**, only when you access it. Hibernate uses a proxy.
- **Eager loading** — the associated data is loaded **immediately** with the parent entity.

```java
@OneToMany(fetch = FetchType.LAZY)   // default for *ToMany
private List<Order> orders;

@ManyToOne(fetch = FetchType.EAGER)  // default for *ToOne
private Customer customer;
```

**Defaults:** `@OneToMany`/`@ManyToMany` → LAZY. `@ManyToOne`/`@OneToOne` → EAGER.

**When lazy causes problems — `LazyInitializationException`:** If you access a lazy association **after the Hibernate session is closed** (e.g., in the controller/view after the transaction ended), Hibernate can't load it → exception.

**Solutions:**
- Use a **DTO projection** to load needed data inside the transaction.
- Use **`JOIN FETCH`** in JPQL.
- Use **`@EntityGraph`**.
- Keep the access within the transactional boundary.
- (Anti-pattern) `OpenSessionInView` — avoid in production.

> **Cross-question — "Why not just make everything eager to avoid the exception?"**
> Eager fetching causes **performance issues** — loads data you don't need, creates N+1 problems, and large cartesian products with multiple eager collections. Prefer lazy + explicit fetching where needed.

---

### 12. N+1 query problem and how to solve it

**Problem:** When you load N parent entities and then access a lazy association for each, Hibernate fires **1 query for the parents + N queries** (one per parent) for the associations = **N+1 queries**.

```java
List<Author> authors = authorRepository.findAll();   // 1 query
for (Author a : authors) {
    a.getBooks().size();                              // N queries (one per author)
}
```

**Solutions:**
1. **`JOIN FETCH`** in JPQL:
   ```java
   @Query("SELECT a FROM Author a JOIN FETCH a.books")
   List<Author> findAllWithBooks();
   ```
2. **`@EntityGraph`:**
   ```java
   @EntityGraph(attributePaths = {"books"})
   List<Author> findAll();
   ```
3. **Batch fetching:** `@BatchSize(size = 20)` — turns N queries into N/20.
4. **DTO projection** with a JOIN — fetch exactly what's needed in one query.

> **Cross-question — "How do you detect N+1 in the first place?"**
> Enable SQL logging (`spring.jpa.show-sql=true`), use **Hibernate statistics**, or tools like **p6spy** / **datasource-proxy**. Seeing repeated identical queries in logs is the tell.

---

### 13. OneToOne, OneToMany, ManyToOne, ManyToMany mappings

| Relationship | Meaning | Example |
|--------------|---------|---------|
| **OneToOne** | One row maps to exactly one row | User ↔ UserProfile |
| **OneToMany** | One parent has many children | Author → Books |
| **ManyToOne** | Many children belong to one parent | Books → Author |
| **ManyToMany** | Many on both sides (needs join table) | Students ↔ Courses |

```java
// OneToMany / ManyToOne (bidirectional)
@Entity
class Author {
    @OneToMany(mappedBy = "author", cascade = CascadeType.ALL)
    private List<Book> books;
}
@Entity
class Book {
    @ManyToOne
    @JoinColumn(name = "author_id")
    private Author author;   // owning side
}

// ManyToMany
@Entity
class Student {
    @ManyToMany
    @JoinTable(name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id"))
    private Set<Course> courses;
}
```

**Key concept — owning side:** The side with `@JoinColumn` (no `mappedBy`) owns the relationship and controls the foreign key. `mappedBy` marks the **inverse** side.

> **Cross-question — "Why prefer ManyToOne over OneToMany as the owning side?"**
> `@ManyToOne` puts the FK on the child table, which is natural and efficient. A unidirectional `@OneToMany` can create an extra join table or extra UPDATE statements.

---

### 14. Entity vs DTO

| Aspect | Entity | DTO |
|--------|--------|-----|
| Purpose | Maps to a DB table | Transfers data between layers/API |
| Annotations | `@Entity`, `@Id`, `@Column` | Plain object / validation annotations |
| Lifecycle | Managed by persistence context | Plain, unmanaged |
| Exposure | Should NOT be exposed via API | Designed for API exposure |
| Contains | All persistent fields | Only fields needed for the use case |

**Why separate them:** security (hide sensitive fields), decoupling (API independent of schema), avoid lazy-loading serialization issues, and tailored validation. (Same reasoning as Q7.)

---

### 15. How do you handle transactions in Spring Boot?

Using the **`@Transactional`** annotation (declarative transaction management). Spring wraps the method in a transaction via AOP proxy — commits on success, rolls back on runtime exception.

```java
@Service
public class TransferService {

    @Transactional
    public void transfer(Long from, Long to, BigDecimal amount) {
        accountRepository.debit(from, amount);
        accountRepository.credit(to, amount);
        // If either fails (runtime exception), both roll back
    }
}
```

**Key attributes:**
- `propagation` — how transactions relate (`REQUIRED` default, `REQUIRES_NEW`, etc.).
- `isolation` — `READ_COMMITTED`, `REPEATABLE_READ`, etc.
- `rollbackFor` — specify which exceptions trigger rollback.
- `readOnly = true` — optimization for read queries.

> **Cross-question — "Where should @Transactional go — controller, service, or repository?"**
> **Service layer.** That's where business logic and the unit-of-work boundary live. Repository-level is too granular; controller-level mixes web concerns with transactions.

---

### 16. What is @Transactional, and what happens if an exception occurs inside it?

`@Transactional` defines a transactional boundary. Spring creates a proxy; the method runs inside a DB transaction.

**On exception — default rollback rule:**
- **Unchecked exceptions** (`RuntimeException`, `Error`) → **rollback** automatically.
- **Checked exceptions** → **NO rollback by default** (transaction commits!). You must specify `@Transactional(rollbackFor = Exception.class)`.

```java
@Transactional(rollbackFor = Exception.class)  // rollback on checked too
public void process() throws IOException { ... }
```

**Common gotchas (very likely cross-questions):**
1. **Self-invocation** — calling a `@Transactional` method from another method **in the same class** bypasses the proxy → no transaction. Solution: separate bean or self-injection.
2. `@Transactional` only works on **public methods** (proxy-based).
3. Catching an exception inside the method **swallows** it → no rollback triggered.

> **Cross-question — "I caught the exception and logged it, why didn't it roll back?"**
> Because rollback is triggered by an **unhandled** exception propagating out of the method. If you catch it, Spring thinks everything succeeded. To force rollback: rethrow, or call `TransactionAspectSupport.currentTransactionStatus().setRollbackOnly()`.

---

### 17. How do indexes help in MySQL?

An **index** is a data structure (typically a **B-Tree**) that lets the database find rows **without scanning the entire table** — similar to a book's index.

**Benefits:**
- Faster `SELECT`, `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY`.
- Turns a full table scan O(n) into a tree lookup O(log n).
- Speeds up uniqueness checks (unique index).

**Costs / trade-offs:**
- **Slower writes** (`INSERT`/`UPDATE`/`DELETE`) — index must be maintained.
- **Extra storage**.
- Too many indexes hurt overall performance.

**Types:** Primary, Unique, Composite (multi-column), Full-text.

> **Cross-question — "When does an index NOT get used?"**
> When you use a function on the column (`WHERE YEAR(date) = 2024`), leading wildcard `LIKE '%abc'`, low-cardinality columns (e.g., boolean), or implicit type conversion. Also composite index column order matters — leftmost prefix rule.

---

### 18. INNER JOIN vs LEFT JOIN

- **INNER JOIN** — returns only rows where there's a **match in both tables**.
- **LEFT JOIN** (LEFT OUTER JOIN) — returns **all rows from the left table**, plus matched rows from the right. Unmatched right-side columns are `NULL`.

```sql
-- Customers who have placed orders
SELECT c.name, o.id
FROM customers c
INNER JOIN orders o ON c.id = o.customer_id;

-- ALL customers, with their orders if any (including customers with no orders)
SELECT c.name, o.id
FROM customers c
LEFT JOIN orders o ON c.id = o.customer_id;
```

**Memory aid:** INNER = intersection. LEFT = everything on the left + matches.

> **Cross-question — "How do you find customers with NO orders?"**
> `LEFT JOIN` + `WHERE o.customer_id IS NULL`.

---

### 19. How would you optimize a slow SQL query?

A structured approach (say this in order):

1. **Run `EXPLAIN` / `EXPLAIN ANALYZE`** — see the execution plan, identify full table scans.
2. **Add appropriate indexes** on columns in `WHERE`, `JOIN`, `ORDER BY`.
3. **Select only needed columns** — avoid `SELECT *`.
4. **Avoid functions on indexed columns** in `WHERE` (kills index usage).
5. **Optimize JOINs** — join on indexed columns, reduce joined rows early.
6. **Pagination** — use `LIMIT`/`OFFSET` or keyset pagination instead of fetching everything.
7. **Avoid N+1** at the application layer.
8. **Check data volume** — partitioning, archiving old data.
9. **Caching** — cache frequent read-heavy queries (Redis).
10. **Rewrite subqueries** as JOINs where appropriate; avoid correlated subqueries.

> **Cross-question — "What does EXPLAIN tell you specifically?"**
> Access type (`ALL` = full scan = bad, `ref`/`range`/`const` = good), which index is used (`key`), estimated `rows` scanned, and `Extra` (e.g., "Using filesort", "Using temporary" — both red flags).

---

### 20. Normalization, and when denormalization helps

**Normalization** organizes data to **reduce redundancy and improve integrity** by splitting data into related tables.

- **1NF** — atomic values, no repeating groups.
- **2NF** — 1NF + no partial dependency on part of a composite key.
- **3NF** — 2NF + no transitive dependency (non-key depends only on the key).

**Benefits:** less duplication, consistent data, easier updates.

**Denormalization** — deliberately adding redundancy (duplicating data) to **improve read performance** by avoiding joins.

**When denormalization helps:**
- **Read-heavy** systems (reporting, analytics, dashboards).
- When joins are too expensive at scale.
- Caching computed/aggregated values.

> **Cross-question — "What's the cost of denormalization?"**
> Data can become **inconsistent** (same data in multiple places), and writes become more complex/expensive (must update every copy). It's a read-vs-write trade-off.

---

## Spring Security / OAuth2 / JWT

### 21. How does JWT-based authentication work?

**JWT (JSON Web Token)** is a self-contained, signed token carrying user claims.

**Flow:**
1. User sends **credentials** (username/password) to `/login`.
2. Server **validates** them.
3. Server generates a **JWT**, signs it with a secret/private key, returns it to the client.
4. Client stores the token and sends it on every request in the **`Authorization: Bearer <token>`** header.
5. Server **validates the signature** and expiry on each request (no DB lookup needed) and grants access.

**Why it's powerful:** **Stateless** — the server doesn't store sessions. Each token is self-validating, which scales horizontally (any server instance can validate).

```
Client → POST /login (credentials)
Server → returns JWT
Client → GET /api/data, Header: Authorization: Bearer eyJhbGc...
Server → validates signature + expiry → 200 OK
```

> **Cross-question — "How does the server validate without storing the token?"**
> It recomputes the signature using its secret key over the header+payload and compares it to the token's signature. If they match and it's not expired, it's valid. No server-side storage required.

---

### 22. Authentication vs Authorization

- **Authentication** — **Who are you?** Verifying identity (login with credentials, OTP, biometrics).
- **Authorization** — **What are you allowed to do?** Checking permissions/roles after identity is known.

**Order:** Authentication **always** comes first, then authorization.

**Example:** Logging into a banking app = authentication. Whether you can access the admin panel = authorization.

| | Authentication | Authorization |
|--|---------------|---------------|
| Question | Who are you? | What can you access? |
| When | First | After authentication |
| Example | Login | Role/permission check |
| HTTP failure | `401 Unauthorized` | `403 Forbidden` |

> **Cross-question — "Which status code for each failure?"**
> Failed authentication → `401 Unauthorized`. Authenticated but no permission → `403 Forbidden`. (The naming of 401 is historically confusing, but 401 = not authenticated.)

---

### 23. Parts of a JWT token

A JWT has **three parts separated by dots**: `header.payload.signature`

1. **Header** — algorithm and token type.
   ```json
   { "alg": "HS256", "typ": "JWT" }
   ```
2. **Payload** — claims (user data + metadata). Contains `sub`, `iat`, `exp`, roles, etc.
   ```json
   { "sub": "user123", "role": "ADMIN", "exp": 1735689600 }
   ```
3. **Signature** — ensures integrity. `HMACSHA256(base64(header) + "." + base64(payload), secret)`.

Header and payload are **Base64Url-encoded (not encrypted)** — anyone can decode and read them. The signature ensures they weren't tampered with.

> **Cross-question — "Is JWT encrypted? Can I store sensitive data in the payload?"**
> **No, it's only encoded, not encrypted.** Anyone can decode the payload. **Never** put passwords or sensitive data in it. Use HTTPS, and if you need confidentiality, use **JWE** (encrypted JWT).

---

### 24. Where should JWT tokens be stored on the frontend? Trade-offs.

Two main options:

| Storage | Pros | Cons |
|---------|------|------|
| **localStorage / sessionStorage** | Simple, easy to access in JS, works across domains/APIs | **Vulnerable to XSS** — malicious JS can read the token |
| **HttpOnly Cookie** | **Not accessible via JS** (XSS-safe for reading), sent automatically | **Vulnerable to CSRF** (needs CSRF protection), domain-bound |

**Recommended approach:**
- Store JWT (or refresh token) in an **HttpOnly, Secure, SameSite cookie** to mitigate XSS.
- Add **CSRF protection** (CSRF token / `SameSite=Strict|Lax`) to mitigate CSRF.
- Use **short-lived access tokens** + refresh tokens to limit damage if leaked.

> **Cross-question — "Why not just localStorage, it's simpler?"**
> Because any XSS vulnerability lets an attacker steal the token and impersonate the user. HttpOnly cookies block JS access. The trade-off is you then handle CSRF, but that's a well-understood, solvable problem.

---

### 25. How to secure Spring Boot APIs using Spring Security

**Steps:**
1. Add `spring-boot-starter-security` dependency.
2. Configure a **`SecurityFilterChain`** bean.
3. Define which endpoints are public vs protected.
4. Add a **JWT filter** (for token-based) before `UsernamePasswordAuthenticationFilter`.
5. Set session policy to **STATELESS** for JWT.
6. Configure `PasswordEncoder` (BCrypt).

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    private final JwtAuthFilter jwtAuthFilter;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())  // disabled for stateless JWT APIs
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**", "/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .sessionManagement(s -> s.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);
        return http.build();
    }

    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
```

> **Cross-question — "Why disable CSRF for JWT APIs?"**
> CSRF protection matters for **cookie-based** auth where the browser auto-sends credentials. With JWT in the `Authorization` header, the token isn't sent automatically by the browser, so CSRF risk is low — hence it's commonly disabled for stateless REST APIs.

---

### 26. What is OAuth 2.0, and how is it different from JWT?

**OAuth 2.0** is an **authorization framework/protocol** that lets a third-party application get **delegated access** to a user's resources **without sharing credentials** (e.g., "Login with Google").

**JWT** is a **token format** — a way to represent claims securely.

**They are different things:**
- OAuth 2.0 = the **protocol/flow** (how access is granted).
- JWT = a **token format** (what the access token can look like).

OAuth 2.0 **can use** JWT as its access token format, but it doesn't have to (it can use opaque tokens).

| | OAuth 2.0 | JWT |
|--|-----------|-----|
| What | Authorization framework | Token format |
| Role | Defines flows for granting access | Carries claims securely |
| Relationship | Can issue JWTs as tokens | Can be the token inside OAuth |

**OAuth roles:** Resource Owner (user), Client (app), Authorization Server (issues tokens), Resource Server (hosts data).

> **Cross-question — "So is OAuth a replacement for JWT?"**
> No — they solve different problems and often work **together**. OAuth defines *how* a client obtains a token; JWT defines *what* that token looks like.

---

### 27. Can OAuth2 work without JWT?

**Yes.** OAuth 2.0 doesn't mandate JWT. Access tokens can be:
- **Opaque tokens** — random strings with no readable content. The resource server validates them by calling the authorization server's **introspection endpoint** (or shared store).
- **JWT (self-contained) tokens** — validated locally via signature, no extra call.

**Trade-off:**
- Opaque → more secure (can be revoked instantly, no data exposure) but requires a network call/lookup to validate.
- JWT → stateless, fast local validation, but harder to revoke before expiry.

> **Cross-question — "If JWT can't be revoked easily, how do you handle logout/revocation?"**
> Keep access tokens **short-lived**, maintain a **token blacklist/denylist** (e.g., in Redis) for forced revocation, or rotate refresh tokens. Many teams accept short expiry + refresh token revocation as the practical answer.

---

### 28. Purpose of a refresh token

A **refresh token** is a long-lived credential used to **obtain new access tokens** without forcing the user to log in again.

**Why needed:**
- Access tokens are **short-lived** (e.g., 5–15 min) for security — if stolen, they expire quickly.
- But forcing login every 15 minutes is bad UX.
- The refresh token (longer-lived, e.g., days/weeks) silently gets a fresh access token.

**Flow:**
```
Access token expires → Client sends refresh token to /refresh
→ Server validates refresh token → issues new access token (and optionally new refresh token)
```

**Security practices:** store refresh tokens securely (HttpOnly cookie / server side), implement **rotation** (new refresh token each use, invalidate old), and allow **revocation**.

> **Cross-question — "Where do you store the refresh token, and how do you revoke it?"**
> Store it in an HttpOnly Secure cookie or server-side store, never in localStorage. Revoke by keeping a server-side record of valid refresh tokens (DB/Redis) and deleting it on logout or suspected compromise.

---

### 29. How to implement role-based access control (RBAC) in Spring Boot

**Approaches:**

**1. URL-based (in SecurityFilterChain):**
```java
.requestMatchers("/api/admin/**").hasRole("ADMIN")
.requestMatchers("/api/user/**").hasAnyRole("USER", "ADMIN")
```

**2. Method-level security:** Enable with `@EnableMethodSecurity`, then:
```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PreAuthorize("hasAnyRole('USER', 'ADMIN')")
public List<Order> getMyOrders() { ... }

@PreAuthorize("#userId == authentication.principal.id")  // ownership check
public User getUser(Long userId) { ... }
```

Roles come from the user's authorities loaded in `UserDetails` (or from JWT claims).

> **Cross-question — "Difference between hasRole and hasAuthority?"**
> `hasRole('ADMIN')` automatically prepends `ROLE_` → checks for authority `ROLE_ADMIN`. `hasAuthority('ROLE_ADMIN')` checks the literal string. Be consistent with how you store roles.

---

### 30. Common security issues to consider for REST APIs

(Aligns with **OWASP Top 10** — mention it.)

1. **Broken Authentication** — weak/no auth, weak passwords → strong auth + JWT/OAuth.
2. **Broken Access Control** — missing authorization checks (IDOR) → enforce RBAC, validate ownership.
3. **Injection (SQL/NoSQL)** → parameterized queries / prepared statements (JPA helps).
4. **Sensitive Data Exposure** → HTTPS everywhere, don't return secrets, encrypt at rest.
5. **XSS / CSRF** → input sanitization, proper token storage, CSRF protection.
6. **Rate limiting / DoS** → throttle requests, API gateway limits.
7. **Mass Assignment** → use DTOs, don't bind directly to entities.
8. **Security Misconfiguration** → disable stack traces in prod, secure headers, least privilege.
9. **Improper input validation** → validate all inputs (`@Valid`).
10. **Logging sensitive data** → never log passwords/tokens.

> **Cross-question — "How do you prevent SQL injection specifically in Spring?"**
> Use **parameterized queries** — JPA/Hibernate and `@Query` with named parameters use prepared statements. **Never** build queries via string concatenation with user input.

---

## Microservices / REST APIs

### 31. Why choose microservices over a monolith?

**Reasons:**
- **Independent deployment** — deploy/update one service without redeploying everything.
- **Independent scaling** — scale only the hot service (e.g., payments) not the whole app.
- **Technology flexibility** — each service can use a different stack.
- **Team autonomy** — small teams own services end-to-end.
- **Fault isolation** — one service failing doesn't necessarily crash the whole system.
- **Faster releases** — smaller, focused codebases.

> **Cross-question — "When is a monolith actually the better choice?"**
> For **small teams, simple/early-stage products, or limited scale**. Microservices add operational complexity (networking, monitoring, distributed data). Start monolith, split when there's a real scaling/team reason ("monolith first").

---

### 32. Disadvantages of microservices

- **Operational complexity** — many services to deploy, monitor, and manage.
- **Distributed system challenges** — network latency, partial failures, retries.
- **Data consistency** — no single DB; distributed transactions are hard.
- **Testing complexity** — integration/end-to-end testing across services.
- **Debugging** — tracing a request across services is harder (need distributed tracing).
- **Infrastructure cost** — more moving parts (gateways, service discovery, message brokers).
- **DevOps maturity required** — CI/CD, containerization, orchestration.

> **Cross-question — "What's the single biggest challenge in practice?"**
> Usually **data management / consistency** across services and **observability** (knowing what failed where). That's why patterns like Saga, event-driven design, and distributed tracing exist.

---

### 33. How do microservices communicate?

**Two styles:**

**1. Synchronous (request/response):**
- **REST (HTTP)** — most common, simple.
- **gRPC** — high-performance, binary (Protobuf), good for internal calls.

**2. Asynchronous (event/message-based):**
- **Message brokers** — Kafka, RabbitMQ.
- Service publishes an event; others consume it. Decoupled, resilient.

| | Synchronous | Asynchronous |
|--|-------------|--------------|
| Coupling | Tight (caller waits) | Loose |
| Example | REST, gRPC | Kafka, RabbitMQ |
| Failure impact | Caller affected immediately | Buffered, resilient |

> **Cross-question — "When sync vs async?"**
> **Sync** when you need an immediate response (e.g., fetch user profile). **Async** for events, decoupling, and resilience (e.g., "order placed" triggering email, inventory, analytics).

---

### 34. What is service discovery?

In microservices, instances are **dynamic** — they scale up/down and change IPs. **Service discovery** lets services **find each other** without hardcoding addresses.

**How it works:**
- A **service registry** (e.g., Eureka, Consul) keeps a live list of service instances.
- Services **register** themselves on startup and send heartbeats.
- A client **queries the registry** (or uses client-side load balancing) to find an available instance.

**Two types:**
- **Client-side discovery** — client queries registry and picks an instance (e.g., Eureka + Ribbon/Spring Cloud LoadBalancer).
- **Server-side discovery** — a load balancer/gateway resolves the instance (e.g., Kubernetes Service, AWS ALB).

> **Cross-question — "How does this work in Kubernetes?"**
> Kubernetes has **built-in service discovery** via **Services** and **internal DNS**. You call a service by name (`http://order-service`) and kube-proxy/DNS routes it to a healthy pod. No Eureka needed.

---

### 35. How do you handle distributed transactions in microservices?

Traditional ACID transactions don't span services. Use the **Saga pattern** — a sequence of local transactions, each with a **compensating action** to undo on failure.

**Two Saga approaches:**

**1. Choreography** — services react to events; no central coordinator.
```
Order Service → "OrderCreated" event
Payment Service → consumes, charges → "PaymentDone" event
Inventory Service → consumes, reserves stock
(If payment fails → "PaymentFailed" → Order Service cancels order)
```

**2. Orchestration** — a central **orchestrator** tells each service what to do and triggers compensations on failure.

**Other patterns:** Outbox pattern (reliable event publishing), eventual consistency.

> **Cross-question — "Why not two-phase commit (2PC)?"**
> 2PC is **blocking, slow, and not scalable** in distributed systems — it locks resources and has a single point of failure (coordinator). Sagas with eventual consistency are preferred for microservices.

---

### 36. What is API Gateway, and why is it useful?

An **API Gateway** is a **single entry point** for all client requests, routing them to the appropriate backend microservice.

**Responsibilities:**
- **Routing** — direct requests to the right service.
- **Authentication/Authorization** — centralized security.
- **Rate limiting & throttling**.
- **Load balancing**.
- **Request/response transformation**.
- **Logging, monitoring, tracing**.
- **SSL termination**.
- **Aggregation** — combine multiple service calls into one response.

**Why useful:** clients talk to one endpoint instead of many; cross-cutting concerns are handled in one place instead of duplicated in every service.

**Examples:** Spring Cloud Gateway, Netflix Zuul, Kong, AWS API Gateway, Nginx.

> **Cross-question — "Isn't the gateway a single point of failure?"**
> It can be — so you run **multiple gateway instances behind a load balancer** for high availability. The benefits of centralizing cross-cutting concerns outweigh the risk when designed redundantly.

---

### 37. How do you version REST APIs?

**Common strategies:**

1. **URI versioning** (most common): `/api/v1/users`, `/api/v2/users`.
2. **Query parameter:** `/api/users?version=1`.
3. **Header versioning:** `Accept: application/vnd.company.v1+json`.
4. **Content negotiation** via `Accept` header.

```java
@RequestMapping("/api/v1/users")  // version in URI
```

**Why version:** to introduce breaking changes **without breaking existing clients**. Old clients keep using v1; new clients use v2.

> **Cross-question — "Which is best?"**
> **URI versioning** is the most explicit, visible, and easy to test/cache — most teams use it. Header versioning is "cleaner" per REST purists but harder to test and discover. Pick one and stay consistent.

---

### 38. What is idempotency in APIs?

An operation is **idempotent** if making the **same request multiple times produces the same result** as making it once (no additional side effects).

**HTTP methods:**
- **Idempotent:** GET, PUT, DELETE, HEAD.
- **NOT idempotent:** POST (creates a new resource each time).

**Why it matters:** networks are unreliable; clients retry. Idempotency ensures retries don't cause **duplicate charges, duplicate orders**, etc.

**How to make POST idempotent:** use an **idempotency key** — client sends a unique key; server stores it and returns the same result for repeats.
```
POST /payments
Idempotency-Key: abc-123
→ Server: if key already processed, return previous result; else process.
```

> **Cross-question — "Give a real example where this matters."**
> Payment processing. If the network drops after charging but before the response, the client retries. Without idempotency, the customer is charged twice. With an idempotency key, the second request returns the original result without re-charging.

---

### 39. How do you handle retries between services?

**Strategies:**
1. **Retry with backoff** — retry failed calls with increasing delays (**exponential backoff**) + **jitter** to avoid thundering herd.
2. **Limit retries** — cap attempts (e.g., 3) to avoid overload.
3. **Circuit Breaker** — after repeated failures, "open" the circuit and fail fast instead of hammering a down service (Resilience4j, Hystrix).
4. **Idempotency** — ensure retried requests don't cause duplicates.
5. **Timeouts** — fail fast on slow calls.
6. **Dead Letter Queue** — for async, route repeatedly-failing messages aside.

```java
@Retry(name = "orderService")
@CircuitBreaker(name = "orderService", fallbackMethod = "fallback")
public Order getOrder(Long id) { ... }
```

> **Cross-question — "Why is exponential backoff better than fixed retries?"**
> Fixed/immediate retries can **overwhelm** an already-struggling service (retry storm). Exponential backoff + jitter spreads load over time, giving the service room to recover.

---

### 40. How do you trace failures across multiple microservices?

**Distributed Tracing.**

**How it works:**
- A unique **trace ID (correlation ID)** is generated at the entry point and **propagated** through every service call (via headers).
- Each service logs with this trace ID, so you can **stitch together** the full request path.
- Each step is a **span**; spans form a tree showing latency at each hop.

**Tools:** Spring Cloud Sleuth / **Micrometer Tracing**, **Zipkin**, **Jaeger**, OpenTelemetry. Plus centralized logging (**ELK / EFK stack**) and metrics (Prometheus + Grafana).

> **Cross-question — "How is the trace ID passed between services?"**
> Via **HTTP headers** (e.g., `X-B3-TraceId` / W3C `traceparent`). The tracing library auto-injects it on outgoing calls and extracts it on incoming ones, so all logs across services share the same ID.

---

## Kafka

### 41. What is Apache Kafka, and when would you use it?

**Kafka** is a **distributed event streaming platform** — a high-throughput, fault-tolerant, publish-subscribe messaging system that stores streams of records durably.

**When to use:**
- **Event-driven architecture** — services react to events.
- **Decoupling** producers and consumers.
- **High-throughput data pipelines** — logs, metrics, clickstreams.
- **Real-time stream processing**.
- **Asynchronous communication** between microservices.
- **Event sourcing / audit logs** (durable, replayable).

**Key strengths:** durability (persisted to disk), scalability (partitions), replayability (consumers can re-read), and high throughput.

> **Cross-question — "Kafka vs RabbitMQ?"**
> Kafka = **distributed log**, optimized for **high throughput, replay, and stream processing**; consumers track their own offset. RabbitMQ = **traditional message broker** with complex routing, optimized for **per-message delivery and lower latency** at smaller scale. Kafka for streaming/big data, RabbitMQ for task queues/complex routing.

---

### 42. What are topics, partitions, producers, and consumers?

- **Topic** — a named category/feed of messages (like a table/channel).
- **Partition** — a topic is split into partitions for **parallelism and scalability**. Each partition is an **ordered, append-only log**.
- **Producer** — publishes messages to a topic (chooses partition via key or round-robin).
- **Consumer** — reads messages from a topic. Tracks its position with an **offset**.
- **Consumer Group** — a set of consumers sharing the work of a topic.
- **Broker** — a Kafka server storing partitions; a cluster has multiple brokers.

```
Producer → Topic "orders" → [Partition 0][Partition 1][Partition 2]
                                  ↓            ↓            ↓
                            Consumer A    Consumer B   Consumer C  (same group)
```

> **Cross-question — "How is a message assigned to a partition?"**
> If the producer provides a **key**, Kafka hashes it → same key always goes to the same partition (preserves ordering for that key). No key → round-robin/sticky distribution.

---

### 43. Why does Kafka use partitions?

**Reasons:**
1. **Scalability** — partitions spread a topic across multiple brokers, enabling horizontal scaling beyond one machine's capacity.
2. **Parallelism** — multiple consumers in a group can read different partitions **simultaneously**, increasing throughput.
3. **Ordering guarantee** — order is guaranteed **within a partition** (not across the whole topic).
4. **Load distribution** — data and traffic are balanced across brokers.

> **Cross-question — "What's the trade-off of having more partitions?"**
> More partitions = more parallelism, but also more open file handles, more overhead, longer leader-election/rebalance times, and **ordering is only per-partition**. You can't easily reduce partition count later. Size them thoughtfully.

---

### 44. What happens if two consumers are in the same consumer group?

Within a **consumer group**, each **partition is consumed by exactly one consumer**. Kafka **distributes partitions** among the group's consumers for parallelism.

**Scenarios:**
- Partitions = 2, Consumers = 2 → each consumer gets 1 partition (balanced).
- Partitions = 2, Consumers = 3 → 2 consumers get a partition each, **1 sits idle** (can't have more active consumers than partitions).
- A consumer dies → **rebalance** — its partitions are reassigned to remaining consumers.

**Key rule:** A message is processed by **only one consumer within a group** (load balancing). Across **different** groups, every group gets its own copy (pub-sub).

> **Cross-question — "How do I get the same message to multiple services?"**
> Put each service in a **different consumer group**. Each group independently reads all messages. Same group = load-balanced (one consumer per message); different groups = broadcast.

---

### 45. How do you ensure message ordering in Kafka?

Kafka guarantees ordering **only within a partition**, not across partitions.

**To ensure ordering:**
1. **Use a partition key** — messages with the same key (e.g., `customerId`, `orderId`) always go to the **same partition** → ordered for that key.
2. **Single partition** — guarantees total ordering but kills parallelism (rarely used).
3. On the producer, set `max.in.flight.requests.per.connection=1` (or enable idempotent producer) to avoid reordering on retries.

```java
// Same key → same partition → ordered
producer.send(new ProducerRecord<>("orders", orderId, orderEvent));
```

> **Cross-question — "Can you guarantee global ordering across the whole topic?"**
> Only with a **single partition**, which sacrifices scalability. In practice you choose a key that gives ordering *where it matters* (per entity) while keeping parallelism across keys.

---

### 46. At-most-once vs at-least-once vs exactly-once

| Semantic | Meaning | Risk | How |
|----------|---------|------|-----|
| **At-most-once** | Message delivered 0 or 1 time | May **lose** messages | Commit offset **before** processing |
| **At-least-once** | Delivered 1 or more times | May **duplicate** messages | Commit offset **after** processing (default, most common) |
| **Exactly-once** | Delivered exactly once | None (hardest) | Kafka transactions + idempotent producer |

- **At-most-once:** fast, no duplicates, but data loss if crash after commit.
- **At-least-once:** no loss, but duplicates possible → consumer must be **idempotent**.
- **Exactly-once (EOS):** no loss, no duplicates — via **idempotent producer + transactions** (`enable.idempotence=true`, transactional API).

> **Cross-question — "Which do you use in practice?"**
> **At-least-once** is the default and most common, combined with **idempotent consumers** to handle duplicates. Exactly-once is powerful but adds overhead/complexity; use it when duplicates are truly unacceptable (financial).

---

### 47. What happens if a consumer fails after reading a message?

Depends on **when the offset was committed**:

- **Offset committed BEFORE processing** (at-most-once): the message is considered done. If the consumer crashes mid-processing → **message is lost**.
- **Offset committed AFTER processing** (at-least-once, default): if it crashes before committing, on restart it **re-reads from the last committed offset** → the message is **reprocessed** (possible duplicate).

**This is why at-least-once + idempotency is the standard pattern** — reprocessing is safe if the consumer is idempotent.

> **Cross-question — "How do you avoid double-processing then?"**
> Make the consumer **idempotent** — track processed message IDs (DB/Redis), or use upserts, or Kafka's exactly-once transactions. Re-delivery becomes harmless.

---

### 48. How do you handle duplicate messages?

Duplicates happen with at-least-once delivery. Handle via **idempotent processing**:

1. **Unique message ID / idempotency key** — store processed IDs; skip if already seen.
   ```java
   if (processedRepository.existsById(message.getId())) return; // skip duplicate
   process(message);
   processedRepository.save(message.getId());
   ```
2. **Database upserts** — `INSERT ... ON DUPLICATE KEY UPDATE`, so reprocessing yields the same state.
3. **Kafka exactly-once semantics** — idempotent producer + transactions.
4. **Deduplication store** — Redis set of recently-seen IDs with TTL.

> **Cross-question — "Where do you store processed IDs without a memory leak?"**
> Use a store with **TTL/expiry** (Redis) or a DB table you periodically clean, sized to your retry/redelivery window. You don't need to keep IDs forever — only long enough to catch realistic duplicates.

---

### 49. When would you choose Kafka over a REST call between services?

**Choose Kafka (async) when:**
- You need **decoupling** — producer shouldn't wait for/know consumers.
- **One-to-many** — one event consumed by multiple services.
- **High throughput / buffering** — absorb spikes; consumers process at their pace.
- **Resilience** — if the consumer is down, messages wait in Kafka (no data loss).
- **Event-driven / streaming** use cases.
- **Replayability** — reprocess historical events.

**Choose REST (sync) when:**
- You need an **immediate response**.
- Simple **request/response** (e.g., fetch data on demand).
- Strong **request-time consistency**.

> **Cross-question — "Give a concrete example."**
> Placing an order: persist the order via REST (need immediate confirmation), then **publish an `OrderPlaced` event to Kafka**. Email service, inventory service, and analytics each consume it independently — without the order service waiting on any of them.

---

### 50. Kafka use cases in enterprise apps

- **Event-driven microservices** — order/payment/inventory communication.
- **Activity tracking** — user clicks, page views, behavior analytics.
- **Log aggregation** — centralize logs from many services.
- **Real-time analytics / dashboards** — metrics, fraud detection.
- **Data pipelines / ETL** — stream data into data lakes/warehouses.
- **Notifications** — email/SMS/push triggered by events.
- **Change Data Capture (CDC)** — stream DB changes (Debezium) to other systems.
- **Audit logging** — durable, immutable event history.

> **Cross-question — "Describe one you've worked with."**
> (Adapt to your experience.) Example: "We published domain events like `PaymentCompleted` to Kafka. The notification service sent emails, the analytics service updated dashboards, and the ledger service recorded entries — all decoupled, so adding a new consumer never required changing the payment service."

---

## React / JavaScript

### 51. State vs Props in React

| Aspect | State | Props |
|--------|-------|-------|
| Definition | Internal, component-managed data | Data passed **from parent to child** |
| Mutability | Mutable (via `setState`/setter) | **Immutable** (read-only in child) |
| Owned by | The component itself | The parent |
| Triggers re-render | Yes, on update | Yes, when parent passes new props |

- **State** — private, controlled by the component (`useState`). Use for data that changes over time.
- **Props** — inputs to a component, passed by the parent. The child cannot modify them.

```jsx
function Parent() {
  const [count, setCount] = useState(0);       // state
  return <Child value={count} />;              // value is a prop in Child
}
function Child({ value }) {                     // receives prop
  return <p>{value}</p>;                        // cannot modify value
}
```

> **Cross-question — "Can a child change props?"**
> No, props are read-only. To change data in the parent, the child calls a **callback function passed down as a prop** (lifting state up).

---

### 52. What are hooks? Explain useState and useEffect.

**Hooks** let you use **state and lifecycle features in functional components** (before hooks, only class components could). Introduced in React 16.8.

**`useState`** — adds state to a functional component.
```jsx
const [count, setCount] = useState(0);  // [current value, setter]
setCount(count + 1);                     // updates state → triggers re-render
```

**`useEffect`** — handles **side effects** (data fetching, subscriptions, DOM manipulation, timers). Runs after render.
```jsx
// Runs once on mount (empty dependency array)
useEffect(() => {
  fetchData();
}, []);

// Runs when `userId` changes
useEffect(() => {
  fetchUser(userId);
}, [userId]);

// Cleanup (runs on unmount or before re-run)
useEffect(() => {
  const timer = setInterval(tick, 1000);
  return () => clearInterval(timer);   // cleanup
}, []);
```

**Rules of Hooks:** call hooks only at the **top level** (not in loops/conditions) and only in **React functions**.

> **Cross-question — "What does the dependency array do?"**
> It controls **when** the effect re-runs. `[]` = once on mount. `[a, b]` = whenever `a` or `b` changes. **No array** = after **every** render (often a bug/performance issue).

---

### 53. What causes a React component to re-render?

A component re-renders when:
1. **State changes** (via setter from `useState`/`useReducer`).
2. **Props change** (parent passes new props).
3. **Parent re-renders** — children re-render by default, even if their props didn't change.
4. **Context value changes** (consumers of that context re-render).
5. Force update (rare).

> **Cross-question — "How do you prevent unnecessary re-renders?"**
> `React.memo` (memoize component by props), `useMemo` (memoize expensive computed values), `useCallback` (memoize function references so child props are stable), and proper state placement (keep state as local as possible).

---

### 54. How do you call a backend API from React?

Using **`fetch`** or **`axios`**, typically inside `useEffect` (for load-time) or an event handler.

```jsx
import { useState, useEffect } from "react";
import axios from "axios";

function Users() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    axios.get("/api/users")
      .then(res => setUsers(res.data))
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

**Best practices:** handle **loading/error/success** states, cleanup on unmount, and consider libraries like **React Query / SWR** for caching, retries, and deduping.

> **Cross-question — "Why use React Query over plain fetch?"**
> It handles caching, background refetching, retries, loading/error states, and request deduplication out of the box — removing tons of boilerplate and bugs around server state.

---

### 55. How do you handle authentication in a React app using JWT?

**Flow:**
1. User submits credentials → call `/login` API.
2. Receive JWT → **store it** (HttpOnly cookie preferred; or memory/localStorage with caveats).
3. **Attach the token** to every API request (`Authorization: Bearer <token>`) — use an **axios interceptor**.
4. **Protect routes** — redirect unauthenticated users to login (`PrivateRoute`).
5. On logout → clear the token.
6. Handle **401 responses** → refresh token or redirect to login.

```jsx
// Axios interceptor attaches token automatically
axios.interceptors.request.use(config => {
  const token = getToken();
  if (token) config.headers.Authorization = `Bearer ${token}`;
  return config;
});

// Protected route
function PrivateRoute({ children }) {
  return isAuthenticated() ? children : <Navigate to="/login" />;
}
```

> **Cross-question — "How do you handle token expiry gracefully?"**
> Use a **response interceptor**: on `401`, call the refresh-token endpoint to get a new access token and retry the original request; if refresh fails, redirect to login.

---

### 56. Controlled vs Uncontrolled components

- **Controlled component** — form data is handled by **React state**. The input's value is driven by state; changes go through `onChange`. **Single source of truth.**
- **Uncontrolled component** — form data is handled by the **DOM itself**; you read values via a **ref** when needed.

```jsx
// Controlled
const [name, setName] = useState("");
<input value={name} onChange={e => setName(e.target.value)} />

// Uncontrolled
const inputRef = useRef();
<input ref={inputRef} />
// read: inputRef.current.value
```

| | Controlled | Uncontrolled |
|--|-----------|--------------|
| Data source | React state | DOM |
| Value access | From state | From ref |
| Validation | Easy, real-time | Manual |
| Recommended | ✅ Generally preferred | For simple/legacy cases |

> **Cross-question — "Which should you use?"**
> **Controlled** in most cases — easier validation, instant access to values, predictable. Uncontrolled is fine for simple forms, file inputs, or integrating non-React code.

---

### 57. What is the Virtual DOM?

The **Virtual DOM (VDOM)** is a **lightweight in-memory JavaScript representation** of the real DOM. React uses it to optimize UI updates.

**How it works:**
1. On a state/props change, React builds a **new virtual DOM tree**.
2. It **diffs** the new tree against the previous one (**reconciliation**).
3. It computes the **minimal set of changes** and updates **only those parts** of the real DOM (batched).

**Why:** Directly manipulating the real DOM is **slow**. Diffing in memory and applying minimal updates is much faster.

> **Cross-question — "How does React diff efficiently?"**
> Using a **heuristic O(n) diffing algorithm**: it compares elements by type and uses **keys** in lists to track items across renders. That's why stable `key` props matter — wrong keys cause unnecessary re-renders or bugs.

---

### 58. How do you optimize performance in React?

1. **`React.memo`** — prevent re-render when props are unchanged.
2. **`useMemo`** — memoize expensive computations.
3. **`useCallback`** — memoize function references (stable child props).
4. **Code splitting / lazy loading** — `React.lazy` + `Suspense`.
5. **Virtualization** — render only visible list items (`react-window`).
6. **Proper `key` props** in lists.
7. **Avoid inline objects/functions** in render where they cause child re-renders.
8. **Keep state local** — don't lift state higher than needed.
9. **Debounce/throttle** expensive handlers (search, scroll).
10. **Production build** — minified, no dev warnings.

> **Cross-question — "How do you find what's slow?"**
> Use the **React DevTools Profiler** to see which components re-render and how long they take, and the browser performance tab. Optimize based on measurement, not guesswork.

---

### 59. == vs === in JavaScript

- **`==` (loose equality)** — compares values **after type coercion** (converts types to match).
- **`===` (strict equality)** — compares **value AND type**, no coercion.

```js
5 == "5"      // true  (string coerced to number)
5 === "5"     // false (different types)
0 == false    // true  (coercion)
0 === false   // false
null == undefined   // true
null === undefined  // false
```

**Best practice:** **always use `===`** to avoid unexpected coercion bugs.

> **Cross-question — "When would you ever use ==?"**
> One common pragmatic use: `value == null` to check for **both `null` and `undefined`** in one shot. Otherwise prefer `===`.

---

### 60. What are closures in JavaScript?

A **closure** is a function that **remembers and accesses variables from its outer (enclosing) scope** even after that outer function has finished executing.

```js
function counter() {
  let count = 0;                 // private variable
  return function () {
    count++;                     // closure "remembers" count
    return count;
  };
}
const increment = counter();
increment(); // 1
increment(); // 2  → count persists
```

**Why useful:**
- **Data privacy / encapsulation** — `count` can't be accessed directly from outside.
- **Stateful functions** — maintain state between calls.
- Used in **callbacks, event handlers, currying, module patterns**, and React hooks.

> **Cross-question — "Where do closures cause bugs?"**
> The classic `var` in a loop with `setTimeout` — all callbacks share the same variable and log the final value. Fix with `let` (block scope) or an IIFE. In React, **stale closures** in `useEffect` capture old state — fix with correct dependencies or functional updates.

---

## AWS / Docker / Kubernetes / CI-CD

### 61. Which AWS services have you used, and for what purpose?

*(Adapt to your real experience. A strong sample answer:)*

| Service | Purpose |
|---------|---------|
| **EC2** | Hosting application servers / Spring Boot apps |
| **S3** | Object storage — files, images, backups, static assets |
| **RDS** | Managed relational DB (MySQL/PostgreSQL) |
| **ECS / EKS** | Running containerized apps |
| **Lambda** | Serverless functions for event-driven tasks |
| **SQS / SNS** | Messaging and notifications |
| **CloudWatch** | Logs, metrics, monitoring, alarms |
| **IAM** | Access control and permissions |
| **API Gateway** | Exposing/managing REST APIs |
| **ELB** | Load balancing across instances |

> **Cross-question — "How do you secure access to these services?"**
> Through **IAM roles and policies** (least-privilege), **IAM roles for EC2/ECS** instead of hardcoded keys, **security groups** for network access, and **Secrets Manager/Parameter Store** for credentials.

---

### 62. How would you deploy a Spring Boot app on AWS?

*(Several valid approaches — explain options, then pick one.)*

**Option A — EC2 (simple):**
1. Build the JAR (`mvn clean package`).
2. Launch an EC2 instance, install Java.
3. Copy the JAR, run it (with `systemd`/`nohup`), put it behind an **ELB**.

**Option B — Containerized (ECS/EKS) — modern:**
1. Dockerize the app.
2. Push the image to **ECR**.
3. Deploy on **ECS (Fargate)** or **EKS (Kubernetes)**.
4. Use **ALB** for routing, **CloudWatch** for logs.

**Option C — Elastic Beanstalk (managed, easiest):**
- Upload the JAR; Beanstalk handles provisioning, load balancing, scaling.

**Supporting services:** RDS (DB), S3 (assets), Secrets Manager (config), CloudWatch (monitoring), CI/CD pipeline for automation.

> **Cross-question — "Which would you choose and why?"**
> For a microservice at scale → **ECS Fargate or EKS** (no server management, easy scaling). For quick deployment → **Elastic Beanstalk**. For full control → **EC2**. I'd justify based on team size, scale, and DevOps maturity.

---

### 63. Difference between EC2, ECS, EKS, and Lambda

| Service | What | Use Case |
|---------|------|----------|
| **EC2** | Virtual servers (IaaS) | Full control over OS/server; traditional hosting |
| **ECS** | AWS-managed container orchestration | Run Docker containers without managing K8s |
| **EKS** | Managed **Kubernetes** | Run containers using standard Kubernetes |
| **Lambda** | Serverless functions (FaaS) | Event-driven, short tasks; no server management; pay-per-execution |

**Spectrum of control vs management:**
`EC2` (most control, most management) → `ECS/EKS` (container orchestration) → `Lambda` (least management, serverless).

> **Cross-question — "ECS vs EKS — when which?"**
> **ECS** if you want simpler, AWS-native orchestration with less overhead. **EKS** if you need **standard Kubernetes** (portability across clouds, existing K8s skills/tooling, advanced features). EKS has more flexibility but more complexity.

---

### 64. What is Docker, and why do we use it?

**Docker** is a **containerization platform** that packages an application with **all its dependencies** (libraries, runtime, config) into a single, portable **container** that runs consistently anywhere.

**Why use it:**
- **"Works on my machine" solved** — same environment everywhere (dev/test/prod).
- **Lightweight** — containers share the host OS kernel (vs heavy VMs).
- **Fast startup** — seconds vs minutes for VMs.
- **Isolation** — apps run independently.
- **Portability** — runs on any Docker host/cloud.
- **Scalability** — easy to spin up multiple instances.
- **CI/CD-friendly** — consistent build artifacts.

> **Cross-question — "Docker vs Virtual Machine?"**
> A **VM** virtualizes hardware and includes a **full guest OS** (heavy, GBs, slow boot). A **container** virtualizes the OS and **shares the host kernel** (lightweight, MBs, fast boot). Containers are more efficient; VMs offer stronger isolation.

---

### 65. Difference between a Docker image and a container

- **Image** — a **read-only template/blueprint** containing the app, dependencies, and config. Built from a Dockerfile. Static.
- **Container** — a **running instance** of an image. Live, has a writable layer, its own process/filesystem/network.

**Analogy:** Image = **class** / recipe; Container = **object** / the cooked dish. One image → many containers.

```bash
docker build -t myapp .       # create image
docker run myapp              # create & start a container from the image
```

> **Cross-question — "Can you run multiple containers from one image?"**
> Yes — that's common. One image can spawn **many identical containers** (e.g., 5 instances of the same service for load balancing). Each is isolated with its own writable layer.

---

### 66. Basic flow of containerizing a Spring Boot application

1. **Build the JAR:** `mvn clean package`.
2. **Write a Dockerfile.**
3. **Build the image:** `docker build -t myapp:1.0 .`.
4. **Run the container:** `docker run -p 8080:8080 myapp:1.0`.
5. **(Optional) Push to a registry:** `docker push` to Docker Hub / ECR.

```dockerfile name=Dockerfile
# Use a slim JRE base image
FROM eclipse-temurin:17-jre-alpine

# Working directory inside container
WORKDIR /app

# Copy the built jar
COPY target/myapp-0.0.1-SNAPSHOT.jar app.jar

# Expose the port
EXPOSE 8080

# Run the app
ENTRYPOINT ["java", "-jar", "app.jar"]
```

> **Cross-question — "How do you reduce Docker image size?"**
> Use a **slim/JRE base** (not full JDK), **multi-stage builds** (build with JDK, run with JRE), `.dockerignore` to exclude junk, and layer caching. Multi-stage keeps build tools out of the final image.

---

### 67. What problem does Kubernetes solve?

Kubernetes (**K8s**) is a **container orchestration platform**. Running containers manually at scale is hard — K8s automates it.

**Problems it solves:**
- **Automated deployment & scaling** — scale containers up/down based on load.
- **Self-healing** — restarts failed containers, replaces unhealthy ones.
- **Load balancing** — distributes traffic across pods.
- **Service discovery** — built-in DNS for services.
- **Rolling updates & rollbacks** — zero-downtime deployments.
- **Config & secret management**.
- **Resource management** — schedules containers across nodes efficiently.

> **Cross-question — "Why not just use Docker / Docker Compose?"**
> Docker runs containers; **Compose** is fine for a single host/dev. But for **production at scale** — multiple nodes, auto-scaling, self-healing, rolling updates, high availability — you need orchestration. That's Kubernetes.

---

### 68. Pods, deployments, services, and config maps in Kubernetes

- **Pod** — the **smallest deployable unit**; wraps one (or more) containers sharing network/storage. Usually one container per pod.
- **Deployment** — manages pods: **desired replica count**, rolling updates, rollbacks, self-healing. You declare "I want 3 replicas," K8s maintains it.
- **Service** — a **stable network endpoint** for a set of pods (pods are ephemeral with changing IPs). Provides load balancing and discovery. Types: ClusterIP, NodePort, LoadBalancer.
- **ConfigMap** — externalizes **non-sensitive configuration** (env-specific settings). (**Secret** is the equivalent for sensitive data like passwords.)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
spec:
  replicas: 3                 # desired pods
  selector:
    matchLabels: { app: myapp }
  template:
    metadata:
      labels: { app: myapp }
    spec:
      containers:
        - name: myapp
          image: myapp:1.0
          ports:
            - containerPort: 8080
```

> **Cross-question — "Why do we need a Service if we already have pods?"**
> Pods are **ephemeral** — they die and restart with **new IPs**. A Service gives a **stable IP/DNS name** and load-balances across the current healthy pods, so clients don't track individual pod IPs.

---

### 69. What happens when a pod crashes in Kubernetes?

Kubernetes **self-heals** automatically:
1. The **kubelet** detects the container/pod failure.
2. Based on the **restart policy** (default `Always`), it **restarts** the container.
3. If a pod is managed by a **Deployment/ReplicaSet** and can't recover, K8s **creates a new pod** to maintain the desired replica count.
4. **Liveness probes** detect hung-but-running containers and restart them.
5. **Readiness probes** ensure traffic only goes to pods that are ready.

**Net effect:** the desired state (e.g., 3 replicas) is continuously maintained without manual intervention.

> **Cross-question — "What if the pod keeps crashing repeatedly?"**
> It enters **`CrashLoopBackOff`** — K8s backs off and waits increasingly longer between restart attempts. That's your signal to check logs (`kubectl logs`), events (`kubectl describe pod`), and fix the root cause (bad config, missing dependency, OOM).

---

### 70. How does horizontal scaling work in Kubernetes?

**Horizontal scaling** = adding/removing **pod replicas** (scaling out/in), as opposed to vertical scaling (bigger pods).

**Manual:**
```bash
kubectl scale deployment myapp --replicas=5
```

**Automatic — Horizontal Pod Autoscaler (HPA):**
- HPA monitors metrics (CPU, memory, or custom metrics).
- When usage exceeds a threshold, it **adds pods**; when it drops, it **removes pods** — within min/max bounds.

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata: { name: myapp-hpa }
spec:
  scaleTargetRef: { apiVersion: apps/v1, kind: Deployment, name: myapp }
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target: { type: Utilization, averageUtilization: 70 }
```

> **Cross-question — "Horizontal vs Vertical scaling?"**
> **Horizontal** = more pods (better for stateless apps, high availability, no downtime). **Vertical** = more CPU/RAM per pod (limited by node size, may need restart). Horizontal is preferred for cloud-native, stateless services.

---

### 71. What is a CI/CD pipeline?

- **CI (Continuous Integration)** — developers frequently merge code; each change triggers **automated build + tests** to catch issues early.
- **CD (Continuous Delivery/Deployment)** — automatically **deploys** validated code to environments (staging/prod). Delivery = ready to deploy (manual approval); Deployment = fully automatic to prod.

**Why:** faster releases, fewer bugs, automated/repeatable process, quick feedback, less manual error.

**Typical flow:**
```
Code commit → Build → Unit tests → Code quality scan →
Package (Docker image) → Deploy to staging → Integration tests →
Deploy to production
```

> **Cross-question — "Difference between Continuous Delivery and Continuous Deployment?"**
> **Delivery** — every change is automatically built/tested and **ready** to deploy, but the final push to prod needs a **manual approval**. **Deployment** — every passing change goes to **production automatically**, no manual gate.

---

### 72. Stages in a GitLab CI/CD pipeline for a Spring Boot app

```yaml name=.gitlab-ci.yml
stages:
  - build
  - test
  - quality
  - package
  - deploy

build:
  stage: build
  script:
    - mvn clean compile

test:
  stage: test
  script:
    - mvn test
  artifacts:
    reports:
      junit: target/surefire-reports/*.xml

code-quality:
  stage: quality
  script:
    - mvn sonar:sonar      # SonarQube static analysis

package:
  stage: package
  script:
    - mvn package -DskipTests
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

deploy-staging:
  stage: deploy
  script:
    - kubectl apply -f k8s/staging/
  environment: staging

deploy-prod:
  stage: deploy
  script:
    - kubectl apply -f k8s/prod/
  environment: production
  when: manual          # manual approval for prod
```

**Stages explained:** Build → compile; Test → unit/integration tests; Quality → SonarQube/security scan; Package → build & push Docker image; Deploy → to staging (auto) and prod (manual gate).

> **Cross-question — "How do you handle secrets in the pipeline?"**
> Use **GitLab CI/CD variables** (masked & protected), or integrate a secrets manager (Vault, AWS Secrets Manager). **Never** hardcode credentials in `.gitlab-ci.yml`.

---

### 73. How do you manage environment-specific configs in deployment?

**Approaches:**
1. **Spring Profiles** — `application-dev.yml`, `application-staging.yml`, `application-prod.yml`; activate via `SPRING_PROFILES_ACTIVE`.
2. **Environment variables** — override config at runtime (12-factor app).
3. **Kubernetes ConfigMaps & Secrets** — inject env-specific config/secrets into pods.
4. **External config server** — **Spring Cloud Config** (centralized, versioned).
5. **Secrets managers** — AWS Secrets Manager, Vault for sensitive values.

```bash
java -jar app.jar --spring.profiles.active=prod
# or
SPRING_PROFILES_ACTIVE=prod
```

> **Cross-question — "Why externalize config instead of bundling it in the jar?"**
> So the **same artifact** can be promoted across environments without rebuilding (build once, deploy anywhere), and so **secrets aren't baked into the image**. It follows the **12-factor** principle of strict config separation.

---

### 74. How do you roll back a failed deployment?

**Kubernetes:**
```bash
kubectl rollout undo deployment/myapp                 # to previous version
kubectl rollout undo deployment/myapp --to-revision=3 # to a specific revision
kubectl rollout status deployment/myapp               # monitor
```
K8s keeps deployment **revision history**, enabling quick rollback.

**Other strategies:**
- **Blue-Green** — switch traffic back to the old (blue) environment instantly.
- **Canary** — roll back by routing all traffic to the stable version.
- **CI/CD rollback job** — redeploy the previous known-good image tag.
- **Database** — handle migrations carefully (backward-compatible changes, separate rollback scripts).

> **Cross-question — "What about database changes during rollback?"**
> App rollback is easy; **DB rollback is risky**. Best practice: make schema changes **backward-compatible** (expand/contract pattern) so the old app version still works with the new schema. Avoid destructive migrations tied to a single release.

---

### 75. Blue-Green vs Rolling deployment

| | Blue-Green | Rolling |
|--|-----------|---------|
| Approach | Two full environments (Blue=current, Green=new); switch traffic all at once | Gradually replace old pods with new ones, a few at a time |
| Downtime | Zero (instant switch) | Zero |
| Rollback | **Instant** (switch back to Blue) | Slower (roll back pods) |
| Resource cost | High (2x environments) | Low (no duplicate env) |
| Risk | All-or-nothing cutover | Gradual; both versions run together briefly |

- **Blue-Green** — deploy to a parallel environment, test it, then flip the router. Fast rollback, but needs double resources.
- **Rolling** — Kubernetes default; replaces pods incrementally. Resource-efficient, but rollback is slower and both versions coexist briefly.

**(Bonus — Canary:** release to a **small % of users** first, monitor, then gradually ramp up. Lowest risk.)

> **Cross-question — "Which would you pick?"**
> **Rolling** for routine updates (efficient, K8s-native). **Blue-Green** when you need **instant rollback** and can afford duplicate infra (critical releases). **Canary** when you want to **limit blast radius** and validate with real traffic gradually.

---

## Testing / Clean Code / Practical

### 76. Difference between JUnit and Mockito

- **JUnit** — a **testing framework** for writing and running tests (lifecycle, assertions, test runners). It's the foundation.
- **Mockito** — a **mocking framework** used **with** JUnit to create **mock objects** for dependencies, so you test a unit in isolation.

| | JUnit | Mockito |
|--|-------|---------|
| Purpose | Write & run tests | Create mocks/stubs of dependencies |
| Role | Test framework | Mocking library |
| Used for | Assertions, test lifecycle | Isolating the unit under test |

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock private PaymentService paymentService;   // Mockito mock
    @InjectMocks private OrderService orderService;

    @Test                                          // JUnit test
    void shouldPlaceOrder() {
        when(paymentService.charge(any())).thenReturn(true);  // Mockito stub
        boolean result = orderService.placeOrder(order);
        assertTrue(result);                                   // JUnit assertion
        verify(paymentService).charge(any());                 // Mockito verify
    }
}
```

> **Cross-question — "Why mock instead of using the real dependency?"**
> To test the unit **in isolation** — fast, deterministic, no DB/network. You control the dependency's behavior and verify interactions, so a failure points to *your* code, not a downstream system.

---

### 77. What should be covered in a unit test for a service layer?

**Cover:**
1. **Happy path** — valid input → expected output.
2. **Edge cases** — empty lists, nulls, boundary values, zero/negative.
3. **Error/exception scenarios** — invalid input, not-found, dependency failures.
4. **Business logic branches** — every `if/else`, calculations, conditions.
5. **Interactions with dependencies** — verify the right methods are called (`verify`).
6. **Validation logic**.

**Mock all external dependencies** (repositories, other services, APIs) — test only the service's logic.

```java
@Test
void shouldThrowWhenUserNotFound() {
    when(userRepository.findById(1L)).thenReturn(Optional.empty());
    assertThrows(ResourceNotFoundException.class,
                 () -> userService.getById(1L));
}
```

> **Cross-question — "Should you aim for 100% coverage?"**
> Coverage is a **guide, not a goal**. Focus on **meaningful tests** of business logic and edge cases. 100% coverage with weak assertions is worse than 80% with strong, behavior-focused tests. Don't chase the number on trivial getters/setters.

---

### 78. When should you mock dependencies?

**Mock when** the dependency is:
- **External** — database, REST APIs, message queues.
- **Slow** — network/IO calls.
- **Non-deterministic** — time, random, third-party services.
- **Hard to set up** — complex state.
- **Has side effects** — sending emails, payments.
- **Not the unit under test** — you're isolating one component.

**Don't mock:**
- The **class under test** itself.
- **Simple value objects / DTOs** (just create real ones).
- **Things you don't own** when it leads to fragile tests (prefer a thin wrapper) — and avoid mocking everything in **integration tests**.

> **Cross-question — "Can you over-mock?"**
> Yes. Over-mocking leads to tests that verify **implementation details** rather than behavior — they break on every refactor and don't catch real bugs. Mock boundaries (I/O), not internal logic.

---

### 79. Unit testing vs Integration testing

| | Unit Testing | Integration Testing |
|--|-------------|---------------------|
| Scope | Single class/method in isolation | Multiple components working together |
| Dependencies | **Mocked** | **Real** (DB, services) |
| Speed | Fast | Slower |
| Purpose | Verify logic of one unit | Verify components integrate correctly |
| Example | Test `OrderService` with mocked repo | Test API → service → real DB flow |
| Spring | Plain JUnit + Mockito | `@SpringBootTest`, Testcontainers |

```java
// Integration test
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerIntegrationTest {
    @Autowired private MockMvc mockMvc;

    @Test
    void shouldCreateUser() throws Exception {
        mockMvc.perform(post("/api/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"name\":\"John\"}"))
            .andExpect(status().isCreated());
    }
}
```

> **Cross-question — "What's the testing pyramid?"**
> **Many unit tests** (fast, cheap) at the base, **fewer integration tests** in the middle, **few end-to-end tests** at the top (slow, brittle). It balances confidence and speed — don't invert it (the "ice cream cone" anti-pattern).

---

### 80. What are SOLID principles? Give one practical example.

**SOLID** = five OOP design principles for maintainable, extensible code:

- **S — Single Responsibility** — a class should have **one reason to change** (one job).
- **O — Open/Closed** — open for **extension**, closed for **modification**.
- **L — Liskov Substitution** — subtypes must be **substitutable** for their base types.
- **I — Interface Segregation** — prefer **small, specific interfaces** over fat ones.
- **D — Dependency Inversion** — depend on **abstractions**, not concretions.

**Practical example — Dependency Inversion + Open/Closed:**
```java
// Depend on abstraction
public interface NotificationSender {
    void send(String message);
}

public class EmailSender implements NotificationSender { ... }
public class SmsSender implements NotificationSender { ... }

public class NotificationService {
    private final NotificationSender sender;   // abstraction, not concrete
    public NotificationService(NotificationSender sender) {
        this.sender = sender;
    }
}
// Add PushSender later WITHOUT modifying NotificationService → Open/Closed.
```

> **Cross-question — "Show a Single Responsibility violation."**
> A `UserService` that handles business logic **and** sends emails **and** generates PDF reports violates SRP — three reasons to change. Split into `UserService`, `EmailService`, `ReportService`. Each changes independently.

---

### 81. What is clean code for you?

Clean code is code that is **easy to read, understand, and maintain** — by others and by your future self.

**Characteristics:**
- **Meaningful names** — variables/methods reveal intent (`calculateTotalPrice` not `calc`).
- **Small, focused functions** — do one thing well.
- **No duplication (DRY)**.
- **Readable** — reads like prose; minimal mental effort.
- **Proper formatting & consistent style**.
- **Minimal, meaningful comments** — code should be self-explanatory; comment the *why*, not the *what*.
- **Good error handling**.
- **Tested**.
- **Follows SOLID** and consistent conventions.

> **Cross-question — "Comments — good or bad?"**
> Comments explaining **what** code does often signal the code isn't clear enough — refactor instead. Comments explaining **why** (business reason, a non-obvious decision, a workaround) are valuable. Avoid redundant or outdated comments.

---

### 82. How do you review code before raising a PR?

**Self-review checklist:**
1. **Re-read my own diff** — does it make sense, any leftover debug code/`System.out.println`?
2. **Functionality** — does it meet the requirement/acceptance criteria?
3. **Tests** — added/updated, and passing?
4. **Edge cases & error handling** covered?
5. **Code quality** — naming, no duplication, SOLID, readable.
6. **No secrets/credentials** committed.
7. **No unrelated changes** — keep PR focused and small.
8. **Run locally** — build passes, lint/static analysis clean.
9. **Meaningful commit messages** and a clear **PR description** (what + why).
10. **Performance/security** implications considered.

> **Cross-question — "What makes a PR easy to review?"**
> **Small, focused, single-purpose** PRs with a clear description, linked ticket, and passing CI. Large PRs (1000+ lines) get rubber-stamped or stall. Keep them reviewable in 15–20 minutes.

---

### 83. How do you debug an issue in production when logs are limited?

**Structured approach:**
1. **Reproduce** — try to reproduce in staging with similar data/config.
2. **Add targeted logging** — deploy more detailed logs around the suspect area (behind a flag/level).
3. **Use observability tools** — metrics (Grafana), distributed tracing (correlation IDs), APM (New Relic/Datadog).
4. **Check recent changes** — recent deploys, config changes, dependency updates (likely culprits).
5. **Narrow scope** — which service, endpoint, user segment, time window?
6. **Check infra** — CPU/memory, DB connections, disk, network, downstream health.
7. **Correlate** — line up error spikes with deploys/traffic/external events.
8. **Examine DB** — slow queries, locks, connection pool exhaustion.

> **Cross-question — "How do you debug without disrupting users?"**
> Use **non-intrusive** methods: read-only log/metric analysis, **feature flags** to toggle behavior, **canary/shadow** traffic, and **correlation IDs** to trace specific requests — rather than attaching debuggers or restarting prod services. Add logging at appropriate levels, then roll forward.

---

### 84. Tell me about a difficult bug you fixed.

*(Use the STAR format — adapt to your real experience. Sample:)*

> **Situation:** Our payment service intermittently created **duplicate transactions** in production, but only under high load — couldn't reproduce locally.
>
> **Task:** Find and fix the root cause without disrupting live payments.
>
> **Action:** I added **correlation-ID logging** and traced requests. I discovered clients were **retrying on timeouts**, and our endpoint wasn't **idempotent** — each retry created a new transaction. I also found a **race condition** where two concurrent requests both passed the "already exists" check before either committed.
>
> **Result:** I implemented an **idempotency key** and a **unique DB constraint** on the transaction reference, plus optimistic locking. Duplicates dropped to zero. I added integration tests simulating concurrent retries to prevent regression.

> **Cross-question — "What did you learn?"**
> The importance of **idempotency** in distributed systems, that **retries are inevitable**, and that some bugs only surface under **concurrency/load** — so load and concurrency testing matter, not just functional tests.

---

### 85. If an API is slow in production, how would you investigate it?

**Systematic approach (say it as layers):**
1. **Identify scope** — one endpoint or all? Specific users/regions? Constant or intermittent?
2. **Check metrics** — response time, throughput, error rate (Grafana/CloudWatch).
3. **Distributed tracing** — find **which span/hop** is slow (the API itself, DB, or a downstream service).
4. **Database** — slow queries (`EXPLAIN`), missing indexes, N+1, locks, connection pool exhaustion.
5. **External calls** — slow third-party APIs/downstream services; check timeouts.
6. **Resources** — CPU, memory, **GC pauses**, thread pool saturation.
7. **Payload size** — large responses, no pagination.
8. **Caching** — is cacheable data being recomputed every time?
9. **Recent changes** — correlate with deploys.

**Common culprits:** N+1 queries, missing indexes, no caching, slow downstream calls, connection pool limits, large payloads, GC pressure.

> **Cross-question — "Walk me through finding a slow DB query."**
> Enable slow-query logging, identify the offending query, run **`EXPLAIN ANALYZE`** to see full scans/wrong index usage, add/fix indexes or rewrite the query, and verify with metrics. If it's an ORM N+1, fix with `JOIN FETCH`/`@EntityGraph`. Then add caching if it's read-heavy.

---

## Best-Fit 12 Questions — Quick Reference

These are cross-referenced to the detailed answers above:

| # | Question | See |
|---|----------|-----|
| 1 | JWT authentication in Spring Boot | [Q21](#21-how-does-jwt-based-authentication-work) + [Q25](#25-how-to-secure-spring-boot-apis-using-spring-security) |
| 2 | N+1 query problem and fix | [Q12](#12-n1-query-problem-and-how-to-solve-it) |
| 3 | Spring Boot vs Spring MVC | [Q3](#3-difference-between-spring-boot-and-spring-mvc) |
| 4 | Design & secure a microservice with REST APIs | [Q6](#6-design-a-rest-api-for-user-management-with-proper-status-codes) + [Q25](#25-how-to-secure-spring-boot-apis-using-spring-security) + [Q31](#31-why-choose-microservices-over-a-monolith) |
| 5 | Kafka consumer crashes before processing | [Q47](#47-what-happens-if-a-consumer-fails-after-reading-a-message) |
| 6 | Authentication vs Authorization | [Q22](#22-authentication-vs-authorization) |
| 7 | Deploy containerized Spring Boot with Docker + K8s | [Q66](#66-basic-flow-of-containerizing-a-spring-boot-application) + [Q68](#68-pods-deployments-services-and-config-maps-in-kubernetes) |
| 8 | AWS services to host a Java microservice | [Q62](#62-how-would-you-deploy-a-spring-boot-app-on-aws) + [Q63](#63-difference-between-ec2-ecs-eks-and-lambda) |
| 9 | Unit tests with JUnit and Mockito | [Q76](#76-difference-between-junit-and-mockito) + [Q77](#77-what-should-be-covered-in-a-unit-test-for-a-service-layer) |
| 10 | useEffect and when it causes bugs | [Q52](#52-what-are-hooks-explain-usestate-and-useeffect) |
| 11 | GitLab CI/CD pipeline stages | [Q72](#72-stages-in-a-gitlab-cicd-pipeline-for-a-spring-boot-app) |
| 12 | Global exception handling in Spring Boot | [Q4](#4-exception-handling-in-spring-boot--global-exception-handling) |

---

### Final Interview Tips
- **Structure answers:** definition → how it works → example → trade-offs.
- **Always mention trade-offs** — shows senior-level thinking.
- **Use real examples** from your experience where the answers say *(adapt to your experience)*.
- **For "how would you" questions**, give a **structured, step-by-step** approach.
- **Anticipate the cross-question** — the follow-ups above are the most common ones interviewers ask.
- **Be honest** if you haven't used something; pivot to what you do know and how you'd learn it.

**Good luck! 🚀**
