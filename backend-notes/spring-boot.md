# Spring Boot Notes: Zero to Advanced  
## Hindi/Hinglish Explanation + English Model Answers + Interview Q&A

---

## Table of Contents

1. [What is Spring Boot?](#1-what-is-spring-boot)
2. [Why Spring Boot?](#2-why-spring-boot)
3. [Spring vs Spring Boot](#3-spring-vs-spring-boot)
4. [Spring Boot Architecture](#4-spring-boot-architecture)
5. [Spring Boot Project Structure](#5-spring-boot-project-structure)
6. [Creating Spring Boot Application](#6-creating-spring-boot-application)
7. [Main Class and `@SpringBootApplication`](#7-main-class-and-springbootapplication)
8. [Spring Boot Starters](#8-spring-boot-starters)
9. [Auto Configuration](#9-auto-configuration)
10. [Embedded Server](#10-embedded-server)
11. [Application Properties and YAML](#11-application-properties-and-yaml)
12. [Dependency Injection in Spring Boot](#12-dependency-injection-in-spring-boot)
13. [Spring Boot Layers](#13-spring-boot-layers)
14. [REST API Development](#14-rest-api-development)
15. [Controller Layer](#15-controller-layer)
16. [Service Layer](#16-service-layer)
17. [Repository Layer](#17-repository-layer)
18. [DTO and Entity](#18-dto-and-entity)
19. [Spring Data JPA](#19-spring-data-jpa)
20. [Database Configuration](#20-database-configuration)
21. [CRUD Operation Example](#21-crud-operation-example)
22. [JPA Entity Relationships](#22-jpa-entity-relationships)
23. [Pagination and Sorting](#23-pagination-and-sorting)
24. [Validation](#24-validation)
25. [Exception Handling](#25-exception-handling)
26. [ResponseEntity](#26-responseentity)
27. [Transactions](#27-transactions)
28. [Profiles](#28-profiles)
29. [Logging](#29-logging)
30. [Actuator](#30-actuator)
31. [Spring Boot DevTools](#31-spring-boot-devtools)
32. [Spring Security Basics](#32-spring-security-basics)
33. [JWT Authentication](#33-jwt-authentication)
34. [CORS and CSRF](#34-cors-and-csrf)
35. [File Upload](#35-file-upload)
36. [Scheduling](#36-scheduling)
37. [Async Processing](#37-async-processing)
38. [Caching](#38-caching)
39. [Testing in Spring Boot](#39-testing-in-spring-boot)
40. [Microservices Basics](#40-microservices-basics)
41. [Spring Cloud Basics](#41-spring-cloud-basics)
42. [Best Practices](#42-best-practices)
43. [Common Interview Questions and Answers](#43-common-interview-questions-and-answers)
44. [Quick Revision Sheet](#44-quick-revision-sheet)

---

# 1. What is Spring Boot?

## Hinglish Explanation

Spring Boot, Spring Framework ka extension hai jo Java applications ko quickly aur easily develop karne ke liye use hota hai.

Spring Framework mein configuration zyada hoti thi. Developer ko manually:

- XML configuration
- Tomcat setup
- Dependency versions
- DispatcherServlet setup
- Database configuration

handle karna padta tha.

Spring Boot ye sab easy bana deta hai.

Spring Boot ka main goal hai:

> Production-ready Spring applications quickly banana with minimum configuration.

## Simple Definition

Spring Boot ek framework hai jo Spring application ko auto-configuration, embedded server aur starter dependencies ke through fast develop karne mein help karta hai.

## English Model Answer

Spring Boot is an extension of the Spring Framework that simplifies the development of production-ready applications. It provides auto-configuration, embedded servers, starter dependencies, externalized configuration, and monitoring features to reduce boilerplate configuration and speed up development.

---

# 2. Why Spring Boot?

## Hinglish Explanation

Spring Boot isliye use hota hai kyunki ye developer ka time save karta hai.

Without Spring Boot:

- Bahut configuration karni padti hai
- External server setup karna padta hai
- Dependency versions manage karne padte hain
- Boilerplate code zyada hota hai

With Spring Boot:

- Auto configuration milti hai
- Embedded Tomcat milta hai
- Starter dependencies milti hain
- Application easily run hoti hai
- REST API banana easy hota hai

## Advantages

| Feature | Benefit |
|---|---|
| Auto Configuration | Manual configuration kam |
| Starter Dependencies | Dependency management easy |
| Embedded Server | No external Tomcat required |
| Actuator | Monitoring easy |
| DevTools | Fast development |
| Production Ready | Health check, metrics |
| Microservice Friendly | Easy deployment |

## English Model Answer

Spring Boot is used because it reduces configuration, simplifies dependency management, provides embedded servers, supports rapid application development, and offers production-ready features such as health checks, metrics, and externalized configuration.

---

# 3. Spring vs Spring Boot

| Spring Framework | Spring Boot |
|---|---|
| Manual configuration required | Auto configuration available |
| External server needed | Embedded server available |
| Dependency management manual | Starter dependencies |
| Setup takes more time | Quick setup |
| XML/Java config needed | Convention over configuration |
| Good for enterprise apps | Good for REST APIs and microservices |

## English Model Answer

Spring is a core framework that provides dependency injection, AOP, transaction management, and web support. Spring Boot is built on top of Spring and simplifies application setup using auto-configuration, embedded servers, and starter dependencies.

---

# 4. Spring Boot Architecture

## Hinglish Explanation

Spring Boot layered architecture follow karta hai.

Common layers:

```text
Client
  |
  v
Controller Layer
  |
  v
Service Layer
  |
  v
Repository Layer
  |
  v
Database
```

## Layer Meaning

| Layer | Work |
|---|---|
| Controller | HTTP request handle karta hai |
| Service | Business logic contain karta hai |
| Repository | Database operations perform karta hai |
| Entity | Database table mapping |
| DTO | Data transfer ke liye use hota hai |

## English Model Answer

Spring Boot applications commonly follow a layered architecture consisting of controller, service, repository, entity, and DTO layers. This separation improves maintainability, testability, and scalability.

---

# 5. Spring Boot Project Structure

## Standard Structure

```text
src
└── main
    ├── java
    │   └── com.example.demo
    │       ├── DemoApplication.java
    │       ├── controller
    │       ├── service
    │       ├── repository
    │       ├── entity
    │       ├── dto
    │       ├── exception
    │       └── config
    └── resources
        ├── application.properties
        ├── static
        └── templates
```

## Hinglish Explanation

Spring Boot mein main class root package mein rakhni chahiye.  
Agar main class `com.example.demo` package mein hai, to Spring automatically uske sub-packages scan karega.

---

# 6. Creating Spring Boot Application

## Ways to Create

1. Spring Initializr
2. IDE like IntelliJ/Eclipse/STS
3. Maven command
4. Gradle command

## Spring Initializr Website

```text
https://start.spring.io
```

## Common Dependencies

For REST API with database:

- Spring Web
- Spring Data JPA
- MySQL Driver/PostgreSQL Driver
- Validation
- Lombok
- Spring Boot DevTools
- Spring Security, if needed

## Maven Example

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```

---

# 7. Main Class and `@SpringBootApplication`

## Example

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

## Hinglish Explanation

`@SpringBootApplication` ek main annotation hai jo Spring Boot app start karta hai.

Ye internally 3 annotations ka combination hai:

```text
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan
```

## Meaning

| Annotation | Meaning |
|---|---|
| `@SpringBootConfiguration` | Configuration class |
| `@EnableAutoConfiguration` | Auto configuration enable |
| `@ComponentScan` | Components scan |

## English Model Answer

`@SpringBootApplication` is a convenience annotation that combines `@SpringBootConfiguration`, `@EnableAutoConfiguration`, and `@ComponentScan`. It marks the main class of a Spring Boot application and enables auto-configuration and component scanning.

---

# 8. Spring Boot Starters

## Hinglish Explanation

Starter dependencies pre-defined dependency groups hote hain.

Example:

Agar hume web application banana hai, to multiple dependencies add karne ke bajaye sirf:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

add karte hain.

Ye automatically Spring MVC, Jackson, Tomcat etc. add kar deta hai.

## Common Starters

| Starter | Use |
|---|---|
| `spring-boot-starter-web` | REST API/Web app |
| `spring-boot-starter-data-jpa` | JPA/database |
| `spring-boot-starter-security` | Security |
| `spring-boot-starter-test` | Testing |
| `spring-boot-starter-validation` | Validation |
| `spring-boot-starter-actuator` | Monitoring |
| `spring-boot-starter-mail` | Email |
| `spring-boot-starter-cache` | Caching |

## English Model Answer

Spring Boot starters are dependency descriptors that bundle commonly used dependencies for a specific functionality. They simplify dependency management and avoid version conflicts.

---

# 9. Auto Configuration

## Hinglish Explanation

Auto configuration ka matlab hai Spring Boot automatically beans configure karta hai based on classpath dependencies.

Example:

Agar project mein `spring-boot-starter-web` hai, to Spring Boot automatically configure karta hai:

- DispatcherServlet
- Embedded Tomcat
- Spring MVC
- Jackson JSON converter
- Error handling

## Example

Aapko manually `DispatcherServlet` configure nahi karna padta.

## Disable Auto Configuration

```java
@SpringBootApplication(exclude = DataSourceAutoConfiguration.class)
public class DemoApplication {
}
```

## English Model Answer

Auto-configuration is a Spring Boot feature that automatically configures application components based on the dependencies available in the classpath. It provides sensible defaults and reduces manual configuration.

---

# 10. Embedded Server

## Hinglish Explanation

Spring Boot embedded server provide karta hai. Iska matlab external Tomcat install karne ki zaroorat nahi hoti.

Default embedded server:

```text
Tomcat
```

Other options:

- Jetty
- Undertow

## Application Run

```bash
mvn spring-boot:run
```

or

```bash
java -jar app.jar
```

## English Model Answer

Spring Boot provides embedded servers such as Tomcat, Jetty, and Undertow. This allows applications to run as standalone JAR files without requiring an external application server.

---

# 11. Application Properties and YAML

## Hinglish Explanation

Spring Boot configuration ke liye `application.properties` ya `application.yml` use hota hai.

## `application.properties`

```properties
server.port=8081
spring.application.name=demo-app
```

## `application.yml`

```yaml
server:
  port: 8081

spring:
  application:
    name: demo-app
```

## Common Properties

```properties
server.port=8080
spring.datasource.url=jdbc:mysql://localhost:3306/testdb
spring.datasource.username=root
spring.datasource.password=root
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

## English Model Answer

Spring Boot supports externalized configuration using properties files, YAML files, environment variables, and command-line arguments. This allows configuration to be changed without modifying source code.

---

# 12. Dependency Injection in Spring Boot

## Hinglish Explanation

Dependency Injection ka matlab hai ek class ki required dependency ko Spring container provide karta hai.

Recommended way:

```text
Constructor Injection
```

## Example

```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
}
```

## Why Constructor Injection?

- Mandatory dependencies clear hoti hain
- Testing easy hoti hai
- Immutability support hoti hai
- Circular dependency avoid karne mein help milti hai

## English Model Answer

Dependency Injection is a design pattern where dependencies are provided externally by the Spring container. Constructor injection is preferred because it makes dependencies explicit, supports immutability, and improves testability.

---

# 13. Spring Boot Layers

## Controller Layer

HTTP request receive karta hai.

```java
@RestController
@RequestMapping("/api/users")
public class UserController {
}
```

## Service Layer

Business logic rakhta hai.

```java
@Service
public class UserService {
}
```

## Repository Layer

Database access karta hai.

```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
}
```

## Entity Layer

Database table mapping.

```java
@Entity
public class User {
}
```

## DTO Layer

Client se data lene/dene ke liye.

```java
public class UserDto {
}
```

---

# 14. REST API Development

## Hinglish Explanation

REST API ek interface hota hai jisse client aur server HTTP ke through communicate karte hain.

## Common HTTP Methods

| Method | Use |
|---|---|
| GET | Data read |
| POST | Data create |
| PUT | Full update |
| PATCH | Partial update |
| DELETE | Data delete |

## REST Controller Example

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping
    public String getUsers() {
        return "All users";
    }

    @PostMapping
    public String createUser() {
        return "User created";
    }
}
```

## English Model Answer

Spring Boot simplifies REST API development using Spring MVC annotations such as `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, and `@DeleteMapping`.

---

# 15. Controller Layer

## Important Annotations

| Annotation | Use |
|---|---|
| `@RestController` | REST API controller |
| `@Controller` | MVC controller |
| `@RequestMapping` | Base URL mapping |
| `@GetMapping` | GET request |
| `@PostMapping` | POST request |
| `@PutMapping` | PUT request |
| `@DeleteMapping` | DELETE request |
| `@PathVariable` | URL path variable |
| `@RequestParam` | Query parameter |
| `@RequestBody` | JSON body to object |

## Example

```java
@RestController
@RequestMapping("/api/products")
public class ProductController {

    @GetMapping("/{id}")
    public String getProduct(@PathVariable Long id) {
        return "Product ID: " + id;
    }

    @GetMapping
    public String searchProduct(@RequestParam String name) {
        return "Product name: " + name;
    }
}
```

## English Model Answer

The controller layer handles incoming HTTP requests, validates input, calls the service layer, and returns responses to the client.

---

# 16. Service Layer

## Hinglish Explanation

Service layer mein business logic hota hai.

Controller ko directly repository call nahi karna chahiye. Controller service ko call karega aur service repository ko.

## Example

```java
@Service
public class ProductService {

    public String getProductName(Long id) {
        return "Laptop";
    }
}
```

## English Model Answer

The service layer contains business logic and acts as a bridge between the controller and repository layers. It improves separation of concerns and makes the application easier to test and maintain.

---

# 17. Repository Layer

## Hinglish Explanation

Repository layer database operations ke liye hoti hai.

Spring Data JPA automatically implementation provide karta hai.

## Example

```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    List<Product> findByName(String name);
}
```

## English Model Answer

The repository layer is responsible for data access. In Spring Data JPA, repository interfaces provide built-in CRUD operations and query methods without requiring manual implementation.

---

# 18. DTO and Entity

## Entity

Entity database table se map hoti hai.

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
}
```

## DTO

DTO client-server data transfer ke liye use hota hai.

```java
public class UserDto {
    private String name;
    private String email;
}
```

## Why DTO?

Entity directly expose nahi karni chahiye because:

- Sensitive data leak ho sakta hai
- API database model se tightly coupled ho jati hai
- Serialization issues aa sakte hain
- API versioning difficult hoti hai

## English Model Answer

An entity represents a database table, while a DTO is used to transfer data between layers or between client and server. DTOs help protect internal data models and provide clean API contracts.

---

# 19. Spring Data JPA

## Hinglish Explanation

Spring Data JPA database operations ko simple banata hai.

Bas repository interface create karo:

```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

Spring automatically CRUD methods provide karega.

## Built-in Methods

| Method | Use |
|---|---|
| `save()` | Insert/update |
| `findById()` | Find by id |
| `findAll()` | Get all |
| `deleteById()` | Delete by id |
| `existsById()` | Check exists |
| `count()` | Count records |

## Query Method Example

```java
List<User> findByName(String name);

Optional<User> findByEmail(String email);

List<User> findByAgeGreaterThan(int age);
```

## Custom Query

```java
@Query("SELECT u FROM User u WHERE u.email = :email")
Optional<User> findUserByEmail(@Param("email") String email);
```

## Native Query

```java
@Query(value = "SELECT * FROM users WHERE email = :email", nativeQuery = true)
Optional<User> findByEmailNative(@Param("email") String email);
```

## English Model Answer

Spring Data JPA simplifies database access by providing repository interfaces such as `JpaRepository`. It supports CRUD operations, derived query methods, JPQL queries, native SQL queries, pagination, and sorting.

---

# 20. Database Configuration

## MySQL Example

```properties
spring.datasource.url=jdbc:mysql://localhost:3306/demo_db
spring.datasource.username=root
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
```

## PostgreSQL Example

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/demo_db
spring.datasource.username=postgres
spring.datasource.password=root

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

## `ddl-auto` Values

| Value | Meaning |
|---|---|
| `none` | No schema change |
| `validate` | Validate schema |
| `update` | Update schema automatically |
| `create` | Create schema every time |
| `create-drop` | Create and drop on shutdown |

## Best Practice

Production mein usually:

```properties
spring.jpa.hibernate.ddl-auto=validate
```

Use Flyway/Liquibase for migrations.

## English Model Answer

Spring Boot configures database connectivity using datasource properties. JPA behavior can be configured using Hibernate properties such as `ddl-auto`, SQL logging, and dialect settings.

---

# 21. CRUD Operation Example

## Entity

```java
@Entity
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;
}
```

## Repository

```java
public interface StudentRepository extends JpaRepository<Student, Long> {
}
```

## Service

```java
@Service
public class StudentService {

    private final StudentRepository repository;

    public StudentService(StudentRepository repository) {
        this.repository = repository;
    }

    public Student create(Student student) {
        return repository.save(student);
    }

    public List<Student> getAll() {
        return repository.findAll();
    }

    public Student getById(Long id) {
        return repository.findById(id)
                .orElseThrow(() -> new RuntimeException("Student not found"));
    }

    public Student update(Long id, Student student) {
        Student existing = getById(id);
        existing.setName(student.getName());
        existing.setEmail(student.getEmail());
        return repository.save(existing);
    }

    public void delete(Long id) {
        repository.deleteById(id);
    }
}
```

## Controller

```java
@RestController
@RequestMapping("/api/students")
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

    @PostMapping
    public Student create(@RequestBody Student student) {
        return service.create(student);
    }

    @GetMapping
    public List<Student> getAll() {
        return service.getAll();
    }

    @GetMapping("/{id}")
    public Student getById(@PathVariable Long id) {
        return service.getById(id);
    }

    @PutMapping("/{id}")
    public Student update(@PathVariable Long id, @RequestBody Student student) {
        return service.update(id, student);
    }

    @DeleteMapping("/{id}")
    public String delete(@PathVariable Long id) {
        service.delete(id);
        return "Student deleted";
    }
}
```

---

# 22. JPA Entity Relationships

## One-to-One

```java
@OneToOne
@JoinColumn(name = "profile_id")
private Profile profile;
```

## One-to-Many

```java
@OneToMany(mappedBy = "user")
private List<Order> orders;
```

## Many-to-One

```java
@ManyToOne
@JoinColumn(name = "user_id")
private User user;
```

## Many-to-Many

```java
@ManyToMany
@JoinTable(
    name = "student_course",
    joinColumns = @JoinColumn(name = "student_id"),
    inverseJoinColumns = @JoinColumn(name = "course_id")
)
private List<Course> courses;
```

## Fetch Types

| Type | Meaning |
|---|---|
| `LAZY` | Data tab load hota hai jab access karte hain |
| `EAGER` | Data immediately load hota hai |

## Cascade Types

| Type | Meaning |
|---|---|
| `PERSIST` | Parent save to child save |
| `MERGE` | Parent update to child update |
| `REMOVE` | Parent delete to child delete |
| `ALL` | All operations cascade |

## English Model Answer

JPA relationships define associations between entities, such as one-to-one, one-to-many, many-to-one, and many-to-many. Fetch type controls when related data is loaded, and cascade type controls how operations are propagated to related entities.

---

# 23. Pagination and Sorting

## Hinglish Explanation

Large data ko ek saath fetch karna bad practice hai. Pagination se data pages mein aata hai.

## Example

```java
@GetMapping
public Page<Student> getStudents(
        @RequestParam int page,
        @RequestParam int size
) {
    Pageable pageable = PageRequest.of(page, size);
    return studentRepository.findAll(pageable);
}
```

## Sorting

```java
Pageable pageable = PageRequest.of(0, 10, Sort.by("name").ascending());
```

## English Model Answer

Pagination and sorting help retrieve large datasets efficiently. Spring Data JPA provides `Pageable`, `PageRequest`, `Page`, and `Sort` for pagination and sorting.

---

# 24. Validation

## Hinglish Explanation

Validation input data ko verify karta hai.

Dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

## DTO Example

```java
public class UserRequest {

    @NotBlank(message = "Name is required")
    private String name;

    @Email(message = "Email should be valid")
    private String email;

    @Min(value = 18, message = "Age must be at least 18")
    private int age;
}
```

## Controller

```java
@PostMapping
public ResponseEntity<String> createUser(@Valid @RequestBody UserRequest request) {
    return ResponseEntity.ok("User created");
}
```

## Common Validation Annotations

| Annotation | Use |
|---|---|
| `@NotNull` | Null not allowed |
| `@NotBlank` | Blank string not allowed |
| `@Email` | Email validation |
| `@Size` | Size validation |
| `@Min` | Minimum value |
| `@Max` | Maximum value |
| `@Pattern` | Regex validation |

## English Model Answer

Spring Boot supports bean validation using Jakarta Validation annotations. Validation is triggered using `@Valid` or `@Validated`, commonly on request DTOs in controller methods.

---

# 25. Exception Handling

## Hinglish Explanation

Exception handling centralized karne ke liye `@RestControllerAdvice` use karte hain.

## Custom Exception

```java
public class ResourceNotFoundException extends RuntimeException {

    public ResourceNotFoundException(String message) {
        super(message);
    }
}
```

## Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<String> handleNotFound(ResourceNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handleGeneral(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Something went wrong");
    }
}
```

## English Model Answer

Spring Boot provides centralized exception handling using `@RestControllerAdvice` and `@ExceptionHandler`. It helps return consistent error responses across the application.

---

# 26. ResponseEntity

## Hinglish Explanation

`ResponseEntity` HTTP response ko control karne ke liye use hota hai.

Isse hum set kar sakte hain:

- Status code
- Headers
- Body

## Example

```java
@GetMapping("/{id}")
public ResponseEntity<Student> getStudent(@PathVariable Long id) {
    Student student = service.getById(id);
    return ResponseEntity.ok(student);
}
```

## Created Response

```java
@PostMapping
public ResponseEntity<Student> create(@RequestBody Student student) {
    Student saved = service.create(student);
    return ResponseEntity.status(HttpStatus.CREATED).body(saved);
}
```

## English Model Answer

`ResponseEntity` represents the complete HTTP response, including status code, headers, and body. It gives developers full control over API responses.

---

# 27. Transactions

## Hinglish Explanation

Transaction ka matlab hai operations ka group jo ya to complete success hona chahiye ya complete rollback.

Example: Bank transfer.

```text
Debit account A
Credit account B
```

Agar credit fail ho jaye to debit rollback hona chahiye.

## Example

```java
@Service
public class BankService {

    @Transactional
    public void transferMoney(Long fromId, Long toId, BigDecimal amount) {
        debit(fromId, amount);
        credit(toId, amount);
    }
}
```

## ACID Properties

| Property | Meaning |
|---|---|
| Atomicity | All or nothing |
| Consistency | Valid state |
| Isolation | Transactions independent |
| Durability | Data permanent after commit |

## English Model Answer

Transaction management ensures data consistency by grouping multiple operations into a single unit of work. Spring Boot supports declarative transaction management using `@Transactional`.

---

# 28. Profiles

## Hinglish Explanation

Profiles environment-specific configuration ke liye use hote hain.

Examples:

- dev
- test
- prod

## Files

```text
application-dev.properties
application-test.properties
application-prod.properties
```

## Activate Profile

```properties
spring.profiles.active=dev
```

## Example

`application-dev.properties`

```properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost:3306/devdb
```

`application-prod.properties`

```properties
server.port=8080
spring.datasource.url=jdbc:mysql://prod-server:3306/proddb
```

## English Model Answer

Spring profiles allow environment-specific beans and configurations. They are commonly used to separate development, testing, staging, and production configurations.

---

# 29. Logging

## Hinglish Explanation

Spring Boot default logging provide karta hai using Logback.

## Example

```java
private static final Logger logger = LoggerFactory.getLogger(UserService.class);

logger.info("User created successfully");
logger.error("Error occurred", exception);
```

## Logging Levels

| Level | Meaning |
|---|---|
| TRACE | Very detailed |
| DEBUG | Debug information |
| INFO | General information |
| WARN | Warning |
| ERROR | Error |

## Configure Logging

```properties
logging.level.root=INFO
logging.level.com.example=DEBUG
logging.file.name=app.log
```

## English Model Answer

Spring Boot uses Logback as the default logging framework. Logging levels such as DEBUG, INFO, WARN, and ERROR can be configured using application properties.

---

# 30. Actuator

## Hinglish Explanation

Actuator production-ready monitoring endpoints provide karta hai.

Dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

## Common Endpoints

| Endpoint | Use |
|---|---|
| `/actuator/health` | Application health |
| `/actuator/info` | App information |
| `/actuator/metrics` | Metrics |
| `/actuator/beans` | Beans |
| `/actuator/mappings` | URL mappings |
| `/actuator/env` | Environment |

## Enable Endpoints

```properties
management.endpoints.web.exposure.include=health,info,metrics
```

## English Model Answer

Spring Boot Actuator provides production-ready endpoints for monitoring and managing applications. It includes health checks, metrics, environment details, beans, and request mappings.

---

# 31. Spring Boot DevTools

## Hinglish Explanation

DevTools development fast banata hai.

Features:

- Auto restart
- Live reload
- Better development defaults

Dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
</dependency>
```

## English Model Answer

Spring Boot DevTools improves developer productivity by providing automatic restart, live reload support, and development-friendly defaults.

---

# 32. Spring Security Basics

## Hinglish Explanation

Spring Security authentication aur authorization ke liye use hota hai.

Authentication:

> User kaun hai?

Authorization:

> User kya access kar sakta hai?

## Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

## Basic Config

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
                .csrf(csrf -> csrf.disable())
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/public/**").permitAll()
                        .anyRequest().authenticated()
                )
                .httpBasic(Customizer.withDefaults())
                .build();
    }
}
```

## Password Encoder

```java
@Bean
public PasswordEncoder passwordEncoder() {
    return new BCryptPasswordEncoder();
}
```

## English Model Answer

Spring Security is a framework for authentication, authorization, and protection against common security attacks. It supports form login, HTTP Basic, JWT, OAuth2, CSRF protection, and method-level security.

---

# 33. JWT Authentication

## Hinglish Explanation

JWT ka full form hai JSON Web Token.

JWT stateless authentication ke liye use hota hai.

## Flow

```text
User login
   |
   v
Server validates username/password
   |
   v
Server generates JWT
   |
   v
Client stores token
   |
   v
Client sends token in Authorization header
   |
   v
Server validates token
   |
   v
Access granted
```

## Header Example

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

## English Model Answer

JWT authentication is a stateless authentication mechanism where the server generates a signed token after successful login. The client sends this token with each request, and the server validates it to authenticate the user.

---

# 34. CORS and CSRF

## CORS

## Hinglish Explanation

CORS ka full form hai Cross-Origin Resource Sharing.

Agar frontend `localhost:3000` par hai aur backend `localhost:8080` par, browser CORS issue de sakta hai.

## Example

```java
@CrossOrigin(origins = "http://localhost:3000")
@RestController
public class UserController {
}
```

## CSRF

CSRF ka full form hai Cross-Site Request Forgery.

Browser-based apps mein CSRF protection useful hota hai. Stateless REST APIs with JWT mein often CSRF disable kiya jata hai.

## English Model Answer

CORS controls whether resources from one origin can be accessed by another origin. CSRF is an attack where a malicious site tricks a user into performing unwanted actions on a trusted site.

---

# 35. File Upload

## Controller Example

```java
@PostMapping("/upload")
public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) {
    String fileName = file.getOriginalFilename();
    return ResponseEntity.ok("Uploaded: " + fileName);
}
```

## Properties

```properties
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=10MB
```

## English Model Answer

Spring Boot supports file upload using `MultipartFile`. File size limits can be configured using multipart properties.

---

# 36. Scheduling

## Hinglish Explanation

Scheduling ka use background tasks run karne ke liye hota hai.

## Enable Scheduling

```java
@EnableScheduling
@SpringBootApplication
public class DemoApplication {
}
```

## Scheduled Task

```java
@Component
public class ReportScheduler {

    @Scheduled(fixedRate = 5000)
    public void generateReport() {
        System.out.println("Report generated");
    }
}
```

## Cron Example

```java
@Scheduled(cron = "0 0 10 * * *")
public void runEveryDayAt10AM() {
}
```

## English Model Answer

Spring Boot supports task scheduling using `@EnableScheduling` and `@Scheduled`. Tasks can be scheduled using fixed rate, fixed delay, or cron expressions.

---

# 37. Async Processing

## Hinglish Explanation

Async processing ka matlab hai task background thread mein run karna.

## Enable Async

```java
@EnableAsync
@Configuration
public class AsyncConfig {
}
```

## Example

```java
@Service
public class EmailService {

    @Async
    public void sendEmail() {
        System.out.println("Sending email in background");
    }
}
```

## English Model Answer

Spring Boot supports asynchronous processing using `@EnableAsync` and `@Async`. It allows methods to run in separate threads without blocking the main execution flow.

---

# 38. Caching

## Hinglish Explanation

Caching frequently used data ko memory mein store karta hai taaki database call reduce ho.

## Enable Cache

```java
@EnableCaching
@SpringBootApplication
public class DemoApplication {
}
```

## Example

```java
@Cacheable("users")
public User getUserById(Long id) {
    return repository.findById(id).orElseThrow();
}
```

## Update Cache

```java
@CachePut(value = "users", key = "#user.id")
public User updateUser(User user) {
    return repository.save(user);
}
```

## Remove Cache

```java
@CacheEvict(value = "users", key = "#id")
public void deleteUser(Long id) {
    repository.deleteById(id);
}
```

## English Model Answer

Caching stores frequently accessed data in memory to reduce expensive operations such as database calls. Spring Boot supports caching using annotations like `@Cacheable`, `@CachePut`, and `@CacheEvict`.

---

# 39. Testing in Spring Boot

## Common Testing Annotations

| Annotation | Use |
|---|---|
| `@SpringBootTest` | Full application context |
| `@WebMvcTest` | Controller layer test |
| `@DataJpaTest` | Repository layer test |
| `@MockBean` | Mock Spring bean |
| `@Test` | Test method |

## Service Test Example

```java
@SpringBootTest
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    void testServiceLoads() {
        assertNotNull(userService);
    }
}
```

## Controller Test Example

```java
@WebMvcTest(UserController.class)
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testGetUsers() throws Exception {
        mockMvc.perform(get("/api/users"))
                .andExpect(status().isOk());
    }
}
```

## English Model Answer

Spring Boot provides testing support through annotations such as `@SpringBootTest`, `@WebMvcTest`, and `@DataJpaTest`. These help test different layers of the application efficiently.

---

# 40. Microservices Basics

## Hinglish Explanation

Microservices architecture mein ek large application ko small independent services mein divide karte hain.

Example:

```text
User Service
Order Service
Payment Service
Notification Service
```

Har service ka apna database ho sakta hai aur independent deploy ho sakti hai.

## Benefits

- Independent deployment
- Easy scaling
- Technology flexibility
- Fault isolation
- Better maintainability

## Challenges

- Distributed complexity
- Network failure
- Data consistency
- Monitoring
- Security
- Service communication

## English Model Answer

Microservices architecture divides an application into small, independent, deployable services. Each service focuses on a specific business capability and can be developed, deployed, and scaled independently.

---

# 41. Spring Cloud Basics

## Hinglish Explanation

Spring Cloud microservices ke liye tools provide karta hai.

## Common Components

| Component | Use |
|---|---|
| Config Server | Centralized configuration |
| Eureka | Service discovery |
| API Gateway | Single entry point |
| OpenFeign | Service communication |
| Resilience4j | Circuit breaker |
| Zipkin | Distributed tracing |

## English Model Answer

Spring Cloud provides tools for building distributed systems and microservices. It supports service discovery, centralized configuration, API gateway, fault tolerance, load balancing, and distributed tracing.

---

# 42. Best Practices

## Spring Boot Best Practices

1. Use constructor injection.
2. Keep controller thin.
3. Put business logic in service layer.
4. Use DTOs instead of exposing entities.
5. Use global exception handling.
6. Use validation for request bodies.
7. Use `ResponseEntity` for proper HTTP responses.
8. Use pagination for large data.
9. Use profiles for environment-specific configuration.
10. Avoid field injection.
11. Avoid circular dependencies.
12. Use `@Transactional` at service layer.
13. Use proper logging instead of `System.out.println`.
14. Do not store secrets in code.
15. Use environment variables for sensitive configuration.
16. Write unit and integration tests.
17. Use Flyway or Liquibase for database migrations.
18. Use actuator for monitoring.
19. Secure APIs properly.
20. Follow clean package structure.

## Recommended Package Structure

```text
com.example.app
├── controller
├── service
├── repository
├── entity
├── dto
├── mapper
├── config
├── security
├── exception
└── util
```

---

# 43. Common Interview Questions and Answers

---

## Q1. What is Spring Boot?

### Hinglish Explanation

Spring Boot Spring Framework ka extension hai jo minimum configuration ke saath production-ready application banane mein help karta hai.

### English Model Answer

Spring Boot is an extension of the Spring Framework that simplifies application development by providing auto-configuration, starter dependencies, embedded servers, and production-ready features.

---

## Q2. Why do we use Spring Boot?

### English Model Answer

We use Spring Boot to reduce boilerplate configuration, simplify dependency management, run applications with embedded servers, and quickly build production-ready applications.

---

## Q3. Difference between Spring and Spring Boot?

### English Model Answer

Spring is a framework that provides dependency injection, AOP, transaction management, and web support. Spring Boot is built on top of Spring and simplifies setup using auto-configuration, starter dependencies, and embedded servers.

---

## Q4. What is `@SpringBootApplication`?

### English Model Answer

`@SpringBootApplication` is a convenience annotation that combines `@SpringBootConfiguration`, `@EnableAutoConfiguration`, and `@ComponentScan`.

---

## Q5. What is auto-configuration?

### English Model Answer

Auto-configuration is a Spring Boot feature that automatically configures beans based on the dependencies available in the classpath.

---

## Q6. What are Spring Boot starters?

### English Model Answer

Spring Boot starters are dependency descriptors that group commonly used dependencies for specific functionality, such as web, JPA, security, and testing.

---

## Q7. What is embedded server?

### English Model Answer

An embedded server is packaged inside the Spring Boot application, allowing it to run as a standalone JAR without requiring an external server.

---

## Q8. What is the default embedded server in Spring Boot?

### English Model Answer

The default embedded server in Spring Boot is Tomcat.

---

## Q9. What is Spring Initializr?

### English Model Answer

Spring Initializr is a web-based tool used to generate Spring Boot project structures with selected dependencies and build configurations.

---

## Q10. What is dependency injection?

### English Model Answer

Dependency Injection is a design pattern where dependencies are provided externally by the Spring container instead of being created inside the class.

---

## Q11. Which dependency injection type is recommended?

### English Model Answer

Constructor injection is recommended because it makes dependencies mandatory, supports immutability, and improves testability.

---

## Q12. What is `@RestController`?

### English Model Answer

`@RestController` is used to create REST APIs. It combines `@Controller` and `@ResponseBody`, so methods return data directly as JSON or XML.

---

## Q13. Difference between `@Controller` and `@RestController`?

### English Model Answer

`@Controller` is used for MVC applications that return views, while `@RestController` is used for REST APIs that return data directly.

---

## Q14. What is `@RequestBody`?

### English Model Answer

`@RequestBody` binds the HTTP request body to a Java object, commonly used to receive JSON data in REST APIs.

---

## Q15. What is `@PathVariable`?

### English Model Answer

`@PathVariable` extracts values from the URI path.

---

## Q16. What is `@RequestParam`?

### English Model Answer

`@RequestParam` extracts query parameters from the request URL.

---

## Q17. What is Spring Data JPA?

### English Model Answer

Spring Data JPA simplifies data access by providing repository interfaces with built-in CRUD operations, query methods, pagination, sorting, and custom queries.

---

## Q18. What is `JpaRepository`?

### English Model Answer

`JpaRepository` is a Spring Data interface that provides CRUD operations, pagination, sorting, batch operations, and JPA-specific methods.

---

## Q19. Difference between `CrudRepository` and `JpaRepository`?

### English Model Answer

`CrudRepository` provides basic CRUD operations, while `JpaRepository` provides additional JPA-specific features such as pagination, sorting, flushing, and batch operations.

---

## Q20. What is an entity?

### English Model Answer

An entity is a Java class mapped to a database table using the `@Entity` annotation.

---

## Q21. What is DTO?

### English Model Answer

DTO stands for Data Transfer Object. It is used to transfer data between layers or between client and server without exposing internal entity models.

---

## Q22. Why should we not expose entities directly?

### English Model Answer

Entities should not be exposed directly because they may contain sensitive fields, create tight coupling with the database model, cause serialization issues, and make API evolution difficult.

---

## Q23. What is validation in Spring Boot?

### English Model Answer

Validation ensures that incoming request data meets defined rules using annotations such as `@NotBlank`, `@Email`, `@Size`, and `@Min`.

---

## Q24. Difference between `@Valid` and `@Validated`?

### English Model Answer

`@Valid` is part of Jakarta Bean Validation and is used for standard validation. `@Validated` is a Spring annotation that supports validation groups.

---

## Q25. What is global exception handling?

### English Model Answer

Global exception handling is a centralized way to handle exceptions across the application using `@RestControllerAdvice` and `@ExceptionHandler`.

---

## Q26. What is `ResponseEntity`?

### English Model Answer

`ResponseEntity` represents the full HTTP response, including status code, headers, and body.

---

## Q27. What is `@Transactional`?

### English Model Answer

`@Transactional` defines transaction boundaries. It ensures that all operations inside a method either complete successfully or roll back in case of failure.

---

## Q28. What are Spring profiles?

### English Model Answer

Spring profiles allow environment-specific configurations and beans, commonly used for development, testing, staging, and production environments.

---

## Q29. What is Spring Boot Actuator?

### English Model Answer

Spring Boot Actuator provides production-ready endpoints for monitoring and managing applications, such as health, info, metrics, beans, and environment.

---

## Q30. What is DevTools?

### English Model Answer

Spring Boot DevTools improves development productivity by providing automatic restart, live reload support, and development-friendly defaults.

---

## Q31. What is Spring Security?

### English Model Answer

Spring Security is a framework for authentication, authorization, and protection against common security attacks.

---

## Q32. Authentication vs Authorization?

### English Model Answer

Authentication verifies who the user is, while authorization determines what the user is allowed to access.

---

## Q33. What is JWT?

### English Model Answer

JWT, or JSON Web Token, is a signed token used for stateless authentication between client and server.

---

## Q34. What is CORS?

### English Model Answer

CORS, or Cross-Origin Resource Sharing, is a browser security mechanism that controls whether resources from one origin can be accessed by another origin.

---

## Q35. What is CSRF?

### English Model Answer

CSRF, or Cross-Site Request Forgery, is an attack where a malicious site tricks a user into performing unwanted actions on a trusted site.

---

## Q36. What is pagination?

### English Model Answer

Pagination is a technique to retrieve large datasets in smaller pages using `Pageable`, `PageRequest`, and `Page`.

---

## Q37. What is lazy loading?

### English Model Answer

Lazy loading delays loading associated data until it is accessed for the first time.

---

## Q38. What is eager loading?

### English Model Answer

Eager loading loads associated data immediately with the parent entity.

---

## Q39. What is N+1 query problem?

### English Model Answer

The N+1 query problem occurs when one query loads parent records and additional queries are executed for each parent to load child records.

---

## Q40. How to solve N+1 query problem?

### English Model Answer

The N+1 problem can be solved using fetch joins, entity graphs, batch fetching, or optimized query design.

---

## Q41. What is caching?

### English Model Answer

Caching stores frequently accessed data in memory to improve performance and reduce database calls.

---

## Q42. What is `@Cacheable`?

### English Model Answer

`@Cacheable` stores the result of a method in cache. Subsequent calls with the same parameters return data from cache instead of executing the method again.

---

## Q43. What is scheduling?

### English Model Answer

Scheduling allows tasks to run automatically at specific intervals or times using `@EnableScheduling` and `@Scheduled`.

---

## Q44. What is async processing?

### English Model Answer

Async processing allows methods to run in separate threads without blocking the main execution flow, using `@EnableAsync` and `@Async`.

---

## Q45. What is the difference between `application.properties` and `application.yml`?

### English Model Answer

Both are used for configuration. `application.properties` uses key-value format, while `application.yml` uses hierarchical YAML format.

---

## Q46. What is externalized configuration?

### English Model Answer

Externalized configuration allows application settings to be stored outside source code using properties files, YAML files, environment variables, or command-line arguments.

---

## Q47. What is `ddl-auto`?

### English Model Answer

`ddl-auto` controls how Hibernate manages database schema generation. Common values are `none`, `validate`, `update`, `create`, and `create-drop`.

---

## Q48. What is Flyway or Liquibase?

### English Model Answer

Flyway and Liquibase are database migration tools used to manage version-controlled schema changes.

---

## Q49. What is microservice architecture?

### English Model Answer

Microservice architecture divides an application into small, independent services that can be developed, deployed, and scaled separately.

---

## Q50. What is Spring Cloud?

### English Model Answer

Spring Cloud provides tools for building distributed systems and microservices, including service discovery, centralized configuration, API gateway, fault tolerance, and distributed tracing.

---

# 44. Quick Revision Sheet

## Spring Boot Core

```text
Spring Boot = Spring + Auto Configuration + Embedded Server + Starters
```

## Important Annotations

| Annotation | Use |
|---|---|
| `@SpringBootApplication` | Main application annotation |
| `@RestController` | REST API controller |
| `@Service` | Service layer bean |
| `@Repository` | Repository layer bean |
| `@Entity` | Database table mapping |
| `@Autowired` | Dependency injection |
| `@Bean` | Manual bean creation |
| `@Configuration` | Configuration class |
| `@Transactional` | Transaction management |
| `@Valid` | Validation |
| `@RestControllerAdvice` | Global exception handling |
| `@Scheduled` | Scheduling |
| `@Async` | Async execution |
| `@Cacheable` | Caching |

## REST API Flow

```text
Client Request
   |
   v
Controller
   |
   v
Service
   |
   v
Repository
   |
   v
Database
```

## Database Flow

```text
Entity
   |
   v
Repository
   |
   v
Spring Data JPA
   |
   v
Hibernate
   |
   v
JDBC
   |
   v
Database
```

## Best Interview Definition

> Spring Boot is a framework built on top of Spring that simplifies application development using auto-configuration, starter dependencies, embedded servers, and production-ready features. It helps developers create standalone, scalable, and production-ready applications with minimal configuration.

---

# Final Study Tips

1. Pehle Spring Core samjho: IoC, DI, Bean, ApplicationContext.
2. Fir Spring Boot samjho: auto configuration, starters, embedded server.
3. REST API practice karo.
4. JPA CRUD operations implement karo.
5. Validation and exception handling add karo.
6. Security and JWT basics samjho.
7. Testing, actuator, profiles, logging revise karo.
8. Interview ke liye English model answers practice karo.

---
