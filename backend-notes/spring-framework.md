# Spring Framework Notes: Zero to Advanced  
### Hinglish Explanation + English Model Answers + Interview Q&A

---

## Table of Contents

1. [What is Spring?](#1-what-is-spring)
2. [Why Spring?](#2-why-spring)
3. [Spring Core Concepts](#3-spring-core-concepts)
4. [IoC Container](#4-ioc-container)
5. [Dependency Injection](#5-dependency-injection)
6. [Bean in Spring](#6-bean-in-spring)
7. [Bean Scopes](#7-bean-scopes)
8. [Bean Lifecycle](#8-bean-lifecycle)
9. [Spring Configuration Types](#9-spring-configuration-types)
10. [Annotations in Spring](#10-annotations-in-spring)
11. [Component Scanning](#11-component-scanning)
12. [Autowiring](#12-autowiring)
13. [Spring AOP](#13-spring-aop)
14. [Spring JDBC](#14-spring-jdbc)
15. [Spring ORM and JPA](#15-spring-orm-and-jpa)
16. [Spring MVC](#16-spring-mvc)
17. [REST API with Spring](#17-rest-api-with-spring)
18. [Exception Handling](#18-exception-handling)
19. [Validation](#19-validation)
20. [Spring Boot](#20-spring-boot)
21. [Spring Boot Auto Configuration](#21-spring-boot-auto-configuration)
22. [Spring Boot Starter](#22-spring-boot-starter)
23. [Spring Boot Actuator](#23-spring-boot-actuator)
24. [Spring Data JPA](#24-spring-data-jpa)
25. [Transactions](#25-transactions)
26. [Spring Security](#26-spring-security)
27. [JWT Authentication](#27-jwt-authentication)
28. [Spring Testing](#28-spring-testing)
29. [Spring Profiles](#29-spring-profiles)
30. [Spring Cloud Basics](#30-spring-cloud-basics)
31. [Microservices with Spring Boot](#31-microservices-with-spring-boot)
32. [Best Practices](#32-best-practices)
33. [Common Interview Questions and Answers](#33-common-interview-questions-and-answers)

---

# 1. What is Spring?

## Hinglish Explanation

Spring ek powerful Java framework hai jo enterprise-level applications banane ke liye use hota hai.  
Iska main goal hai Java development ko simple, flexible aur maintainable banana.

Spring mainly help karta hai:

- Object creation manage karne mein
- Dependency injection provide karne mein
- Loose coupling achieve karne mein
- Database work simplify karne mein
- Web applications aur REST APIs banane mein
- Security, transactions, testing, microservices handle karne mein

Simple words mein:

> Spring ek container provide karta hai jo objects banata hai, unki dependencies inject karta hai aur application lifecycle manage karta hai.

## English Model Answer

Spring is a lightweight, open-source Java framework used to build enterprise applications. It provides infrastructure support for dependency injection, aspect-oriented programming, transaction management, web development, data access, security, and microservices. The main goal of Spring is to simplify Java development by promoting loose coupling and testable code.

---

# 2. Why Spring?

## Hinglish Explanation

Without Spring, developer ko object creation, dependency management, transaction handling, database connection, security sab manually handle karna padta tha.

Spring ye sab kaam easy bana deta hai.

## Benefits

| Feature | Benefit |
|---|---|
| Dependency Injection | Loose coupling |
| IoC Container | Object lifecycle management |
| AOP | Cross-cutting concerns handle karta hai |
| Spring MVC | Web app development easy |
| Spring Boot | Fast setup and production-ready apps |
| Spring Data JPA | Database operations simple |
| Spring Security | Authentication and authorization |
| Testing Support | Unit and integration testing easy |

## English Model Answer

Spring is used because it reduces boilerplate code, promotes loose coupling, improves testability, provides transaction management, integrates easily with databases and web frameworks, and supports building scalable enterprise applications.

---

# 3. Spring Core Concepts

Spring ke core concepts:

1. IoC - Inversion of Control
2. DI - Dependency Injection
3. Bean
4. ApplicationContext
5. BeanFactory
6. AOP
7. Autowiring
8. Configuration

---

# 4. IoC Container

## Hinglish Explanation

IoC ka full form hai **Inversion of Control**.

Normally Java mein object developer create karta hai:

```java
Car car = new Car();
```

Spring mein object creation ka control developer se Spring container ke paas chala jata hai.

Isliye ise Inversion of Control kehte hain.

Spring container:

- Object create karta hai
- Dependencies inject karta hai
- Bean lifecycle manage karta hai
- Object destroy karta hai

## Types of IoC Containers

### 1. BeanFactory

Basic container hai. Lazy initialization karta hai.

### 2. ApplicationContext

Advanced container hai. Mostly real projects mein ye use hota hai.

Features:

- Bean management
- Event handling
- Internationalization
- Annotation support
- AOP support

## English Model Answer

IoC means Inversion of Control. It is a design principle where the control of object creation and dependency management is transferred from the application code to the Spring container. The Spring IoC container creates objects, wires dependencies, manages lifecycle, and provides configured beans to the application.

---

# 5. Dependency Injection

## Hinglish Explanation

Dependency Injection ka matlab hai object ki required dependency ko bahar se provide karna.

Example:

Agar `Car` class ko `Engine` chahiye, to Car khud Engine create nahi karegi. Spring Engine object bana ke Car mein inject karega.

## Without DI

```java
class Car {
    private Engine engine = new Engine();
}
```

Problem:

- Tight coupling
- Testing difficult
- Code flexibility low

## With DI

```java
class Car {
    private Engine engine;

    public Car Engine(Engine engine) {
        this.engine = engine;
    }
}
```

Better:

- Loose coupling
- Easy testing
- Easy maintenance

## Types of Dependency Injection

### 1. Constructor Injection

```java
@Component
class Car {
    private final Engine engine;

    @Autowired
    public Car(Engine engine) {
        this.engine = engine;
    }
}
```

Recommended for mandatory dependencies.

### 2. Setter Injection

```java
@Component
class Car {
    private Engine engine;

    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

Useful for optional dependencies.

### 3. Field Injection

```java
@Component
class Car {
    @Autowired
    private Engine engine;
}
```

Easy but not recommended because testing difficult hoti hai.

## English Model Answer

Dependency Injection is a design pattern where dependencies required by a class are provided externally instead of being created inside the class. Spring supports constructor injection, setter injection, and field injection. Constructor injection is preferred because it supports immutability, mandatory dependencies, and easier testing.

---

# 6. Bean in Spring

## Hinglish Explanation

Spring ke andar jo object Spring container manage karta hai usse **Bean** kehte hain.

Example:

```java
@Component
class StudentService {
}
```

Yaha `StudentService` Spring bean ban jayega.

Spring bean:

- Spring container create karta hai
- Dependencies inject hoti hain
- Lifecycle Spring manage karta hai

## Ways to Create Bean

### Using `@Component`

```java
@Component
public class UserService {
}
```

### Using `@Bean`

```java
@Configuration
public class AppConfig {

    @Bean
    public UserService userService() {
        return new UserService();
    }
}
```

### Using XML

```xml
<bean id="userService" class="com.example.UserService"/>
```

## English Model Answer

A Spring bean is an object that is created, configured, and managed by the Spring IoC container. Beans can be declared using annotations such as `@Component`, Java configuration using `@Bean`, or XML configuration.

---

# 7. Bean Scopes

## Hinglish Explanation

Bean scope decide karta hai ki Spring bean ka object kitni baar create hoga aur kitna time live karega.

## Common Bean Scopes

| Scope | Meaning |
|---|---|
| singleton | One object per Spring container |
| prototype | New object every time requested |
| request | One object per HTTP request |
| session | One object per HTTP session |
| application | One object per ServletContext |
| websocket | One object per WebSocket session |

## Singleton Scope

Default scope hota hai.

```java
@Component
@Scope("singleton")
class UserService {
}
```

Same object baar-baar return hota hai.

## Prototype Scope

```java
@Component
@Scope("prototype")
class UserService {
}
```

Har request par new object milega.

## English Model Answer

Bean scope defines the lifecycle and visibility of a Spring bean. The default scope is singleton, where only one instance is created per Spring container. Prototype creates a new instance every time the bean is requested. Web-aware scopes include request, session, application, and websocket.

---

# 8. Bean Lifecycle

## Hinglish Explanation

Spring bean lifecycle ka matlab hai bean create hone se destroy hone tak ka process.

## Lifecycle Steps

1. Bean definition read hoti hai
2. Object create hota hai
3. Dependencies inject hoti hain
4. Aware interfaces call hote hain
5. BeanPostProcessor before initialization
6. Initialization method call hota hai
7. Bean ready to use
8. Destroy method call hota hai

## Example

```java
@Component
public class MyBean {

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("Bean destroyed");
    }
}
```

## English Model Answer

The Spring bean lifecycle includes bean instantiation, dependency injection, aware callbacks, bean post-processing, initialization, usage, and destruction. Spring provides lifecycle callbacks such as `@PostConstruct`, `@PreDestroy`, `InitializingBean`, `DisposableBean`, and custom init/destroy methods.

---

# 9. Spring Configuration Types

Spring mein configuration 3 tarah se kar sakte hain:

## 1. XML Configuration

Old style.

```xml
<bean id="student" class="com.example.Student"/>
```

## 2. Annotation Configuration

```java
@Component
public class Student {
}
```

## 3. Java-Based Configuration

```java
@Configuration
public class AppConfig {

    @Bean
    public Student student() {
        return new Student();
    }
}
```

## English Model Answer

Spring supports XML-based configuration, annotation-based configuration, and Java-based configuration. Modern Spring applications mostly use annotation and Java-based configuration because they are type-safe, readable, and easier to maintain.

---

# 10. Annotations in Spring

## Important Annotations

| Annotation | Use |
|---|---|
| `@Component` | Generic Spring bean |
| `@Service` | Service layer bean |
| `@Repository` | DAO layer bean |
| `@Controller` | MVC controller |
| `@RestController` | REST API controller |
| `@Autowired` | Dependency injection |
| `@Qualifier` | Select specific bean |
| `@Primary` | Default bean preference |
| `@Bean` | Define bean manually |
| `@Configuration` | Configuration class |
| `@ComponentScan` | Scan components |
| `@Value` | Inject property value |
| `@Scope` | Define bean scope |
| `@PostConstruct` | Init callback |
| `@PreDestroy` | Destroy callback |

---

# 11. Component Scanning

## Hinglish Explanation

Component scanning ka matlab hai Spring automatically classes ko scan karta hai jinke upar annotations lage hote hain like:

- `@Component`
- `@Service`
- `@Repository`
- `@Controller`

Example:

```java
@Configuration
@ComponentScan(basePackages = "com.example")
public class AppConfig {
}
```

Spring `com.example` package ke andar beans discover karega.

## English Model Answer

Component scanning is a mechanism in Spring that automatically detects classes annotated with stereotype annotations such as `@Component`, `@Service`, `@Repository`, and `@Controller`, and registers them as beans in the Spring container.

---

# 12. Autowiring

## Hinglish Explanation

Autowiring ka matlab hai Spring automatically dependency inject karega.

Example:

```java
@Service
public class UserService {

    private final UserRepository repository;

    @Autowired
    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

Spring automatically `UserRepository` ka bean find karega aur inject karega.

## Autowiring Conflict

Agar same type ke multiple beans ho:

```java
@Component
class PetrolEngine implements Engine {
}

@Component
class DieselEngine implements Engine {
}
```

Spring confuse ho jayega.

Solution:

### Use `@Qualifier`

```java
@Autowired
public Car(@Qualifier("dieselEngine") Engine engine) {
    this.engine = engine;
}
```

### Use `@Primary`

```java
@Component
@Primary
class DieselEngine implements Engine {
}
```

## English Model Answer

Autowiring is Spring's feature for automatically resolving and injecting bean dependencies. If multiple beans of the same type exist, ambiguity can be resolved using `@Qualifier` or `@Primary`.

---

# 13. Spring AOP

## Hinglish Explanation

AOP ka full form hai **Aspect-Oriented Programming**.

AOP ka use cross-cutting concerns handle karne ke liye hota hai.

Cross-cutting concerns:

- Logging
- Security
- Transaction management
- Auditing
- Performance monitoring

Ye concerns multiple classes mein repeat hote hain. AOP se hum common logic separate rakh sakte hain.

## Important AOP Terms

| Term | Meaning |
|---|---|
| Aspect | Common logic class |
| Advice | Action taken by aspect |
| Join Point | Method execution point |
| Pointcut | Expression to select methods |
| Weaving | Applying aspect to target object |
| Proxy | Wrapper object created by Spring |

## Advice Types

| Advice | Meaning |
|---|---|
| `@Before` | Method ke pehle |
| `@After` | Method ke baad |
| `@AfterReturning` | Successful return ke baad |
| `@AfterThrowing` | Exception ke baad |
| `@Around` | Before and after both |

## Example

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore() {
        System.out.println("Method execution started");
    }
}
```

## English Model Answer

Spring AOP allows separation of cross-cutting concerns such as logging, security, and transaction management from business logic. It uses proxies to apply aspects to target objects at runtime. Common AOP concepts include aspect, advice, join point, pointcut, and weaving.

---

# 14. Spring JDBC

## Hinglish Explanation

Spring JDBC database operations ko simple banata hai. Normal JDBC mein boilerplate code zyada hota hai:

- Connection open
- Statement create
- Query execute
- ResultSet handle
- Exception handle
- Connection close

Spring JDBC `JdbcTemplate` provide karta hai jo ye sab internally handle karta hai.

## Example

```java
@Repository
public class UserDao {

    private final JdbcTemplate jdbcTemplate;

    public UserDao(JdbcTemplate jdbcTemplate) {
        this.jdbcTemplate = jdbcTemplate;
    }

    public int countUsers() {
        return jdbcTemplate.queryForObject("SELECT COUNT(*) FROM users", Integer.class);
    }
}
```

## English Model Answer

Spring JDBC simplifies database access by reducing boilerplate JDBC code. `JdbcTemplate` handles connection management, statement execution, result processing, exception translation, and resource cleanup.

---

# 15. Spring ORM and JPA

## Hinglish Explanation

ORM ka matlab hai **Object Relational Mapping**.

Iska kaam Java objects ko database tables ke saath map karna hai.

Example:

```java
@Entity
public class User {

    @Id
    private Long id;

    private String name;
}
```

Yaha `User` class database table ban sakti hai.

## JPA

JPA ek specification hai. Hibernate uska implementation hai.

## Common JPA Annotations

| Annotation | Use |
|---|---|
| `@Entity` | Class as database entity |
| `@Table` | Table name |
| `@Id` | Primary key |
| `@GeneratedValue` | Auto generation |
| `@Column` | Column mapping |
| `@OneToOne` | One-to-one relation |
| `@OneToMany` | One-to-many relation |
| `@ManyToOne` | Many-to-one relation |
| `@ManyToMany` | Many-to-many relation |

## English Model Answer

ORM maps Java objects to relational database tables. JPA is a specification for ORM in Java, while Hibernate is a popular implementation of JPA. Spring integrates with JPA to simplify persistence operations.

---

# 16. Spring MVC

## Hinglish Explanation

Spring MVC ek web framework hai jo Model-View-Controller pattern follow karta hai.

## MVC Components

| Component | Meaning |
|---|---|
| Model | Data |
| View | UI |
| Controller | Request handle karta hai |

## Request Flow

1. Client request send karta hai
2. DispatcherServlet request receive karta hai
3. HandlerMapping controller find karta hai
4. Controller business logic call karta hai
5. Model return hota hai
6. ViewResolver view find karta hai
7. Response client ko send hota hai

## Example

```java
@Controller
public class HomeController {

    @GetMapping("/home")
    public String home(Model model) {
        model.addAttribute("message", "Welcome");
        return "home";
    }
}
```

## English Model Answer

Spring MVC is a web framework based on the Model-View-Controller design pattern. The `DispatcherServlet` acts as the front controller and delegates requests to controllers. Controllers process requests, interact with services, and return views or responses.

---

# 17. REST API with Spring

## Hinglish Explanation

REST API banane ke liye Spring mein `@RestController` use hota hai.

`@RestController` = `@Controller` + `@ResponseBody`

## Example

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) {
        return new User(id, "Rahul");
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return user;
    }
}
```

## Common REST Annotations

| Annotation | Use |
|---|---|
| `@RestController` | REST controller |
| `@RequestMapping` | Base mapping |
| `@GetMapping` | GET request |
| `@PostMapping` | POST request |
| `@PutMapping` | PUT request |
| `@DeleteMapping` | DELETE request |
| `@PathVariable` | URL variable |
| `@RequestParam` | Query parameter |
| `@RequestBody` | JSON to object |
| `@ResponseBody` | Object to JSON |

## English Model Answer

Spring provides REST API support through `@RestController`. It automatically serializes Java objects to JSON and deserializes JSON requests to Java objects using message converters such as Jackson.

---

# 18. Exception Handling

## Hinglish Explanation

Exception handling ke liye Spring mein `@ControllerAdvice` aur `@ExceptionHandler` use karte hain.

## Example

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<String> handleUserNotFound(UserNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(ex.getMessage());
    }
}
```

## English Model Answer

Spring provides centralized exception handling using `@ControllerAdvice` or `@RestControllerAdvice`. Specific exceptions can be handled using `@ExceptionHandler`, allowing consistent error responses across the application.

---

# 19. Validation

## Hinglish Explanation

Validation ka use input data verify karne ke liye hota hai.

Example:

```java
public class UserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Min(18)
    private int age;
}
```

Controller:

```java
@PostMapping("/users")
public ResponseEntity<String> createUser(@Valid @RequestBody UserRequest request) {
    return ResponseEntity.ok("User created");
}
```

## Common Validation Annotations

| Annotation | Use |
|---|---|
| `@NotNull` | Null not allowed |
| `@NotBlank` | Empty string not allowed |
| `@Email` | Valid email |
| `@Min` | Minimum value |
| `@Max` | Maximum value |
| `@Size` | Size limit |
| `@Pattern` | Regex validation |

## English Model Answer

Spring supports bean validation using Jakarta Validation annotations such as `@NotNull`, `@NotBlank`, `@Email`, and `@Size`. Validation can be triggered using `@Valid` or `@Validated`.

---

# 20. Spring Boot

## Hinglish Explanation

Spring Boot, Spring Framework ka extension hai jo application setup ko easy banata hai.

Spring Boot ka main goal:

- Less configuration
- Fast development
- Embedded server
- Auto configuration
- Production-ready features

Without Spring Boot, developer ko Tomcat setup, XML config, dependency versions sab manually handle karna padta tha.

Spring Boot mein:

```java
@SpringBootApplication
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

## English Model Answer

Spring Boot is an extension of the Spring Framework that simplifies application development by providing auto-configuration, embedded servers, starter dependencies, externalized configuration, and production-ready features.

---

# 21. Spring Boot Auto Configuration

## Hinglish Explanation

Auto configuration ka matlab hai Spring Boot automatically required beans configure karta hai based on classpath dependencies.

Example:

Agar project mein `spring-boot-starter-web` hai, to Spring Boot automatically:

- DispatcherServlet
- Embedded Tomcat
- Jackson
- Spring MVC

configure kar deta hai.

## Annotation

```java
@EnableAutoConfiguration
```

`@SpringBootApplication` ke andar ye already included hota hai.

## English Model Answer

Spring Boot auto-configuration automatically configures Spring beans based on the dependencies available in the classpath. It reduces manual configuration and provides sensible defaults.

---

# 22. Spring Boot Starter

## Hinglish Explanation

Starter dependencies pre-defined dependency groups hote hain.

Example:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Is single dependency se web app ke liye required dependencies aa jati hain.

## Common Starters

| Starter | Use |
|---|---|
| `spring-boot-starter-web` | Web and REST APIs |
| `spring-boot-starter-data-jpa` | JPA and database |
| `spring-boot-starter-security` | Security |
| `spring-boot-starter-test` | Testing |
| `spring-boot-starter-actuator` | Monitoring |
| `spring-boot-starter-validation` | Validation |

## English Model Answer

Spring Boot starters are dependency descriptors that group commonly used dependencies for specific functionality. They simplify dependency management and reduce version conflicts.

---

# 23. Spring Boot Actuator

## Hinglish Explanation

Actuator production-ready monitoring endpoints provide karta hai.

Common endpoints:

| Endpoint | Use |
|---|---|
| `/actuator/health` | App health |
| `/actuator/info` | App info |
| `/actuator/metrics` | Metrics |
| `/actuator/env` | Environment |
| `/actuator/beans` | Beans info |
| `/actuator/mappings` | Request mappings |

## Dependency

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

## English Model Answer

Spring Boot Actuator provides production-ready monitoring and management endpoints such as health, metrics, info, beans, environment, and mappings. It helps monitor and manage applications in production.

---

# 24. Spring Data JPA

## Hinglish Explanation

Spring Data JPA database operations ko extremely simple bana deta hai.

Repository interface create karo:

```java
public interface UserRepository extends JpaRepository<User, Long> {
    List<User> findByName(String name);
}
```

Spring automatically implementation provide karega.

## Repository Types

| Interface | Use |
|---|---|
| `CrudRepository` | Basic CRUD |
| `PagingAndSortingRepository` | Pagination and sorting |
| `JpaRepository` | Full JPA features |

## Query Methods

```java
List<User> findByEmail(String email);

List<User> findByAgeGreaterThan(int age);

List<User> findByNameContaining(String keyword);
```

## Custom Query

```java
@Query("SELECT u FROM User u WHERE u.email = :email")
User getUserByEmail(@Param("email") String email);
```

## English Model Answer

Spring Data JPA simplifies data access by providing repository interfaces such as `JpaRepository`. It automatically implements common CRUD operations and supports query methods, pagination, sorting, and custom JPQL or native queries.

---

# 25. Transactions

## Hinglish Explanation

Transaction ka matlab hai operations ka group jo completely success ya completely fail hona chahiye.

Example:

Bank transfer:

1. Account A se money debit
2. Account B mein money credit

Agar credit fail ho gaya to debit bhi rollback hona chahiye.

## `@Transactional`

```java
@Service
public class BankService {

    @Transactional
    public void transferMoney(Long from, Long to, BigDecimal amount) {
        debit(from, amount);
        credit(to, amount);
    }
}
```

## ACID Properties

| Property | Meaning |
|---|---|
| Atomicity | All or nothing |
| Consistency | Valid state maintain |
| Isolation | Transactions independent |
| Durability | Committed data permanent |

## Propagation Types

| Propagation | Meaning |
|---|---|
| REQUIRED | Existing transaction use karo, nahi hai to new create karo |
| REQUIRES_NEW | Always new transaction |
| SUPPORTS | Transaction ho to use karo |
| NOT_SUPPORTED | Transaction ke bina run karo |
| MANDATORY | Existing transaction required |
| NEVER | Transaction nahi hona chahiye |
| NESTED | Nested transaction |

## English Model Answer

Transaction management ensures data consistency by grouping operations into a single unit of work. Spring provides declarative transaction management using `@Transactional`. It supports propagation, isolation, rollback rules, and integration with JDBC, JPA, and other transaction managers.

---

# 26. Spring Security

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

## Basic Security Config

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
                .build();
    }
}
```

## English Model Answer

Spring Security is a powerful framework for authentication, authorization, and protection against common security attacks. It integrates with Spring applications and supports form login, HTTP Basic, JWT, OAuth2, method-level security, CSRF protection, and role-based access control.

---

# 27. JWT Authentication

## Hinglish Explanation

JWT ka full form hai JSON Web Token.

JWT stateless authentication ke liye use hota hai.

## Flow

1. User login karta hai
2. Server username/password verify karta hai
3. Server JWT token generate karta hai
4. Client token store karta hai
5. Client har request ke saath token bhejta hai
6. Server token validate karta hai
7. Access allow ya deny hota hai

## Header Example

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

## English Model Answer

JWT authentication is a stateless authentication mechanism where the server generates a signed token after successful login. The client sends this token with each request, and the server validates it to authenticate the user without storing session state.

---

# 28. Spring Testing

## Hinglish Explanation

Spring testing support provide karta hai unit testing aur integration testing ke liye.

## Common Testing Annotations

| Annotation | Use |
|---|---|
| `@SpringBootTest` | Full application context test |
| `@WebMvcTest` | Controller layer test |
| `@DataJpaTest` | Repository layer test |
| `@MockBean` | Mock Spring bean |
| `@Test` | Test method |
| `@BeforeEach` | Before every test |

## Example

```java
@SpringBootTest
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    void testUserCreation() {
        assertNotNull(userService);
    }
}
```

## English Model Answer

Spring provides strong testing support through annotations such as `@SpringBootTest`, `@WebMvcTest`, and `@DataJpaTest`. It supports unit testing, integration testing, mocking, and loading application contexts for tests.

---

# 29. Spring Profiles

## Hinglish Explanation

Profiles ka use different environments ke liye different configuration maintain karne ke liye hota hai.

Example:

- dev
- test
- prod

## application-dev.properties

```properties
server.port=8081
spring.datasource.url=jdbc:mysql://localhost/devdb
```

## application-prod.properties

```properties
server.port=8080
spring.datasource.url=jdbc:mysql://prod-server/proddb
```

## Activate Profile

```properties
spring.profiles.active=dev
```

## English Model Answer

Spring profiles allow applications to define environment-specific configurations. Profiles can be used for development, testing, staging, and production environments. A profile can be activated using `spring.profiles.active`.

---

# 30. Spring Cloud Basics

## Hinglish Explanation

Spring Cloud microservices ke liye tools provide karta hai.

Important components:

| Component | Use |
|---|---|
| Config Server | Centralized configuration |
| Eureka | Service discovery |
| Gateway | API gateway |
| OpenFeign | Service-to-service communication |
| Resilience4j | Fault tolerance |
| Sleuth/Zipkin | Distributed tracing |

## English Model Answer

Spring Cloud provides tools for building distributed systems and microservices. It supports service discovery, centralized configuration, API gateway, load balancing, fault tolerance, and distributed tracing.

---

# 31. Microservices with Spring Boot

## Hinglish Explanation

Microservices architecture mein application ko small independent services mein divide kiya jata hai.

Example:

- User Service
- Order Service
- Payment Service
- Notification Service

Har service independently deploy ho sakti hai.

## Key Concepts

| Concept | Meaning |
|---|---|
| Service Discovery | Services ko locate karna |
| API Gateway | Single entry point |
| Config Server | Central config |
| Circuit Breaker | Failure handle karna |
| Distributed Tracing | Request tracking |
| Load Balancing | Traffic distribute karna |

## English Model Answer

Microservices architecture divides an application into small, independent, deployable services. Spring Boot and Spring Cloud provide tools to build, configure, discover, secure, and monitor microservices.

---

# 32. Best Practices

## Spring Best Practices

1. Prefer constructor injection over field injection.
2. Keep controllers thin.
3. Put business logic in service layer.
4. Use DTOs instead of exposing entities directly.
5. Use global exception handling.
6. Validate request bodies.
7. Use profiles for environment-specific config.
8. Avoid circular dependencies.
9. Use transactions at service layer.
10. Do not put business logic in repositories.
11. Use pagination for large data.
12. Secure sensitive endpoints.
13. Keep configuration externalized.
14. Write unit and integration tests.
15. Use meaningful package structure.

## Recommended Layered Architecture

```text
controller
   ↓
service
   ↓
repository
   ↓
database
```

## Example Package Structure

```text
com.example.project
├── controller
├── service
├── repository
├── entity
├── dto
├── config
├── exception
└── security
```

---

# 33. Common Interview Questions and Answers

---

## Q1. What is Spring Framework?

### Hinglish Explanation

Spring Java ka ek framework hai jo enterprise applications ko easy banata hai. Ye dependency injection, AOP, transaction, web, security, database integration jaise features provide karta hai.

### English Model Answer

Spring Framework is a lightweight Java framework used to build enterprise applications. It provides support for dependency injection, aspect-oriented programming, transaction management, web development, data access, and security.

---

## Q2. What is IoC?

### Hinglish Explanation

IoC ka matlab hai object creation ka control developer ke paas nahi balki Spring container ke paas hota hai.

### English Model Answer

IoC, or Inversion of Control, is a principle where the control of object creation and dependency management is transferred from application code to the Spring container.

---

## Q3. What is Dependency Injection?

### Hinglish Explanation

Dependency Injection mein class apni dependency khud create nahi karti. Dependency bahar se provide ki jati hai, mostly Spring container ke through.

### English Model Answer

Dependency Injection is a design pattern where dependencies of a class are provided externally instead of being created inside the class. It improves loose coupling and testability.

---

## Q4. Constructor Injection vs Field Injection?

### English Model Answer

Constructor injection is preferred because it makes dependencies mandatory, supports immutability, and improves testability. Field injection is easier to write but makes testing difficult and hides dependencies.

---

## Q5. What is a Spring Bean?

### English Model Answer

A Spring bean is an object that is created, configured, and managed by the Spring IoC container.

---

## Q6. What is the default bean scope in Spring?

### English Model Answer

The default bean scope in Spring is singleton. It means only one instance of the bean is created per Spring container.

---

## Q7. Difference between BeanFactory and ApplicationContext?

### English Model Answer

`BeanFactory` is the basic IoC container and provides lazy initialization. `ApplicationContext` is an advanced container that provides additional features such as event handling, internationalization, annotation support, and AOP integration.

---

## Q8. What is `@Component`?

### English Model Answer

`@Component` marks a class as a Spring-managed bean. During component scanning, Spring detects the class and registers it in the application context.

---

## Q9. Difference between `@Component`, `@Service`, and `@Repository`?

### English Model Answer

`@Component` is a generic stereotype annotation. `@Service` is used for service layer classes, and `@Repository` is used for persistence layer classes. `@Repository` also provides exception translation for database exceptions.

---

## Q10. What is `@Autowired`?

### English Model Answer

`@Autowired` is used to automatically inject dependencies into Spring beans by type. If multiple beans of the same type exist, `@Qualifier` or `@Primary` can be used to resolve ambiguity.

---

## Q11. What is `@Qualifier`?

### English Model Answer

`@Qualifier` is used to specify which bean should be injected when multiple beans of the same type are available.

---

## Q12. What is `@Primary`?

### English Model Answer

`@Primary` marks a bean as the default candidate for autowiring when multiple beans of the same type are present.

---

## Q13. What is AOP?

### English Model Answer

AOP, or Aspect-Oriented Programming, is used to separate cross-cutting concerns such as logging, security, auditing, and transaction management from business logic.

---

## Q14. What is a Pointcut?

### English Model Answer

A pointcut is an expression that defines where an advice should be applied in the application, usually matching method executions.

---

## Q15. What is Advice in AOP?

### English Model Answer

Advice is the action taken by an aspect at a particular join point. Examples include `@Before`, `@After`, `@Around`, `@AfterReturning`, and `@AfterThrowing`.

---

## Q16. What is Spring MVC?

### English Model Answer

Spring MVC is a web framework based on the Model-View-Controller pattern. It uses `DispatcherServlet` as the front controller to route requests to appropriate controllers.

---

## Q17. What is DispatcherServlet?

### English Model Answer

`DispatcherServlet` is the front controller in Spring MVC. It receives incoming HTTP requests and delegates them to appropriate controllers.

---

## Q18. Difference between `@Controller` and `@RestController`?

### English Model Answer

`@Controller` is used for MVC controllers that return views. `@RestController` is used for REST APIs and combines `@Controller` and `@ResponseBody`, returning data directly as JSON or XML.

---

## Q19. What is `@RequestBody`?

### English Model Answer

`@RequestBody` is used to bind the HTTP request body to a Java object. It is commonly used in REST APIs to receive JSON input.

---

## Q20. What is `@PathVariable`?

### English Model Answer

`@PathVariable` is used to extract values from the URI path.

Example:

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}
```

---

## Q21. What is `@RequestParam`?

### English Model Answer

`@RequestParam` is used to extract query parameters from the URL.

Example:

```java
@GetMapping("/users")
public List<User> getUsers(@RequestParam String name) {
    return userService.findByName(name);
}
```

---

## Q22. What is Spring Boot?

### English Model Answer

Spring Boot is a framework built on top of Spring that simplifies application development using auto-configuration, starter dependencies, embedded servers, and production-ready features.

---

## Q23. What is `@SpringBootApplication`?

### English Model Answer

`@SpringBootApplication` is a convenience annotation that combines `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`.

---

## Q24. What is Auto Configuration in Spring Boot?

### English Model Answer

Auto configuration automatically configures Spring beans based on the dependencies present in the classpath. It reduces manual configuration and provides sensible defaults.

---

## Q25. What are Spring Boot Starters?

### English Model Answer

Spring Boot starters are dependency descriptors that group commonly required dependencies for specific functionality, such as web, JPA, security, and testing.

---

## Q26. What is Spring Boot Actuator?

### English Model Answer

Spring Boot Actuator provides production-ready endpoints for monitoring and managing applications, such as health, info, metrics, environment, and mappings.

---

## Q27. What is Spring Data JPA?

### English Model Answer

Spring Data JPA simplifies database access by providing repository interfaces that automatically implement common CRUD operations and query methods.

---

## Q28. Difference between JPA and Hibernate?

### English Model Answer

JPA is a specification for ORM in Java, while Hibernate is an implementation of the JPA specification.

---

## Q29. What is `JpaRepository`?

### English Model Answer

`JpaRepository` is a Spring Data interface that provides CRUD operations, pagination, sorting, batch operations, and JPA-specific methods.

---

## Q30. What is `@Transactional`?

### English Model Answer

`@Transactional` is used to define transaction boundaries. It ensures that all operations inside the method either complete successfully or roll back in case of failure.

---

## Q31. What is transaction propagation?

### English Model Answer

Transaction propagation defines how a transactional method behaves when called inside another transaction. Common propagation types include `REQUIRED`, `REQUIRES_NEW`, `SUPPORTS`, `MANDATORY`, and `NESTED`.

---

## Q32. What is Spring Security?

### English Model Answer

Spring Security is a framework that provides authentication, authorization, and protection against common security vulnerabilities for Spring applications.

---

## Q33. Difference between Authentication and Authorization?

### English Model Answer

Authentication verifies the identity of a user, while authorization determines what resources or actions the authenticated user is allowed to access.

---

## Q34. What is JWT?

### English Model Answer

JWT, or JSON Web Token, is a compact and signed token used for stateless authentication. It contains claims and is sent by the client with each request to authenticate the user.

---

## Q35. What is CSRF?

### English Model Answer

CSRF, or Cross-Site Request Forgery, is an attack where a malicious site tricks a user into performing unwanted actions on a trusted site. Spring Security provides CSRF protection by default for browser-based applications.

---

## Q36. What is CORS?

### English Model Answer

CORS, or Cross-Origin Resource Sharing, is a browser security mechanism that controls whether resources from one origin can be accessed by another origin.

---

## Q37. What is DTO?

### English Model Answer

DTO, or Data Transfer Object, is used to transfer data between layers or between client and server. It helps avoid exposing entity classes directly.

---

## Q38. Why should we not expose entities directly in REST APIs?

### English Model Answer

Entities should not be exposed directly because they may contain sensitive fields, create tight coupling with the database model, cause serialization issues, and make API evolution difficult.

---

## Q39. What is Global Exception Handling?

### English Model Answer

Global exception handling is a centralized way to handle exceptions across the application using `@ControllerAdvice` or `@RestControllerAdvice`.

---

## Q40. What is the difference between `@Valid` and `@Validated`?

### English Model Answer

`@Valid` is part of Jakarta Bean Validation and is used for standard validation. `@Validated` is a Spring annotation that supports validation groups.

---

## Q41. What is lazy loading?

### English Model Answer

Lazy loading means related data is loaded only when it is accessed. It improves performance but can cause `LazyInitializationException` if accessed outside a persistence context.

---

## Q42. What is eager loading?

### English Model Answer

Eager loading means related data is loaded immediately along with the main entity. It can simplify access but may reduce performance if unnecessary data is loaded.

---

## Q43. What is N+1 query problem?

### English Model Answer

The N+1 query problem occurs when one query loads parent records and then additional queries are executed for each child record. It can be solved using fetch joins, entity graphs, or batch fetching.

---

## Q44. What is pagination in Spring Data JPA?

### English Model Answer

Pagination is used to retrieve data in pages instead of loading all records at once. Spring Data JPA provides `Pageable` and `Page` interfaces for pagination.

---

## Q45. What are Spring Profiles?

### English Model Answer

Spring profiles allow environment-specific beans and configuration. They are commonly used for development, testing, staging, and production environments.

---

## Q46. What is externalized configuration?

### English Model Answer

Externalized configuration allows configuration values to be stored outside the application code using properties files, YAML files, environment variables, or command-line arguments.

---

## Q47. What is `application.properties`?

### English Model Answer

`application.properties` is a configuration file used by Spring Boot to define application settings such as server port, database configuration, logging, and custom properties.

---

## Q48. Difference between `application.properties` and `application.yml`?

### English Model Answer

Both are used for configuration. `application.properties` uses key-value format, while `application.yml` uses hierarchical YAML format. YAML is often more readable for nested configuration.

---

## Q49. What is embedded server in Spring Boot?

### English Model Answer

Spring Boot includes embedded servers such as Tomcat, Jetty, or Undertow, allowing applications to run as standalone JAR files without deploying to an external server.

---

## Q50. How do you handle database migrations in Spring Boot?

### English Model Answer

Database migrations can be handled using tools like Flyway or Liquibase. They manage version-controlled database schema changes.

---

# Quick Revision Sheet

## Core

- Spring = Java enterprise framework
- IoC = Control object creation ka Spring ke paas
- DI = Dependency bahar se inject hoti hai
- Bean = Spring-managed object
- ApplicationContext = Advanced IoC container

## Web

- `@Controller` = View return karta hai
- `@RestController` = JSON/XML response
- `DispatcherServlet` = Front controller
- `@RequestBody` = JSON to object
- `@PathVariable` = URL path value
- `@RequestParam` = Query parameter

## Boot

- Spring Boot = Spring setup easy
- Auto configuration = Automatic bean setup
- Starter = Dependency group
- Actuator = Monitoring endpoints
- Embedded Tomcat = Standalone app run

## Data

- JPA = Specification
- Hibernate = JPA implementation
- Repository = DB operations
- `@Transactional` = Transaction management
- DTO = Data transfer object

## Security

- Authentication = User identity verify
- Authorization = Permission check
- JWT = Stateless token-based auth
- CSRF = Browser attack protection
- CORS = Cross-origin access control

---

# Final Interview Tip

Agar interviewer Spring ke baare mein pooche, answer structure aise rakho:

1. Definition do
2. Real-life use explain karo
3. Important annotations batao
4. Example mention karo
5. Best practice add karo

Example:

> Spring is a lightweight Java framework used to build enterprise applications. Its core feature is dependency injection, which promotes loose coupling and testability. Spring also provides modules for web development, data access, transactions, security, and microservices. In modern applications, Spring Boot is commonly used to simplify configuration and deployment.

---
