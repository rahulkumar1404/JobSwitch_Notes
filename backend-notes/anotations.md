# 🚀 Spring Boot Annotations - Complete Guide (100+ Annotations | Zero to Advanced)

## 📚 Table of Contents
1. [Core Stereotype Annotations](#1-core-stereotype-annotations)
2. [Spring Boot Application Annotations](#2-spring-boot-application-annotations)
3. [REST API / Web Annotations](#3-rest-api--web-annotations)
4. [Dependency Injection Annotations](#4-dependency-injection-annotations)
5. [Configuration & Properties Annotations](#5-configuration--properties-annotations)
6. [JPA / Database Annotations](#6-jpa--database-annotations)
7. [Transaction Annotations](#7-transaction-annotations)
8. [Validation Annotations](#8-validation-annotations)
9. [Exception Handling Annotations](#9-exception-handling-annotations)
10. [Security Annotations](#10-security-annotations)
11. [Caching Annotations](#11-caching-annotations)
12. [Scheduling & Async Annotations](#12-scheduling--async-annotations)
13. [AOP Annotations](#13-aop-annotations)
14. [Testing Annotations](#14-testing-annotations)
15. [Conditional Annotations](#15-conditional-annotations)
16. [Event & Listener Annotations](#16-event--listener-annotations)
17. [Actuator & Monitoring Annotations](#17-actuator--monitoring-annotations)
18. [Lombok Annotations (Commonly Used)](#18-lombok-annotations-commonly-used)
19. [Jackson / Serialization Annotations](#19-jackson--serialization-annotations)
20. [Scenario-Based Interview Questions](#20-scenario-based-interview-questions)
21. [Master Differences Table](#21-master-differences-table)
22. [When to Use / When NOT to Use Guide](#22-when-to-use--when-not-to-use-guide)

---

## 1. Core Stereotype Annotations

### 🔹 1. `@Component`

**Hinglish Explanation:**
> Bhai `@Component` ek generic annotation hai jo Spring ko bolta hai — "ye class meri bean hai, isko manage kar." Jab tumhari class kisi specific layer (Service, Repository, Controller) mein nahi aati, tab `@Component` lagao. Spring component scanning ke through isko automatically detect kar leta hai.

**English Interview Answer:**
> `@Component` is a generic stereotype annotation indicating that a class is a Spring-managed bean. During classpath scanning, Spring detects `@Component`-annotated classes and registers them as beans in the ApplicationContext. It is the parent annotation for `@Service`, `@Repository`, and `@Controller`.

```java
@Component
public class EmailHelper {
    public String formatEmail(String rawEmail) {
        return rawEmail.trim().toLowerCase();
    }
}
```

**🎯 When to Use:** Utility classes, helpers, generic beans not belonging to service/repo/controller layer.
**❌ When NOT:** Don't use for clearly identifiable service/dao/controller classes.

---

### 🔹 2. `@Service`

**Hinglish Explanation:**
> `@Service` business logic layer ke liye hota hai. Functionally ye `@Component` jaisa hi hai — koi extra magic nahi. But isko lagane se code readable banta hai, team ke logo ko pata chalta hai ki "is class mein business logic hai." Future mein Spring is annotation ke basis pe extra behavior bhi add kar sakta hai.

**English Interview Answer:**
> `@Service` is a specialization of `@Component` semantically indicating that the class contains business logic. While functionally identical to `@Component`, it improves code readability and allows Spring to potentially apply layer-specific behavior (e.g., AOP advice targeting service beans).

```java
@Service
public class PaymentService {
    
    private final PaymentRepository paymentRepository;
    private final NotificationService notificationService;
    
    public PaymentService(PaymentRepository paymentRepository, 
                          NotificationService notificationService) {
        this.paymentRepository = paymentRepository;
        this.notificationService = notificationService;
    }
    
    public PaymentResponse processPayment(PaymentRequest request) {
        // validation, calculation, saving — all business logic here
        Payment payment = new Payment(request);
        payment.setStatus("COMPLETED");
        paymentRepository.save(payment);
        notificationService.sendReceipt(payment);
        return new PaymentResponse(payment);
    }
}
```

---

### 🔹 3. `@Repository`

**Hinglish Explanation:**
> `@Repository` data access layer (DAO layer) ke liye hai. Iska special power kya hai? Ye database exceptions (SQLException, HibernateException) ko Spring ke `DataAccessException` mein translate kar deta hai. Matlab tumhe persistence technology change karne pe apna exception handling code change nahi karna padega.

**English Interview Answer:**
> `@Repository` is a specialization of `@Component` for the persistence layer. Its unique feature is **automatic exception translation** — it converts technology-specific exceptions (JDBC, Hibernate, JPA) into Spring's consistent `DataAccessException` hierarchy, decoupling your code from the underlying persistence mechanism.

```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    List<Order> findByCustomerIdAndStatus(Long customerId, String status);
    
    @Query("SELECT o FROM Order o WHERE o.createdAt >= :date AND o.total > :minAmount")
    List<Order> findRecentHighValueOrders(@Param("date") LocalDateTime date, 
                                          @Param("minAmount") BigDecimal minAmount);
}
```

---

### 🔹 4. `@Controller`

**Hinglish Explanation:**
> `@Controller` Spring MVC web layer ke liye hai. Ye views return karta hai — matlab HTML pages (Thymeleaf, JSP). Method se String return karo toh ViewResolver us naam ka template dhundh ke render karta hai. REST API bana rahe ho toh ye mat use karo, `@RestController` use karo.

**English Interview Answer:**
> `@Controller` marks a class as a Spring MVC controller responsible for handling HTTP requests and returning view names. The returned string is resolved by a ViewResolver to render an HTML template. It's for server-side rendered web applications.

```java
@Controller
public class DashboardController {
    
    @GetMapping("/dashboard")
    public String showDashboard(Model model) {
        model.addAttribute("stats", statsService.getStats());
        return "dashboard"; // resolves to dashboard.html
    }
}
```

---

### 🔹 5. `@RestController`

**Hinglish Explanation:**
> `@RestController` = `@Controller` + `@ResponseBody`. Matlab har method ka return value directly JSON/XML mein convert hoke response body mein jayega. Koi view resolve nahi hoga. REST APIs banane ke liye ye use karo. Ye aajkal ka standard hai.

**English Interview Answer:**
> `@RestController` is a convenience annotation combining `@Controller` and `@ResponseBody`. Every handler method automatically serializes its return value to the HTTP response body (JSON by default via Jackson). It's the standard for building RESTful APIs.

```java
@RestController
@RequestMapping("/api/v1/products")
public class ProductController {
    
    private final ProductService productService;
    
    public ProductController(ProductService productService) {
        this.productService = productService;
    }
    
    @GetMapping
    public ResponseEntity<List<Product>> getAll() {
        return ResponseEntity.ok(productService.findAll());
    }
    
    @PostMapping
    public ResponseEntity<Product> create(@Valid @RequestBody ProductDTO dto) {
        Product created = productService.create(dto);
        URI location = URI.create("/api/v1/products/" + created.getId());
        return ResponseEntity.created(location).body(created);
    }
}
```

---

## 2. Spring Boot Application Annotations

### 🔹 6. `@SpringBootApplication`

**Hinglish Explanation:**
> Ye teen annotations ka combo hai:
> - `@Configuration` — ye class config source hai
> - `@EnableAutoConfiguration` — classpath dekh ke automatically configure karo
> - `@ComponentScan` — current package + sub-packages scan karo beans ke liye
>
> Main class pe lagao — yahi se application boot hoti hai.

**English Interview Answer:**
> `@SpringBootApplication` is a meta-annotation combining `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan`. It bootstraps the Spring Boot application by enabling auto-configuration based on classpath dependencies and scanning for components in the current and child packages.

```java
@SpringBootApplication
public class ECommerceApplication {
    public static void main(String[] args) {
        SpringApplication.run(ECommerceApplication.class, args);
    }
}
```

---

### 🔹 7. `@EnableAutoConfiguration`

**Hinglish Explanation:**
> Spring Boot ko bolta hai — "bhai classpath mein jo jars padi hain, unke hisaab se sab automatic configure kar de." Jaise agar H2 jar hai toh in-memory DB configure karega, JPA jar hai toh EntityManager set karega. `exclude` attribute se specific auto-config hata sakte ho.

**English Interview Answer:**
> `@EnableAutoConfiguration` instructs Spring Boot to automatically configure beans based on classpath dependencies, property settings, and existing bean definitions. It significantly reduces boilerplate configuration. Use `exclude` to opt out of specific auto-configurations.

```java
@SpringBootApplication(exclude = {
    DataSourceAutoConfiguration.class,
    SecurityAutoConfiguration.class
})
public class MyApp { }
```

---

### 🔹 8. `@ComponentScan`

**Hinglish Explanation:**
> Spring ko batata hai ki kin packages mein jaake beans dhundni hain. By default current package + sub-packages scan hota hai. Agar beans doosre package mein hain toh explicitly basePackages define karo.

**English Interview Answer:**
> `@ComponentScan` configures which packages Spring should scan for annotated components. By default, it scans from the package of the declaring class downward. You can customize it to include/exclude specific packages or filters.

```java
@ComponentScan(
    basePackages = {"com.example.core", "com.example.modules"},
    excludeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = "com.example.legacy.*")
)
```

---

### 🔹 9. `@Configuration`

**Hinglish Explanation:**
> `@Configuration` class ko Spring ka config source banata hai. Ismein `@Bean` methods define karte ho jo manually beans create karti hain. Third-party library classes ko bean banana ho toh ye use karo kyunki unpe `@Component` nahi laga sakte. `@Configuration` classes CGLIB proxy use karti hain — matlab `@Bean` method agar doosre `@Bean` method call kare toh same instance milta hai (singleton behavior).

**English Interview Answer:**
> `@Configuration` designates a class as a source of bean definitions. It enables full Spring container features including inter-bean references through CGLIB proxying (calling one `@Bean` method from another returns the same singleton instance). It's essential for Java-based configuration.

```java
@Configuration
public class AppConfig {
    
    @Bean
    public RestTemplate restTemplate(RestTemplateBuilder builder) {
        return builder
            .setConnectTimeout(Duration.ofSeconds(5))
            .setReadTimeout(Duration.ofSeconds(10))
            .additionalInterceptors(new LoggingInterceptor())
            .build();
    }
    
    @Bean
    public ModelMapper modelMapper() {
        ModelMapper mapper = new ModelMapper();
        mapper.getConfiguration().setMatchingStrategy(MatchingStrategies.STRICT);
        return mapper;
    }
}
```

---

### 🔹 10. `@Bean`

**Hinglish Explanation:**
> `@Bean` method pe lagata hai `@Configuration` class ke andar. Ye method jo object return kare wo Spring bean ban jata hai. Mainly third-party classes bean banana ho, ya complex initialization logic ho tab use karo. Method name bean name banta hai (change kar sakte ho).

**English Interview Answer:**
> `@Bean` declares a method as a factory for a Spring bean. The returned object is registered in the ApplicationContext. It's used for objects requiring complex initialization or for third-party classes you cannot annotate. The method name becomes the bean name by default.

```java
@Configuration
public class SecurityConfig {
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder(12);
    }
    
    @Bean("customObjectMapper")
    public ObjectMapper objectMapper() {
        return new ObjectMapper()
            .registerModule(new JavaTimeModule())
            .disable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES);
    }
}
```

---

## 3. REST API / Web Annotations

### 🔹 11. `@RequestMapping`

**Hinglish Explanation:**
> HTTP request ko handler method/class se map karne ke liye. Class level pe base URL lagao, method level pe specific endpoint. URL, HTTP method, headers, content type — sab specify kar sakte ho. But aajkal specific shortcuts (`@GetMapping` etc.) zyada use hote hain readability ke liye.

```java
@RestController
@RequestMapping(value = "/api/v1/orders", produces = "application/json")
public class OrderController {
    
    @RequestMapping(value = "/{id}", method = RequestMethod.GET)
    public Order getOrder(@PathVariable Long id) { ... }
}
```

---

### 🔹 12. `@GetMapping`

**Hinglish Explanation:**
> `@RequestMapping(method = GET)` ka shortcut. Data fetch karne ke liye — READ operation. Idempotent hai — kitni bhi baar call karo, server pe koi change nahi hoga.

```java
@GetMapping("/{id}")
public ResponseEntity<User> getUser(@PathVariable Long id) {
    return userService.findById(id)
        .map(ResponseEntity::ok)
        .orElse(ResponseEntity.notFound().build());
}
```

---

### 🔹 13. `@PostMapping`

**Hinglish Explanation:**
> New resource CREATE karne ke liye. Request body mein data bhejo, server pe naya record banega. Non-idempotent — har call pe naya resource banega.

```java
@PostMapping
@ResponseStatus(HttpStatus.CREATED)
public User createUser(@Valid @RequestBody CreateUserRequest request) {
    return userService.create(request);
}
```

---

### 🔹 14. `@PutMapping`

**Hinglish Explanation:**
> Existing resource ko FULLY UPDATE karne ke liye. Pura object replace hota hai. Idempotent — same data ke sath baar baar call karo, result same rahega.

```java
@PutMapping("/{id}")
public User updateUser(@PathVariable Long id, @Valid @RequestBody UpdateUserRequest request) {
    return userService.update(id, request);
}
```

---

### 🔹 15. `@PatchMapping`

**Hinglish Explanation:**
> PARTIAL UPDATE ke liye — sirf wo fields jo change karna hai wahi bhejo. PUT mein pura object bhejte ho, PATCH mein partial data.

```java
@PatchMapping("/{id}")
public User patchUser(@PathVariable Long id, @RequestBody Map<String, Object> updates) {
    return userService.partialUpdate(id, updates);
}
```

---

### 🔹 16. `@DeleteMapping`

**Hinglish Explanation:**
> Resource DELETE karne ke liye. Usually 204 No Content return karte hain successful deletion pe.

```java
@DeleteMapping("/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)
public void deleteUser(@PathVariable Long id) {
    userService.delete(id);
}
```

---

### 🔹 17. `@PathVariable`

**Hinglish Explanation:**
> URL path ke dynamic part ko method parameter mein capture karne ke liye. `/users/{id}` mein `{id}` ko extract karta hai. Multiple path variables bhi use kar sakte ho ek URL mein.

**English Interview Answer:**
> `@PathVariable` extracts values from URI template variables and binds them to method parameters. It's essential for RESTful resource identification.

```java
@GetMapping("/departments/{deptId}/employees/{empId}")
public Employee getEmployee(
    @PathVariable Long deptId,
    @PathVariable("empId") Long employeeId) {
    return employeeService.findByDeptAndId(deptId, employeeId);
}
```

---

### 🔹 18. `@RequestParam`

**Hinglish Explanation:**
> URL mein `?key=value` — query parameters ko method parameter mein bind karne ke liye. Searching, filtering, pagination ke liye commonly use hota hai. Optional bana sakte ho, default value de sakte ho.

**English Interview Answer:**
> `@RequestParam` binds HTTP query string parameters to method arguments. It supports required/optional parameters, default values, and multi-value parameters (arrays/lists).

```java
@GetMapping("/search")
public Page<Product> search(
    @RequestParam(required = false) String q,
    @RequestParam(defaultValue = "0") int page,
    @RequestParam(defaultValue = "20") int size,
    @RequestParam(defaultValue = "createdAt") String sortBy,
    @RequestParam(defaultValue = "desc") String sortDir,
    @RequestParam(required = false) List<String> categories) {
    
    Sort sort = Sort.by(Sort.Direction.fromString(sortDir), sortBy);
    return productService.search(q, categories, PageRequest.of(page, size, sort));
}
```

---

### 🔹 19. `@RequestBody`

**Hinglish Explanation:**
> HTTP request ki body (JSON/XML) ko Java object mein convert karta hai. Jackson internally use hota hai deserialization ke liye. POST/PUT/PATCH mein data receive karne ke liye standard approach.

```java
@PostMapping("/orders")
public ResponseEntity<Order> placeOrder(@Valid @RequestBody OrderRequest request) {
    Order order = orderService.place(request);
    return ResponseEntity.status(HttpStatus.CREATED).body(order);
}
```

---

### 🔹 20. `@ResponseBody`

**Hinglish Explanation:**
> Method ka return value directly response body mein likhta hai (JSON mein). `@RestController` mein ye implicit hai har method pe. `@Controller` mein selectively lagana padta hai jab koi method JSON return kare view ke bajaye.

---

### 🔹 21. `@ResponseStatus`

**Hinglish Explanation:**
> Default HTTP status code change karne ke liye. Normal GET 200 return karta hai, but POST ke liye 201 CREATED set karna ho, ya exception pe 404/403 set karna ho toh ye use karo.

```java
@DeleteMapping("/{id}")
@ResponseStatus(HttpStatus.NO_CONTENT)  // 204
public void delete(@PathVariable Long id) { ... }

@ResponseStatus(HttpStatus.NOT_FOUND)
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(Long id) {
        super("User not found with id: " + id);
    }
}
```

---

### 🔹 22. `@RequestHeader`

**Hinglish Explanation:**
> HTTP request headers access karne ke liye — jaise Authorization token, custom headers (X-Request-Id, X-Correlation-Id), Accept-Language etc.

```java
@GetMapping("/me")
public UserProfile getCurrentUser(
    @RequestHeader("Authorization") String token,
    @RequestHeader(value = "Accept-Language", defaultValue = "en") String lang) {
    return userService.getProfile(token, lang);
}
```

---

### 🔹 23. `@CookieValue`

**Hinglish Explanation:**
> HTTP request se specific cookie read karne ke liye.

```java
@GetMapping("/theme")
public String getTheme(@CookieValue(value = "theme", defaultValue = "light") String theme) {
    return theme;
}
```

---

### 🔹 24. `@ModelAttribute`

**Hinglish Explanation:**
> Form data (non-JSON) ko Java object mein bind karne ke liye. Thymeleaf/JSP form submissions mein use hota hai. Bhi method level pe use karke model mein common attributes add kar sakte ho jo har request pe chahiye.

```java
@Controller
public class RegistrationController {
    
    @ModelAttribute("countries")
    public List<String> populateCountries() {
        return List.of("India", "USA", "UK"); // available in all views
    }
    
    @PostMapping("/register")
    public String register(@ModelAttribute UserForm form) {
        userService.register(form);
        return "redirect:/login";
    }
}
```

---

### 🔹 25. `@CrossOrigin`

**Hinglish Explanation:**
> CORS (Cross-Origin Resource Sharing) enable karne ke liye. Jab frontend (React/Angular) alag port/domain pe ho aur backend Spring Boot pe, toh browser cross-origin requests block karta hai. Ye annotation us restriction ko relax karta hai.

```java
@RestController
@CrossOrigin(origins = {"http://localhost:3000", "https://myapp.com"}, maxAge = 3600)
@RequestMapping("/api/users")
public class UserController { ... }
```

---

### 🔹 26. `@SessionAttributes`

**Hinglish Explanation:**
> Multi-step form wizard mein data persist karne ke liye. Controller ke model attributes ko HTTP session mein store karta hai across multiple requests.

```java
@Controller
@SessionAttributes("orderForm")
public class CheckoutController {
    
    @ModelAttribute("orderForm")
    public OrderForm orderForm() {
        return new OrderForm();
    }
    
    @PostMapping("/checkout/step1")
    public String step1(@ModelAttribute OrderForm form) { return "step2"; }
    
    @PostMapping("/checkout/step2")
    public String step2(@ModelAttribute OrderForm form, SessionStatus status) {
        orderService.place(form);
        status.setComplete(); // clears session attributes
        return "redirect:/confirmation";
    }
}
```

---

### 🔹 27. `@MatrixVariable`

**Hinglish Explanation:**
> URL path segment mein semicolon-separated parameters handle karne ke liye. Jaise `/cars/color=red;year=2024`. Rarely used but powerful for complex filtering.

```java
// GET /employees/dept=IT;level=senior
@GetMapping("/employees/{criteria}")
public List<Employee> filter(
    @MatrixVariable(pathVar = "criteria") Map<String, String> criteria) {
    return employeeService.filter(criteria);
}
```

---

## 4. Dependency Injection Annotations

### 🔹 28. `@Autowired`

**Hinglish Explanation:**
> Spring ko bolta hai — "ye dependency dhundh ke inject kar de." Type ke basis pe matching bean dhundhta hai ApplicationContext mein. Constructor, field, ya setter pe use kar sakte ho. **Constructor injection BEST PRACTICE hai** — immutability, testability, aur required dependencies clearly dikhti hain. Spring 4.3+ mein single constructor pe `@Autowired` optional hai.

**English Interview Answer:**
> `@Autowired` enables Spring's dependency injection by type. It can be applied to constructors (recommended), fields, or setters. Constructor injection is preferred because it enforces immutability, makes dependencies explicit, and simplifies unit testing. Since Spring 4.3, `@Autowired` is implicit on single-constructor classes.

```java
// ✅ BEST PRACTICE: Constructor Injection
@Service
public class OrderService {
    
    private final OrderRepository orderRepository;
    private final InventoryService inventoryService;
    private final EventPublisher eventPublisher;
    
    // @Autowired optional with single constructor
    public OrderService(OrderRepository orderRepository,
                        InventoryService inventoryService,
                        EventPublisher eventPublisher) {
        this.orderRepository = orderRepository;
        this.inventoryService = inventoryService;
        this.eventPublisher = eventPublisher;
    }
}

// ⚠️ AVOID: Field Injection (hard to test, hides dependencies)
@Service
public class OrderService {
    @Autowired private OrderRepository orderRepository;
    @Autowired private InventoryService inventoryService;
}
```

---

### 🔹 29. `@Qualifier`

**Hinglish Explanation:**
> Jab ek interface ke multiple implementations hain toh Spring confuse ho jata hai — "kaunsa bean inject karun?" `@Qualifier` mein bean ka naam batao toh specific bean inject hoga. Ye `@Autowired` ke saath use hota hai.

```java
public interface CacheService {
    void put(String key, Object value);
    Object get(String key);
}

@Service("redisCache")
public class RedisCacheService implements CacheService { ... }

@Service("localCache")
public class LocalCacheService implements CacheService { ... }

@Service
public class ProductService {
    private final CacheService cacheService;
    
    public ProductService(@Qualifier("redisCache") CacheService cacheService) {
        this.cacheService = cacheService;
    }
}
```

---

### 🔹 30. `@Primary`

**Hinglish Explanation:**
> Multiple beans mein se ek ko DEFAULT mark karne ke liye. Jab koi `@Qualifier` nahi diya toh `@Primary` wala bean inject hoga. `@Qualifier` ki priority `@Primary` se zyada hoti hai — matlab agar dono hain toh `@Qualifier` win karega.

```java
@Primary
@Service("redisCache")
public class RedisCacheService implements CacheService { ... }
// This will be injected by default when no @Qualifier specified
```

---

### 🔹 31. `@Lazy`

**Hinglish Explanation:**
> By default Spring startup pe saare singleton beans create kar deta hai. `@Lazy` lagao toh bean tab banega jab pehli baar use hoga. Heavy initialization wali beans ke liye useful — startup time kam hota hai. Circular dependency tod ne ke liye bhi use hota hai.

```java
@Service
@Lazy
public class ReportEngine {
    // Heavy: loads templates, connects to report server
    // Only created when first injected/used
}

// Break circular dependency
@Service
public class ServiceA {
    public ServiceA(@Lazy ServiceB serviceB) {
        this.serviceB = serviceB;
    }
}
```

---

### 🔹 32. `@Scope`

**Hinglish Explanation:**
> Bean ka lifecycle define karo:
> - `singleton` (default) — ek hi instance pure app mein
> - `prototype` — har baar naya instance milega
> - `request` — har HTTP request pe naya
> - `session` — har HTTP session pe naya
> - `application` — ServletContext ke per naya
> - `websocket` — har WebSocket session pe naya

```java
@Component
@Scope(value = "prototype")
public class ShoppingCart {
    private List<Item> items = new ArrayList<>();
}

@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class AuditContext {
    private String userId;
    private String correlationId;
}
```

---

### 🔹 33. `@Lookup`

**Hinglish Explanation:**
> Singleton bean ke andar prototype bean inject karna ho toh problem hoti hai — prototype bean bhi ek hi baar create hoga. `@Lookup` solve karta hai — har baar fresh prototype instance milta hai. Spring method override kar deta hai internally.

```java
@Service
public abstract class NotificationService {
    
    @Lookup
    protected abstract NotificationTask createTask(); // prototype bean
    
    public void notify(String message) {
        NotificationTask task = createTask(); // fresh instance every time
        task.execute(message);
    }
}

@Component
@Scope("prototype")
public class NotificationTask {
    public void execute(String message) { ... }
}
```

---

### 🔹 34. `@DependsOn`

**Hinglish Explanation:**
> Bean creation order control karne ke liye. "Meri bean banne se pehle ye wali bean ban jani chahiye." Rare cases mein use hota hai jab implicit dependency nahi dikhti.

```java
@Bean
@DependsOn({"dataSource", "cacheManager"})
public ApplicationStartup startup() {
    return new ApplicationStartup();
}
```

---

## 5. Configuration & Properties Annotations

### 🔹 35. `@Value`

**Hinglish Explanation:**
> Single property value inject karne ke liye. `application.properties` se `${property.name}` se value aati hai. Default value bhi de sakte ho `${property:default}`. SpEL (Spring Expression Language) bhi support karta hai `#{expression}`. 1-2 values ke liye theek hai, zyada ho toh `@ConfigurationProperties` better hai.

```java
@Service
public class AppService {
    
    @Value("${app.name:MyApp}")
    private String appName;
    
    @Value("${app.feature.enabled:false}")
    private boolean featureEnabled;
    
    @Value("${app.allowed-origins}")
    private List<String> allowedOrigins;
    
    @Value("#{${app.feature.enabled} ? 'ACTIVE' : 'INACTIVE'}")
    private String featureStatus; // SpEL
    
    @Value("#{systemProperties['java.version']}")
    private String javaVersion;
}
```

---

### 🔹 36. `@ConfigurationProperties`

**Hinglish Explanation:**
> Properties file ki ek group of related properties ko POJO class mein map karne ke liye. Type-safe hai, validation support hai, IDE autocomplete milta hai. `@Value` se better approach hai jab multiple related properties ho. Prefix do — matching properties automatically bind ho jayengi.

**English Interview Answer:**
> `@ConfigurationProperties` provides type-safe, structured binding of externalized configuration to a Java POJO. It supports nested objects, collections, validation (`@Validated`), and relaxed binding (camelCase ↔ kebab-case ↔ snake_case). It's superior to `@Value` for groups of related properties.

```java
@Component
@ConfigurationProperties(prefix = "app.datasource")
@Validated
public class DataSourceProperties {
    
    @NotBlank
    private String url;
    
    @NotBlank
    private String username;
    
    private String password;
    
    @Min(1) @Max(100)
    private int maxPoolSize = 10;
    
    private Duration connectionTimeout = Duration.ofSeconds(30);
    
    private Map<String, String> properties = new HashMap<>();
    
    // getters and setters
}
```
```yaml
app:
  datasource:
    url: jdbc:postgresql://localhost:5432/mydb
    username: admin
    password: secret
    max-pool-size: 20
    connection-timeout: 15s
    properties:
      cachePrepStmts: "true"
      prepStmtCacheSize: "250"
```

---

### 🔹 37. `@EnableConfigurationProperties`

**Hinglish Explanation:**
> `@ConfigurationProperties` wali classes ko register karne ke liye. Agar class pe `@Component` nahi lagaya toh ye annotation config class mein laga ke explicitly register karo.

```java
@Configuration
@EnableConfigurationProperties({MailProperties.class, StorageProperties.class})
public class AppConfig { }
```

---

### 🔹 38. `@PropertySource`

**Hinglish Explanation:**
> Custom properties file (default application.properties ke alawa) load karne ke liye. Jaise koi `custom-config.properties` file ho jo separate rakhi ho.

```java
@Configuration
@PropertySource("classpath:payment-gateway.properties")
@PropertySource("classpath:email-config.properties")
public class ExternalConfig { }
```

---

### 🔹 39. `@Profile`

**Hinglish Explanation:**
> Environment-specific beans define karne ke liye. Dev mein H2, prod mein PostgreSQL; dev mein mock email, prod mein real SMTP. Active profile set karo — sirf usi profile ki beans load hongi. Multiple profiles bhi combine kar sakte ho.

```java
@Configuration
@Profile("dev")
public class DevConfig {
    @Bean
    public EmailService emailService() {
        return new MockEmailService(); // prints to console
    }
}

@Configuration
@Profile("prod")
public class ProdConfig {
    @Bean
    public EmailService emailService() {
        return new SmtpEmailService(); // real email
    }
}

// Negative profile
@Bean
@Profile("!prod") // active in everything EXCEPT prod
public DataSource embeddedDataSource() { ... }
```

---

### 🔹 40. `@Import`

**Hinglish Explanation:**
> Additional `@Configuration` classes import karne ke liye jo component scan se nahi milti (different module/package). Explicitly add karta hai.

```java
@Configuration
@Import({SecurityConfig.class, CacheConfig.class, SchedulerConfig.class})
public class AppConfig { }
```

---

### 🔹 41. `@ImportResource`

**Hinglish Explanation:**
> Legacy XML configuration files load karne ke liye. Purane projects mein jo XML-based config hai use integrate karne ke liye.

```java
@Configuration
@ImportResource("classpath:legacy-beans.xml")
public class LegacyConfig { }
```

---

## 6. JPA / Database Annotations

### 🔹 42. `@Entity`

**Hinglish Explanation:**
> JPA ko batata hai ki ye class ek database table represent karti hai. Har object ek row hai. Class ka naam default table name hota hai — change karna ho toh `@Table` use karo. Ye class JPA entity lifecycle follow karegi.

```java
@Entity
@Table(name = "users", 
       uniqueConstraints = @UniqueConstraint(columnNames = {"email"}),
       indexes = @Index(name = "idx_user_email", columnList = "email"))
public class User {
    
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @Column(nullable = false, length = 100)
    private String name;
    
    @Column(nullable = false, unique = true)
    private String email;
    
    @Enumerated(EnumType.STRING)
    @Column(nullable = false)
    private UserStatus status;
    
    @CreationTimestamp
    @Column(updatable = false)
    private LocalDateTime createdAt;
    
    @UpdateTimestamp
    private LocalDateTime updatedAt;
    
    @Version
    private Long version; // Optimistic locking
}
```

---

### 🔹 43. `@Id`

**Hinglish Explanation:**
> Primary key mark karne ke liye. Har `@Entity` mein ek `@Id` hona zaruri hai.

---

### 🔹 44. `@GeneratedValue`

**Hinglish Explanation:**
> Primary key auto-generate karne ki strategy:
> - `IDENTITY` — DB auto-increment (MySQL best)
> - `SEQUENCE` — DB sequence (PostgreSQL/Oracle best, batch inserts support)
> - `TABLE` — Separate table for IDs (portable but slow)
> - `AUTO` — JPA provider decide kare
> - `UUID` (Hibernate 6+) — UUID generate kare

```java
@Id
@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "user_seq")
@SequenceGenerator(name = "user_seq", sequenceName = "user_sequence", allocationSize = 50)
private Long id;

// UUID approach
@Id
@GeneratedValue(strategy = GenerationType.UUID)
private UUID id;
```

---

### 🔹 45. `@Column`

**Hinglish Explanation:**
> Column level customization — name, nullable, unique, length, precision define karo. Field ko specific database column se map karo.

```java
@Column(name = "phone_number", nullable = true, length = 15, unique = true)
private String phoneNumber;

@Column(precision = 10, scale = 2) // for BigDecimal
private BigDecimal salary;

@Column(columnDefinition = "TEXT")
private String description;
```

---

### 🔹 46. `@Table`

**Hinglish Explanation:**
> Entity ko specific table name, schema, aur constraints se map karo.

```java
@Entity
@Table(name = "order_items", schema = "ecommerce",
    uniqueConstraints = @UniqueConstraint(columnNames = {"order_id", "product_id"}))
public class OrderItem { ... }
```

---

### 🔹 47. `@OneToMany` & `@ManyToOne`

**Hinglish Explanation:**
> Parent-Child relationship ke liye:
> - `@OneToMany` — ek parent ke multiple children (Department → Employees)
> - `@ManyToOne` — child ki taraf se parent ka reference
> - `mappedBy` — relationship ka owner kaun hai (jo side pe FK hoti hai wo owner)
> - `FetchType.LAZY` — data tab fetch hoga jab access karoge (recommended)
> - `cascade` — parent pe operation hone pe children pe bhi apply ho

```java
@Entity
public class Department {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL, orphanRemoval = true)
    private List<Employee> employees = new ArrayList<>();
    
    // Helper methods
    public void addEmployee(Employee emp) {
        employees.add(emp);
        emp.setDepartment(this);
    }
    public void removeEmployee(Employee emp) {
        employees.remove(emp);
        emp.setDepartment(null);
    }
}

@Entity
public class Employee {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "department_id", nullable = false)
    private Department department;
}
```

---

### 🔹 48. `@OneToOne`

**Hinglish Explanation:**
> One-to-one mapping. Jaise User ↔ UserProfile, Employee ↔ ParkingSpot.

```java
@Entity
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToOne(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private UserProfile profile;
}

@Entity
public class UserProfile {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @OneToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "user_id", unique = true)
    @MapsId // shares same PK as User
    private User user;
    
    private String bio;
    private String avatarUrl;
}
```

---

### 🔹 49. `@ManyToMany`

**Hinglish Explanation:**
> Many-to-many relationship — jaise Student ↔ Course, User ↔ Role. Join table automatically banti hai. Careful: `CascadeType.ALL` mat lagao — galti se connected entities delete ho sakte hain.

```java
@Entity
public class Student {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @ManyToMany
    @JoinTable(
        name = "student_courses",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();
}

@Entity
public class Course {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;
    
    @ManyToMany(mappedBy = "courses")
    private Set<Student> students = new HashSet<>();
}
```

---

### 🔹 50. `@JoinColumn`

**Hinglish Explanation:**
> Foreign key column define karta hai. Relationship ka owner side pe lagta hai. Column name, nullable, unique define kar sakte ho.

---

### 🔹 51. `@Embedded` & `@Embeddable`

**Hinglish Explanation:**
> Reusable value objects ke liye jo separate table nahi chahte but logically group karte ho. Jaise Address (street, city, zip) ko User entity mein embed karo bina alag table banaye.

```java
@Embeddable
public class Address {
    private String street;
    private String city;
    private String state;
    
    @Column(name = "zip_code")
    private String zipCode;
}

@Entity
public class Customer {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    
    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "street", column = @Column(name = "billing_street")),
        @AttributeOverride(name = "city", column = @Column(name = "billing_city"))
    })
    private Address billingAddress;
    
    @Embedded
    @AttributeOverrides({
        @AttributeOverride(name = "street", column = @Column(name = "shipping_street")),
        @AttributeOverride(name = "city", column = @Column(name = "shipping_city"))
    })
    private Address shippingAddress;
}
```

---

### 🔹 52. `@Query`

**Hinglish Explanation:**
> Spring Data JPA mein custom JPQL ya native SQL queries likhne ke liye. Method name convention se complex queries possible nahi hoti tab ye use karo.

```java
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {
    
    // JPQL
    @Query("SELECT o FROM Order o JOIN FETCH o.items WHERE o.customer.id = :customerId")
    List<Order> findOrdersWithItems(@Param("customerId") Long customerId);
    
    // Native SQL
    @Query(value = "SELECT * FROM orders WHERE total > :min ORDER BY created_at DESC LIMIT :limit",
           nativeQuery = true)
    List<Order> findTopOrders(@Param("min") BigDecimal min, @Param("limit") int limit);
    
    // Update query
    @Modifying(clearAutomatically = true)
    @Query("UPDATE Order o SET o.status = :status WHERE o.id IN :ids")
    int bulkUpdateStatus(@Param("status") String status, @Param("ids") List<Long> ids);
}
```

---

### 🔹 53. `@Modifying`

**Hinglish Explanation:**
> `@Query` ke saath UPDATE/DELETE queries execute karne ke liye `@Modifying` lagana zaruri hai. `clearAutomatically = true` set karo taaki persistence context stale data na rakhe.

---

### 🔹 54. `@EntityGraph`

**Hinglish Explanation:**
> N+1 query problem solve karne ke liye. Ye batata hai ki kaunsi related entities ek hi query mein fetch karni hain (JOIN). LAZY associations ko ek specific query mein EAGER load karne ke liye use hota hai.

```java
@Repository
public interface DepartmentRepository extends JpaRepository<Department, Long> {
    
    @EntityGraph(attributePaths = {"employees", "employees.projects"})
    Optional<Department> findById(Long id);
    
    @EntityGraph(attributePaths = {"employees"})
    List<Department> findByNameContaining(String name);
}
```

---

### 🔹 55. `@NamedQuery` & `@NamedNativeQuery`

**Hinglish Explanation:**
> Entity class pe pre-defined queries declare karo. Compile time pe validate hoti hain. Reusable hoti hain multiple repositories mein.

```java
@Entity
@NamedQuery(name = "User.findActive", query = "SELECT u FROM User u WHERE u.status = 'ACTIVE'")
@NamedQuery(name = "User.findByDepartment", query = "SELECT u FROM User u WHERE u.department.name = :dept")
public class User { ... }
```

---

### 🔹 56. `@Version`

**Hinglish Explanation:**
> Optimistic locking ke liye. Multiple users ek hi record edit karein toh conflict detect karne mein help karta hai. JPA har update pe version increment karta hai — agar version mismatch ho toh `OptimisticLockException` throw hota hai.

```java
@Entity
public class Product {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private BigDecimal price;
    
    @Version
    private Long version; // auto-incremented on each update
}
```

---

### 🔹 57. `@CreatedDate`, `@LastModifiedDate`, `@CreatedBy`, `@LastModifiedBy`

**Hinglish Explanation:**
> Spring Data JPA auditing annotations. Automatically timestamps aur user info fill karte hain. `@EnableJpaAuditing` enable karna padta hai.

```java
@Entity
@EntityListeners(AuditingEntityListener.class)
public class Article {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    @CreatedDate
    @Column(updatable = false)
    private LocalDateTime createdAt;
    
    @LastModifiedDate
    private LocalDateTime updatedAt;
    
    @CreatedBy
    @Column(updatable = false)
    private String createdBy;
    
    @LastModifiedBy
    private String updatedBy;
}

@Configuration
@EnableJpaAuditing(auditorAwareRef = "auditorProvider")
public class JpaConfig {
    @Bean
    public AuditorAware<String> auditorProvider() {
        return () -> Optional.ofNullable(SecurityContextHolder.getContext()
            .getAuthentication().getName());
    }
}
```

---

### 🔹 58. `@Enumerated`

**Hinglish Explanation:**
> Enum ko database mein kaise store karna hai:
> - `EnumType.STRING` — enum ka naam store hoga ("ACTIVE") — **recommended** (safe if enum order changes)
> - `EnumType.ORDINAL` — enum ka position number (0, 1, 2) — **avoid** (breaks if enum reordered)

```java
@Enumerated(EnumType.STRING)
@Column(nullable = false)
private OrderStatus status; // stored as "PENDING", "SHIPPED", "DELIVERED"
```

---

### 🔹 59. `@Temporal`

**Hinglish Explanation:**
> `java.util.Date` / `Calendar` fields ke liye date type specify karo (DATE, TIME, TIMESTAMP). Java 8+ `LocalDate`/`LocalDateTime` use kar rahe ho toh ye zaruri nahi.

---

### 🔹 60. `@Lob`

**Hinglish Explanation:**
> Large objects store karne ke liye — BLOB (binary data like files/images) ya CLOB (large text). Database mein special column type use hota hai.

```java
@Lob
@Column(columnDefinition = "TEXT")
private String content; // CLOB

@Lob
private byte[] profileImage; // BLOB
```

---

## 7. Transaction Annotations

### 🔹 61. `@Transactional`

**Hinglish Explanation:**
> Database transaction management ke liye. Method pe lagao — method start pe transaction begin, successful completion pe commit, RuntimeException pe rollback. Service layer pe lagao (controller pe nahi). Class level pe lagao toh saari methods pe apply hoga.
>
> **Important pitfalls:**
> 1. Self-invocation — same class mein ek method dusre `@Transactional` method ko call kare toh proxy bypass ho jata hai
> 2. Private methods pe kaam nahi karta
> 3. Checked exceptions pe default rollback nahi hota — `rollbackFor` specify karo
> 4. Read-only methods pe `readOnly = true` lagao — performance optimize hota hai

**English Interview Answer:**
> `@Transactional` provides declarative transaction demarcation. It wraps the annotated method in a transaction — committing on success, rolling back on unchecked exceptions. Key attributes: `propagation` (REQUIRED, REQUIRES_NEW, etc.), `isolation`, `timeout`, `readOnly`, and `rollbackFor`. It works via AOP proxying, so it doesn't apply to private methods or self-invocations.

```java
@Service
@Transactional(readOnly = true) // default for all methods
public class AccountService {
    
    private final AccountRepository accountRepository;
    
    @Transactional // overrides class-level readOnly
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        Account from = accountRepository.findById(fromId)
            .orElseThrow(() -> new AccountNotFoundException(fromId));
        Account to = accountRepository.findById(toId)
            .orElseThrow(() -> new AccountNotFoundException(toId));
        
        if (from.getBalance().compareTo(amount) < 0) {
            throw new InsufficientFundsException();
        }
        
        from.debit(amount);
        to.credit(amount);
        
        accountRepository.save(from);
        accountRepository.save(to);
    }
    
    @Transactional(propagation = Propagation.REQUIRES_NEW, timeout = 5)
    public void logTransaction(TransactionLog log) {
        // Runs in separate transaction — commits even if parent rolls back
        transactionLogRepository.save(log);
    }
    
    @Transactional(readOnly = true)
    public AccountStatement getStatement(Long accountId) {
        // Optimized: no dirty checking, may use read replica
        return accountRepository.getStatement(accountId);
    }
}
```

**Transaction Propagation Types:**
| Propagation | Behavior |
|-------------|----------|
| `REQUIRED` (default) | Join existing or create new |
| `REQUIRES_NEW` | Always create new (suspend existing) |
| `NESTED` | Nested transaction with savepoint |
| `SUPPORTS` | Join if exists, else non-transactional |
| `NOT_SUPPORTED` | Run non-transactionally (suspend existing) |
| `MANDATORY` | Must run in existing (throws if none) |
| `NEVER` | Must NOT run in transaction (throws if exists) |

---

## 8. Validation Annotations

### 🔹 62. `@Valid`

**Hinglish Explanation:**
> Request body ya nested object pe validation trigger karne ke liye. `@RequestBody` ke saath lagao — bean validation annotations check honge. Nested objects pe bhi `@Valid` lagao taaki cascading validation ho.

---

### 🔹 63. `@Validated`

**Hinglish Explanation:**
> Spring ka annotation — `@Valid` se powerful. Validation groups support karta hai. Matlab create vs update scenarios mein different rules apply karo. Class-level pe method parameter validation bhi enable karta hai.

```java
// Groups
public interface OnCreate {}
public interface OnUpdate {}

public class ProductDTO {
    
    @Null(groups = OnCreate.class, message = "ID must be null for creation")
    @NotNull(groups = OnUpdate.class, message = "ID required for update")
    private Long id;
    
    @NotBlank(groups = {OnCreate.class, OnUpdate.class})
    @Size(min = 3, max = 100)
    private String name;
    
    @NotNull(groups = OnCreate.class)
    @Positive
    private BigDecimal price;
}

@RestController
@Validated
public class ProductController {
    
    @PostMapping
    public Product create(@Validated(OnCreate.class) @RequestBody ProductDTO dto) { ... }
    
    @PutMapping("/{id}")
    public Product update(@Validated(OnUpdate.class) @RequestBody ProductDTO dto) { ... }
}
```

---

### 🔹 64-75. Bean Validation Annotations

**Hinglish Explanation:**
> Jakarta Bean Validation se common validations easily apply karo. `spring-boot-starter-validation` dependency add karo.

```java
public class RegistrationRequest {
    
    // 64. @NotNull — null nahi hona chahiye
    @NotNull(message = "Name cannot be null")
    private String name;
    
    // 65. @NotBlank — null nahi, empty nahi, whitespace-only nahi (Strings only)
    @NotBlank(message = "Email is required")
    private String email;
    
    // 66. @NotEmpty — null nahi, empty nahi (Strings, Collections, Arrays)
    @NotEmpty(message = "Roles cannot be empty")
    private List<String> roles;
    
    // 67. @Size — length/size constraint
    @Size(min = 8, max = 50, message = "Password must be 8-50 chars")
    private String password;
    
    // 68. @Min & @Max — numeric range
    @Min(value = 18, message = "Must be at least 18")
    @Max(value = 150, message = "Invalid age")
    private Integer age;
    
    // 69. @Email — valid email format
    @Email(message = "Invalid email format")
    private String contactEmail;
    
    // 70. @Pattern — regex pattern matching
    @Pattern(regexp = "^\\+?[1-9]\\d{9,14}$", message = "Invalid phone")
    private String phone;
    
    // 71. @Positive / @PositiveOrZero
    @Positive(message = "Amount must be positive")
    private BigDecimal amount;
    
    // 72. @Negative / @NegativeOrZero
    @Negative(message = "Discount must be negative")
    private BigDecimal discount;
    
    // 73. @Past / @PastOrPresent — date must be in past
    @Past(message = "Birth date must be in the past")
    private LocalDate birthDate;
    
    // 74. @Future / @FutureOrPresent — date must be in future
    @Future(message = "Expiry must be in future")
    private LocalDate expiryDate;
    
    // 75. @Digits — integer and fraction digits control
    @Digits(integer = 6, fraction = 2, message = "Invalid salary format")
    private BigDecimal salary;
}
```

---

### 🔹 76. Custom Validation Annotation

**Hinglish Explanation:**
> Jab built-in validations kaafi nahi hoti — apna custom validator banao. Annotation define karo + Validator class implement karo.

```java
// Custom annotation
@Target({ElementType.FIELD})
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = UniqueEmailValidator.class)
public @interface UniqueEmail {
    String message() default "Email already registered";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}

// Validator
@Component
public class UniqueEmailValidator implements ConstraintValidator<UniqueEmail, String> {
    
    @Autowired
    private UserRepository userRepository;
    
    @Override
    public boolean isValid(String email, ConstraintValidatorContext context) {
        if (email == null) return true;
        return !userRepository.existsByEmail(email);
    }
}

// Usage
public class CreateUserDTO {
    @UniqueEmail
    @Email
    private String email;
}
```

---

## 9. Exception Handling Annotations

### 🔹 77. `@ExceptionHandler`

**Hinglish Explanation:**
> Specific exception ko catch karke proper response dene ke liye. Controller mein locally ya `@ControllerAdvice` mein globally define karo. Method parameters mein exception object, request object le sakte ho.

---

### 🔹 78. `@ControllerAdvice`

**Hinglish Explanation:**
> Global exception handler — saare controllers ke exceptions ek jagah handle karo. Consistent error response format milta hai puri application mein. `basePackages` ya `assignableTypes` se scope limit kar sakte ho.

---

### 🔹 79. `@RestControllerAdvice`

**Hinglish Explanation:**
> `@ControllerAdvice` + `@ResponseBody` = `@RestControllerAdvice`. REST APIs ke liye use karo — JSON error responses return karega.

```java
@RestControllerAdvice
@Slf4j
public class GlobalExceptionHandler {
    
    @ExceptionHandler(ResourceNotFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public ApiError handleNotFound(ResourceNotFoundException ex, WebRequest request) {
        log.warn("Resource not found: {}", ex.getMessage());
        return ApiError.builder()
            .status(404)
            .error("Not Found")
            .message(ex.getMessage())
            .path(request.getDescription(false))
            .timestamp(LocalDateTime.now())
            .build();
    }
    
    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public ApiError handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> fieldErrors = new HashMap<>();
        ex.getBindingResult().getFieldErrors().forEach(error ->
            fieldErrors.put(error.getField(), error.getDefaultMessage()));
        
        return ApiError.builder()
            .status(400)
            .error("Validation Failed")
            .message("Request body validation failed")
            .fieldErrors(fieldErrors)
            .timestamp(LocalDateTime.now())
            .build();
    }
    
    @ExceptionHandler(AccessDeniedException.class)
    @ResponseStatus(HttpStatus.FORBIDDEN)
    public ApiError handleAccessDenied(AccessDeniedException ex) {
        return ApiError.builder()
            .status(403)
            .error("Forbidden")
            .message("You don't have permission to access this resource")
            .build();
    }
    
    @ExceptionHandler(DataIntegrityViolationException.class)
    @ResponseStatus(HttpStatus.CONFLICT)
    public ApiError handleConflict(DataIntegrityViolationException ex) {
        return ApiError.builder()
            .status(409)
            .error("Conflict")
            .message("Database constraint violated")
            .build();
    }
    
    @ExceptionHandler(Exception.class)
    @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
    public ApiError handleAll(Exception ex) {
        log.error("Unexpected error", ex);
        return ApiError.builder()
            .status(500)
            .error("Internal Server Error")
            .message("Something went wrong")
            .build();
    }
}
```

---

## 10. Security Annotations

### 🔹 80. `@EnableWebSecurity`

**Hinglish Explanation:**
> Spring Security configuration enable karne ke liye. Security filter chain define karo ismein.

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        return http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers(HttpMethod.POST, "/api/users").permitAll()
                .anyRequest().authenticated()
            )
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .addFilterBefore(jwtFilter, UsernamePasswordAuthenticationFilter.class)
            .build();
    }
}
```

---

### 🔹 81. `@EnableMethodSecurity`

**Hinglish Explanation:**
> Method-level security annotations (`@PreAuthorize`, `@PostAuthorize`, `@Secured`) enable karne ke liye. Spring Boot 3+ mein ye use hota hai (purana `@EnableGlobalMethodSecurity` deprecated hai).

```java
@Configuration
@EnableMethodSecurity(prePostEnabled = true, securedEnabled = true)
public class MethodSecurityConfig { }
```

---

### 🔹 82. `@PreAuthorize`

**Hinglish Explanation:**
> Method execute hone SE PEHLE authorization check. SpEL expressions mein roles, permissions, method arguments sab check kar sakte ho. Most powerful security annotation.

```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser(Long id) { ... }

@PreAuthorize("hasAnyRole('ADMIN', 'MANAGER')")
public List<Report> getReports() { ... }

@PreAuthorize("#userId == authentication.principal.id or hasRole('ADMIN')")
public User getUser(@Param("userId") Long userId) { ... }

@PreAuthorize("@securityService.canAccess(#projectId, authentication)")
public Project getProject(Long projectId) { ... }
```

---

### 🔹 83. `@PostAuthorize`

**Hinglish Explanation:**
> Method execute hone KE BAAD check. Return value access kar sakte ho `returnObject` se. Use case: method execute karo, but return value mein sensitive data ho toh check karo ki user ko dikhana chahiye ya nahi.

```java
@PostAuthorize("returnObject.owner == authentication.principal.username or hasRole('ADMIN')")
public Document getDocument(Long id) {
    return documentRepository.findById(id).orElseThrow();
}
```

---

### 🔹 84. `@Secured`

**Hinglish Explanation:**
> Simple role-based access control. SpEL nahi support karta — sirf role names dete ho. `@PreAuthorize` zyada powerful hai, but simple cases ke liye `@Secured` readable hai.

```java
@Secured({"ROLE_ADMIN", "ROLE_SUPER_ADMIN"})
public void adminOnlyMethod() { ... }
```

---

### 🔹 85. `@RolesAllowed` (JSR-250)

**Hinglish Explanation:**
> Java standard annotation for role-based access (same as `@Secured` but JSR-250 standard).

```java
@RolesAllowed({"ADMIN", "MANAGER"})
public void manageTeam() { ... }
```

---

## 11. Caching Annotations

### 🔹 86. `@EnableCaching`

**Hinglish Explanation:**
> Application mein caching enable karne ke liye main config class pe lagao.

---

### 🔹 87. `@Cacheable`

**Hinglish Explanation:**
> Method ka result cache mein store karo. Next call pe same parameters ke saath cache se return hoga — method execute nahi hoga. Database calls reduce hote hain. Cache miss pe method execute hota hai aur result cache mein store hota hai.

```java
@Service
public class UserService {
    
    @Cacheable(value = "users", key = "#id")
    public User findById(Long id) {
        log.info("Fetching from DB..."); // Only logged on cache miss
        return userRepository.findById(id).orElseThrow();
    }
    
    @Cacheable(value = "userSearch", key = "#name + '-' + #page",
               condition = "#name.length() > 2") // Only cache if name > 2 chars
    public Page<User> search(String name, int page) {
        return userRepository.findByNameContaining(name, PageRequest.of(page, 20));
    }
    
    @Cacheable(value = "users", key = "#id", unless = "#result.status == 'INACTIVE'")
    public User findActive(Long id) {
        // Don't cache inactive users
        return userRepository.findById(id).orElseThrow();
    }
}
```

---

### 🔹 88. `@CachePut`

**Hinglish Explanation:**
> Method HAMESHA execute hota hai aur result cache UPDATE karta hai. Create/Update operations ke baad cache sync rakhne ke liye.

```java
@CachePut(value = "users", key = "#result.id")
public User updateUser(UpdateUserRequest request) {
    User user = userRepository.findById(request.getId()).orElseThrow();
    user.setName(request.getName());
    return userRepository.save(user);
}
```

---

### 🔹 89. `@CacheEvict`

**Hinglish Explanation:**
> Cache se entry remove karo. Delete operation ke baad, ya stale data clean karne ke liye. `allEntries = true` se puri cache clean hoti hai.

```java
@CacheEvict(value = "users", key = "#id")
public void deleteUser(Long id) {
    userRepository.deleteById(id);
}

@CacheEvict(value = {"users", "userSearch"}, allEntries = true)
@Scheduled(fixedRate = 3600000) // every hour
public void clearAllCaches() {
    log.info("Cache cleared");
}
```

---

### 🔹 90. `@Caching`

**Hinglish Explanation:**
> Multiple caching operations ek method pe combine karo.

```java
@Caching(
    put = { @CachePut(value = "users", key = "#result.id") },
    evict = { @CacheEvict(value = "userSearch", allEntries = true) }
)
public User createUser(CreateUserRequest request) {
    return userRepository.save(new User(request));
}
```

---

## 12. Scheduling & Async Annotations

### 🔹 91. `@EnableScheduling`

**Hinglish Explanation:**
> Background scheduled tasks enable karne ke liye config class pe lagao.

---

### 🔹 92. `@Scheduled`

**Hinglish Explanation:**
> Method ko automatically fixed interval ya cron schedule pe execute karo. Background cleanup, report generation, health checks ke liye use hota hai. Method void return kare aur no parameters le.

```java
@Component
@Slf4j
public class ScheduledJobs {
    
    // Every day at 2:30 AM
    @Scheduled(cron = "0 30 2 * * ?")
    public void dailyCleanup() {
        log.info("Running daily cleanup...");
        sessionRepository.deleteExpired();
    }
    
    // Every 5 minutes (from start of previous execution)
    @Scheduled(fixedRate = 300000)
    public void healthCheck() {
        monitoringService.checkAllServices();
    }
    
    // 30 seconds after previous execution ENDS
    @Scheduled(fixedDelay = 30000)
    public void processQueue() {
        messageQueue.processNextBatch();
    }
    
    // With initial delay
    @Scheduled(fixedRate = 60000, initialDelay = 10000)
    public void syncData() {
        syncService.syncExternalData();
    }
    
    // Using properties
    @Scheduled(cron = "${app.scheduler.cleanup-cron}")
    public void configuredJob() { ... }
}
```

---

### 🔹 93. `@EnableAsync`

**Hinglish Explanation:**
> Asynchronous method execution enable karo. Config class pe lagao aur custom thread pool define karo.

```java
@Configuration
@EnableAsync
public class AsyncConfig implements AsyncConfigurer {
    
    @Override
    @Bean(name = "taskExecutor")
    public Executor getAsyncExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(5);
        executor.setMaxPoolSize(20);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("Async-");
        executor.setRejectedExecutionHandler(new ThreadPoolExecutor.CallerRunsPolicy());
        executor.initialize();
        return executor;
    }
    
    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return (throwable, method, params) -> 
            log.error("Async error in {}: {}", method.getName(), throwable.getMessage());
    }
}
```

---

### 🔹 94. `@Async`

**Hinglish Explanation:**
> Method ko separate thread mein execute karo — caller thread block nahi hoga. Email sending, file processing, notifications jaise tasks ke liye. `void` ya `CompletableFuture` return type use karo.
>
> **⚠️ Pitfall:** Same class mein self-invocation karo toh `@Async` kaam nahi karega (proxy bypass). Doosri bean se call karo.

```java
@Service
@Slf4j
public class NotificationService {
    
    @Async
    public void sendEmail(String to, String subject, String body) {
        // Runs in separate thread
        log.info("Sending email to {} on thread {}", to, Thread.currentThread().getName());
        emailClient.send(to, subject, body);
    }
    
    @Async
    public CompletableFuture<ReportData> generateReport(ReportRequest request) {
        // Caller can get result later
        ReportData data = reportEngine.generate(request);
        return CompletableFuture.completedFuture(data);
    }
    
    @Async("customExecutor") // Use specific executor
    public void processLargeFile(Path filePath) {
        fileProcessor.process(filePath);
    }
}

// Caller
@Service
public class OrderService {
    @Autowired private NotificationService notificationService;
    
    public Order placeOrder(OrderRequest request) {
        Order order = orderRepository.save(new Order(request));
        notificationService.sendEmail(order.getEmail(), "Order Placed", "..."); // non-blocking
        return order;
    }
}
```

---

## 13. AOP Annotations

### 🔹 95. `@Aspect`

**Hinglish Explanation:**
> AOP (Aspect-Oriented Programming) — cross-cutting concerns (logging, security, metrics, retry) ko alag module mein separate karo. `@Aspect` class ko aspect declare karta hai.

---

### 🔹 96. `@Before`

**Hinglish Explanation:**
> Target method se PEHLE execute hota hai. Logging, validation, security checks ke liye.

---

### 🔹 97. `@After`

**Hinglish Explanation:**
> Target method ke BAAD execute hota hai (chahe success ho ya exception). Cleanup ke liye.

---

### 🔹 98. `@AfterReturning`

**Hinglish Explanation:**
> Sirf successful execution ke baad. Return value access kar sakte ho.

---

### 🔹 99. `@AfterThrowing`

**Hinglish Explanation:**
> Sirf exception throw hone pe. Exception object access kar sakte ho.

---

### 🔹 100. `@Around`

**Hinglish Explanation:**
> Most powerful — method ke pehle aur baad dono mein logic lagao. Method execution control kar sakte ho — execute karna ya nahi, retry karna, timing measure karna. `ProceedingJoinPoint.proceed()` call karke actual method execute hota hai.

```java
@Aspect
@Component
@Slf4j
public class PerformanceAspect {
    
    @Around("@annotation(Timed)")
    public Object measureTime(ProceedingJoinPoint joinPoint) throws Throwable {
        String method = joinPoint.getSignature().toShortString();
        long start = System.nanoTime();
        
        try {
            Object result = joinPoint.proceed();
            long duration = (System.nanoTime() - start) / 1_000_000;
            log.info("⏱️ {} executed in {}ms", method, duration);
            return result;
        } catch (Throwable ex) {
            long duration = (System.nanoTime() - start) / 1_000_000;
            log.error("❌ {} failed after {}ms: {}", method, duration, ex.getMessage());
            throw ex;
        }
    }
    
    @Around("@annotation(Retry)")
    public Object retry(ProceedingJoinPoint joinPoint) throws Throwable {
        int maxAttempts = joinPoint.getSignature().getDeclaringType()
            .getMethod(joinPoint.getSignature().getName())
            .getAnnotation(Retry.class).maxAttempts();
        
        Throwable lastException = null;
        for (int attempt = 1; attempt <= maxAttempts; attempt++) {
            try {
                return joinPoint.proceed();
            } catch (Throwable ex) {
                lastException = ex;
                log.warn("Attempt {}/{} failed: {}", attempt, maxAttempts, ex.getMessage());
                Thread.sleep(1000 * attempt); // exponential backoff
            }
        }
        throw lastException;
    }
}

// Pointcut expressions
@Aspect
@Component
public class LoggingAspect {
    
    // All methods in service package
    @Before("execution(* com.example.service.*.*(..))")
    public void logServiceCalls(JoinPoint jp) { ... }
    
    // All methods annotated with @Loggable
    @Around("@annotation(com.example.annotation.Loggable)")
    public Object logAnnotated(ProceedingJoinPoint pjp) throws Throwable { ... }
    
    // All public methods in classes annotated with @RestController
    @Before("@within(org.springframework.web.bind.annotation.RestController)")
    public void logControllerCalls(JoinPoint jp) { ... }
}
```

---

## 14. Testing Annotations

### 🔹 101. `@SpringBootTest`

**Hinglish Explanation:**
> Full integration test — pura Spring context load hota hai. Slow but complete testing. Random port pe server start kar sakte ho. End-to-end testing ke liye.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class OrderIntegrationTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @Test
    void shouldCreateOrder() {
        OrderRequest request = new OrderRequest("Product A", 2);
        ResponseEntity<Order> response = restTemplate.postForEntity("/api/orders", request, Order.class);
        
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(response.getBody().getId()).isNotNull();
    }
}
```

---

### 🔹 102. `@WebMvcTest`

**Hinglish Explanation:**
> Sirf web layer (controller) test karo — service/repository beans load nahi hongi. Fast unit tests. MockMvc se HTTP requests simulate karo. Dependencies mock karo `@MockBean` se.

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {
    
    @Autowired private MockMvc mockMvc;
    @MockBean private ProductService productService;
    
    @Test
    void shouldReturnProduct() throws Exception {
        Product product = new Product(1L, "Laptop", BigDecimal.valueOf(999.99));
        when(productService.findById(1L)).thenReturn(product);
        
        mockMvc.perform(get("/api/products/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("Laptop"))
            .andExpect(jsonPath("$.price").value(999.99));
    }
    
    @Test
    void shouldReturnValidationError() throws Exception {
        String invalidBody = """
            {"name": "", "price": -10}
            """;
        
        mockMvc.perform(post("/api/products")
                .contentType(MediaType.APPLICATION_JSON)
                .content(invalidBody))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.fieldErrors.name").exists());
    }
}
```

---

### 🔹 103. `@DataJpaTest`

**Hinglish Explanation:**
> Sirf JPA layer test karo — embedded DB (H2) use hota hai, sirf repository beans load hoti hain. Fast. Har test method ke baad rollback hota hai.

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE) // use real DB
class UserRepositoryTest {
    
    @Autowired private TestEntityManager em;
    @Autowired private UserRepository userRepository;
    
    @Test
    void shouldFindByEmail() {
        em.persistAndFlush(new User("John", "john@test.com"));
        
        Optional<User> found = userRepository.findByEmail("john@test.com");
        
        assertThat(found).isPresent();
        assertThat(found.get().getName()).isEqualTo("John");
    }
}
```

---

### 🔹 104. `@MockBean`

**Hinglish Explanation:**
> Spring context mein real bean ki jagah mock inject karo. Mockito mock banta hai. Behavior define karo `when(...).thenReturn(...)` se.

---

### 🔹 105. `@SpyBean`

**Hinglish Explanation:**
> Real bean rakho but selected methods override karo. Partially mock. Real implementation chale but kuch methods ka behavior change karo.

```java
@SpringBootTest
class OrderServiceTest {
    
    @SpyBean
    private EmailService emailService;
    
    @Test
    void shouldProcessOrderWithoutSendingRealEmail() {
        doNothing().when(emailService).sendEmail(any(), any(), any());
        
        orderService.placeOrder(request);
        
        verify(emailService).sendEmail(any(), eq("Order Confirmed"), any());
    }
}
```

---

### 🔹 106. `@TestConfiguration`

**Hinglish Explanation:**
> Test-specific beans define karo jo sirf test mein override karni hain.

```java
@TestConfiguration
public class TestConfig {
    @Bean
    public Clock clock() {
        return Clock.fixed(Instant.parse("2025-01-01T00:00:00Z"), ZoneId.UTC);
    }
}
```

---

### 🔹 107. `@Testcontainers` & `@Container`

**Hinglish Explanation:**
> Real database/Redis/Kafka containers test mein start karo Docker ke through. Integration testing ke liye H2 se better — real DB behavior test hota hai.

```java
@SpringBootTest
@Testcontainers
class UserRepositoryIntegrationTest {
    
    @Container
    static PostgreSQLContainer<?> postgres = new PostgreSQLContainer<>("postgres:15")
        .withDatabaseName("testdb");
    
    @DynamicPropertySource
    static void setProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgres::getJdbcUrl);
        registry.add("spring.datasource.username", postgres::getUsername);
        registry.add("spring.datasource.password", postgres::getPassword);
    }
}
```

---

## 15. Conditional Annotations

### 🔹 108. `@ConditionalOnProperty`

**Hinglish Explanation:**
> Property ke basis pe bean banao ya mat banao. Feature flags ke liye perfect.

```java
@Bean
@ConditionalOnProperty(name = "app.cache.type", havingValue = "redis")
public CacheManager redisCacheManager() { ... }

@Bean
@ConditionalOnProperty(name = "app.cache.type", havingValue = "local", matchIfMissing = true)
public CacheManager localCacheManager() { ... }
```

---

### 🔹 109. `@ConditionalOnClass`

**Hinglish Explanation:**
> Classpath mein specific class exist kare toh hi bean banao. Auto-configuration mein use hota hai.

```java
@Bean
@ConditionalOnClass(RedisTemplate.class)
public CacheManager redisCacheManager() { ... }
```

---

### 🔹 110. `@ConditionalOnMissingBean`

**Hinglish Explanation:**
> Agar user ne apni bean define nahi ki toh default provide karo. Customization allow karta hai.

```java
@Bean
@ConditionalOnMissingBean(PasswordEncoder.class)
public PasswordEncoder defaultPasswordEncoder() {
    return new BCryptPasswordEncoder();
}
```

---

### 🔹 111. `@ConditionalOnBean`

**Hinglish Explanation:**
> Agar specific bean already context mein hai tabhi ye bean banao.

---

### 🔹 112. `@ConditionalOnExpression`

**Hinglish Explanation:**
> SpEL expression ke basis pe conditional bean creation.

```java
@Bean
@ConditionalOnExpression("${app.feature.x:false} and ${app.env} != 'test'")
public FeatureXService featureXService() { ... }
```

---

### 🔹 113. `@ConditionalOnWebApplication` / `@ConditionalOnNotWebApplication`

**Hinglish Explanation:**
> Web application hai ya non-web application — uske basis pe bean banao.

---

## 16. Event & Listener Annotations

### 🔹 114. `@EventListener`

**Hinglish Explanation:**
> Application events listen karo. Decoupled architecture — publisher ko listener ka pata nahi, listener ko publisher ka pata nahi. Loose coupling achieve hota hai.

```java
// Event class
public record OrderPlacedEvent(Long orderId, String customerEmail, BigDecimal total) {}

// Publisher
@Service
public class OrderService {
    @Autowired private ApplicationEventPublisher publisher;
    
    @Transactional
    public Order place(OrderRequest req) {
        Order order = orderRepository.save(new Order(req));
        publisher.publishEvent(new OrderPlacedEvent(order.getId(), order.getEmail(), order.getTotal()));
        return order;
    }
}

// Listeners
@Component
public class OrderEventListeners {
    
    @EventListener
    public void sendConfirmation(OrderPlacedEvent event) {
        emailService.send(event.customerEmail(), "Order #" + event.orderId() + " confirmed!");
    }
    
    @EventListener
    public void updateInventory(OrderPlacedEvent event) {
        inventoryService.reserve(event.orderId());
    }
    
    @EventListener(condition = "#event.total.compareTo(new java.math.BigDecimal('1000')) > 0")
    public void flagHighValueOrder(OrderPlacedEvent event) {
        // Only for orders > 1000
        fraudService.review(event.orderId());
    }
}
```

---

### 🔹 115. `@TransactionalEventListener`

**Hinglish Explanation:**
> Transaction commit/rollback ke baad event process karo. Ye CRITICAL hai — agar transaction rollback ho jaye toh email/notification bhejna sahi nahi. Ye ensure karta hai ki sirf successful transaction ke baad listener execute ho.

```java
@Component
public class AfterCommitListeners {
    
    @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    @Async
    public void onOrderCommitted(OrderPlacedEvent event) {
        // Only runs AFTER transaction successfully commits
        smsService.sendOrderSMS(event.customerEmail());
    }
    
    @TransactionalEventListener(phase = TransactionPhase.AFTER_ROLLBACK)
    public void onOrderFailed(OrderPlacedEvent event) {
        // Cleanup on failure
        log.error("Order {} transaction failed", event.orderId());
    }
}
```

---

## 17. Actuator & Monitoring Annotations

### 🔹 116. `@Endpoint`

**Hinglish Explanation:**
> Custom Actuator endpoint banao. Health checks, custom metrics, admin operations expose karo.

```java
@Component
@Endpoint(id = "app-info")
public class AppInfoEndpoint {
    
    @ReadOperation
    public Map<String, Object> info() {
        return Map.of(
            "version", "2.1.0",
            "uptime", ManagementFactory.getRuntimeMXBean().getUptime(),
            "activeUsers", userService.getActiveCount()
        );
    }
    
    @WriteOperation
    public void clearCache(@Selector String cacheName) {
        cacheManager.getCache(cacheName).clear();
    }
}
```

---

### 🔹 117. `@Timed` (Micrometer)

**Hinglish Explanation:**
> Method execution time automatically measure karo metrics ke liye. Prometheus/Grafana mein visualize kar sakte ho.

```java
@Timed(value = "order.processing.time", description = "Time to process orders")
public Order processOrder(OrderRequest request) { ... }
```

---

### 🔹 118. `@Counted` (Micrometer)

**Hinglish Explanation:**
> Method call count track karo.

---

## 18. Lombok Annotations (Commonly Used with Spring Boot)

### 🔹 119-126. Lombok Annotations

**Hinglish Explanation:**
> Lombok boilerplate code (getters, setters, constructors, toString, equals/hashCode) automatically generate karta hai compile-time pe.

```java
// 119. @Getter / @Setter — generate getters/setters
// 120. @NoArgsConstructor — no-arg constructor
// 121. @AllArgsConstructor — all-args constructor  
// 122. @RequiredArgsConstructor — final fields constructor (DI ke liye best!)
// 123. @Data — @Getter + @Setter + @ToString + @EqualsAndHashCode + @RequiredArgsConstructor
// 124. @Builder — builder pattern
// 125. @Slf4j — logger field generate
// 126. @ToString / @EqualsAndHashCode

@Entity
@Getter @Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString(exclude = "password")
@EqualsAndHashCode(of = "id")
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
    private String password;
}

// ✅ BEST for Services — @RequiredArgsConstructor replaces @Autowired constructor
@Service
@RequiredArgsConstructor
@Slf4j
public class UserService {
    private final UserRepository userRepository; // auto-injected
    private final PasswordEncoder passwordEncoder; // auto-injected
    
    public User create(CreateUserRequest request) {
        log.info("Creating user: {}", request.getEmail());
        // ...
    }
}
```

---

## 19. Jackson / Serialization Annotations

### 🔹 127-135. Jackson Annotations

**Hinglish Explanation:**
> JSON serialization/deserialization customize karo. REST APIs mein response format control karo.

```java
public class UserResponse {
    
    // 127. @JsonProperty — JSON field name change
    @JsonProperty("user_name")
    private String name;
    
    // 128. @JsonIgnore — field ko JSON mein mat include karo
    @JsonIgnore
    private String password;
    
    // 129. @JsonIgnoreProperties — class level pe multiple fields ignore
    // @JsonIgnoreProperties({"password", "secretKey"})
    
    // 130. @JsonFormat — date/time format
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "dd-MM-yyyy HH:mm:ss")
    private LocalDateTime createdAt;
    
    // 131. @JsonInclude — null/empty values skip karo
    @JsonInclude(JsonInclude.Include.NON_NULL)
    private String middleName;
    
    // 132. @JsonSerialize / @JsonDeserialize — custom serializer
    @JsonSerialize(using = MoneySerializer.class)
    private BigDecimal balance;
    
    // 133. @JsonCreator + @JsonProperty — custom deserialization constructor
    @JsonCreator
    public UserResponse(@JsonProperty("name") String name) {
        this.name = name;
    }
    
    // 134. @JsonAlias — accept multiple field names during deserialization
    @JsonAlias({"phone", "mobile", "contact_number"})
    private String phoneNumber;
    
    // 135. @JsonView — different views for same object
    // Useful: return limited fields in list API, full fields in detail API
}
```

**`@JsonView` Example:**
```java
public class Views {
    public interface Summary {}
    public interface Detail extends Summary {}
}

public class User {
    @JsonView(Views.Summary.class)
    private Long id;
    
    @JsonView(Views.Summary.class)
    private String name;
    
    @JsonView(Views.Detail.class)
    private String email;
    
    @JsonView(Views.Detail.class)
    private String phone;
}

@RestController
public class UserController {
    
    @GetMapping("/users")
    @JsonView(Views.Summary.class) // Returns only id, name
    public List<User> listUsers() { ... }
    
    @GetMapping("/users/{id}")
    @JsonView(Views.Detail.class) // Returns id, name, email, phone
    public User getUser(@PathVariable Long id) { ... }
}
```

---

## 20. Scenario-Based Interview Questions

### ❓ Scenario 1: @Transactional Not Working — Why?

**Question:** Aapne `@Transactional` lagaya lekin rollback nahi ho raha. Kya reasons ho sakte hain?

**Answer:**

| # | Problem | Solution |
|---|---------|----------|
| 1 | Self-invocation (same class mein call) | Dusri bean se call karo ya `TransactionTemplate` use karo |
| 2 | Private method pe lagaya | Public method pe lagao |
| 3 | Checked exception throw ho raha | `rollbackFor = Exception.class` add karo |
| 4 | Class Spring bean nahi hai | `@Service`/`@Component` lagao |
| 5 | Wrong proxy mode | Interface hai toh JDK proxy, class hai toh CGLIB — `proxyTargetClass=true` check karo |
| 6 | Exception catch kar ke swallow kar rahe | Exception propagate hone do |

```java
// ❌ WRONG: Self-invocation
@Service
public class OrderService {
    public void process() {
        this.save(); // PROXY BYPASS! Transaction won't work
    }
    @Transactional
    public void save() { ... }
}

// ✅ FIX: Inject self or use separate bean
@Service
public class OrderService {
    @Autowired private OrderPersistenceService persistenceService;
    
    public void process() {
        persistenceService.save(); // Goes through proxy ✅
    }
}
```

---

### ❓ Scenario 2: Multiple Implementations of Same Interface

**Question:** `NotificationService` interface ke `EmailNotification`, `SmsNotification`, `PushNotification` — teen implementations hain. Dynamically decide karna hai kaunsa use karna hai.

**Answer: Strategy Pattern with Spring**
```java
public interface NotificationService {
    void send(String to, String message);
    NotificationType getType();
}

@Service
public class EmailNotificationService implements NotificationService {
    public void send(String to, String message) { ... }
    public NotificationType getType() { return NotificationType.EMAIL; }
}

@Service
public class SmsNotificationService implements NotificationService {
    public void send(String to, String message) { ... }
    public NotificationType getType() { return NotificationType.SMS; }
}

// Strategy Registry
@Component
public class NotificationFactory {
    private final Map<NotificationType, NotificationService> services;
    
    public NotificationFactory(List<NotificationService> serviceList) {
        this.services = serviceList.stream()
            .collect(Collectors.toMap(NotificationService::getType, Function.identity()));
    }
    
    public NotificationService get(NotificationType type) {
        return Optional.ofNullable(services.get(type))
            .orElseThrow(() -> new UnsupportedOperationException("No service for: " + type));
    }
}
```

---

### ❓ Scenario 3: Circular Dependency

**Question:** ServiceA → ServiceB → ServiceA. Application start nahi ho rahi.

**Answer:**
```java
// Option 1: @Lazy (quick fix, not ideal)
@Service
public class ServiceA {
    public ServiceA(@Lazy ServiceB serviceB) { this.serviceB = serviceB; }
}

// Option 2: ✅ BEST — Redesign with Event-Driven
@Service
public class ServiceA {
    @Autowired private ApplicationEventPublisher publisher;
    
    public void doSomething() {
        publisher.publishEvent(new SomethingHappenedEvent(data));
    }
}

@Service
public class ServiceB {
    @EventListener
    public void onSomethingHappened(SomethingHappenedEvent event) { ... }
}

// Option 3: Extract common logic into ServiceC
// Option 4: Setter injection (breaks immutability — avoid)
```

---

### ❓ Scenario 4: N+1 Query Problem

**Question:** 100 departments fetch kiye, har department ke employees alag query se aa rahe hain (101 queries total). Kaise fix karein?

**Answer:**
```java
// ❌ Problem
@OneToMany(mappedBy = "department", fetch = FetchType.LAZY)
private List<Employee> employees;
// Jab employees access karo toh har department ke liye separate query

// ✅ Solution 1: JOIN FETCH in @Query
@Query("SELECT d FROM Department d JOIN FETCH d.employees")
List<Department> findAllWithEmployees();

// ✅ Solution 2: @EntityGraph
@EntityGraph(attributePaths = {"employees"})
List<Department> findAll();

// ✅ Solution 3: @BatchSize (Hibernate)
@OneToMany(mappedBy = "department")
@BatchSize(size = 25) // Fetch employees in batches of 25
private List<Employee> employees;

// ✅ Solution 4: Projection (fetch only needed fields)
@Query("SELECT new com.example.dto.DeptSummary(d.name, SIZE(d.employees)) FROM Department d")
List<DeptSummary> findDepartmentSummaries();
```

---

### ❓ Scenario 5: REST API Versioning

**Question:** API versioning kaise implement karein production mein?

```java
// Strategy 1: URI Versioning (Most Common)
@RestController @RequestMapping("/api/v1/users") public class UserControllerV1 { }
@RestController @RequestMapping("/api/v2/users") public class UserControllerV2 { }

// Strategy 2: Header Versioning
@GetMapping(value = "/users", headers = "X-API-VERSION=2")
public UserV2 getUserV2() { }

// Strategy 3: Parameter Versioning
@GetMapping(value = "/users", params = "version=2")
public UserV2 getUserV2() { }

// Strategy 4: Content Negotiation
@GetMapping(value = "/users", produces = "application/vnd.myapp.v2+json")
public UserV2 getUserV2() { }
```

---

### ❓ Scenario 6: Bean Scope Issue — Prototype Inside Singleton

**Question:** Prototype bean singleton mein inject kiya — har baar same instance mil raha hai.

**Answer:**
```java
// ❌ Problem: Prototype injected once into singleton — never refreshed
@Service // singleton
public class ReportService {
    @Autowired private ReportGenerator generator; // prototype — but injected only once!
}

// ✅ Solution 1: ObjectFactory / ObjectProvider
@Service
public class ReportService {
    @Autowired private ObjectProvider<ReportGenerator> generatorProvider;
    
    public Report generate() {
        ReportGenerator gen = generatorProvider.getObject(); // fresh instance each time
        return gen.build();
    }
}

// ✅ Solution 2: @Lookup method
@Service
public abstract class ReportService {
    @Lookup
    protected abstract ReportGenerator createGenerator();
    
    public Report generate() {
        return createGenerator().build();
    }
}

// ✅ Solution 3: Scoped Proxy
@Component
@Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class ReportGenerator { }
```

---

### ❓ Scenario 7: Custom Auto-Configuration

**Question:** Apni library ke liye auto-configuration kaise banayein?

```java
@Configuration
@ConditionalOnClass(MyLibrary.class)
@ConditionalOnProperty(name = "mylib.enabled", havingValue = "true", matchIfMissing = true)
@EnableConfigurationProperties(MyLibProperties.class)
public class MyLibAutoConfiguration {
    
    @Bean
    @ConditionalOnMissingBean
    public MyLibClient myLibClient(MyLibProperties props) {
        return new MyLibClient(props.getApiKey(), props.getBaseUrl());
    }
}

// Register in: META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports
// Content: com.mylib.MyLibAutoConfiguration
```

---

### ❓ Scenario 8: Handling Optimistic Locking

**Question:** Two users same record edit kar rahe hain simultaneously. Conflict kaise handle karein?

```java
@Entity
public class Product {
    @Id private Long id;
    @Version private Long version; // JPA increments on each save
    private String name;
    private BigDecimal price;
}

@Service
public class ProductService {
    
    @Transactional
    @Retryable(value = OptimisticLockException.class, maxAttempts = 3)
    public Product updatePrice(Long id, BigDecimal newPrice) {
        Product product = productRepository.findById(id).orElseThrow();
        product.setPrice(newPrice);
        return productRepository.save(product);
        // If version mismatch → OptimisticLockException → retry
    }
}

@RestControllerAdvice
public class ExceptionHandler {
    @ExceptionHandler(OptimisticLockException.class)
    @ResponseStatus(HttpStatus.CONFLICT)
    public ApiError handleConflict() {
        return new ApiError(409, "Resource was modified by another user. Please retry.");
    }
}
```

---

### ❓ Scenario 9: Rate Limiting with Custom Annotation + AOP

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface RateLimit {
    int requests() default 10;
    int seconds() default 60;
}

@Aspect
@Component
public class RateLimitAspect {
    private final Map<String, Bucket> buckets = new ConcurrentHashMap<>();
    
    @Around("@annotation(rateLimit)")
    public Object limit(ProceedingJoinPoint pjp, RateLimit rateLimit) throws Throwable {
        String key = getClientIp() + ":" + pjp.getSignature().getName();
        Bucket bucket = buckets.computeIfAbsent(key, k -> createBucket(rateLimit));
        
        if (bucket.tryConsume(1)) {
            return pjp.proceed();
        }
        throw new RateLimitExceededException("Too many requests");
    }
}

// Usage
@GetMapping("/api/search")
@RateLimit(requests = 20, seconds = 60)
public List<Product> search(@RequestParam String q) { ... }
```

---

### ❓ Scenario 10: Multi-Tenant Application

```java
@Component
@Scope(value = WebApplicationContext.SCOPE_REQUEST, proxyMode = ScopedProxyMode.TARGET_CLASS)
public class TenantContext {
    private String tenantId;
    // getter/setter
}

@Component
public class TenantInterceptor implements HandlerInterceptor {
    @Autowired private TenantContext tenantContext;
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) {
        String tenantId = request.getHeader("X-Tenant-ID");
        if (tenantId == null) throw new MissingTenantException();
        tenantContext.setTenantId(tenantId);
        return true;
    }
}
```

---

## 21. Master Differences Table

| # | Annotation A | Annotation B | Key Difference |
|---|---|---|---|
| 1 | `@Component` | `@Service` | `@Service` is semantically for business logic; `@Component` is generic. Functionally same. |
| 2 | `@Component` | `@Bean` | `@Component` = auto-detected class. `@Bean` = manually defined in `@Configuration`. |
| 3 | `@Controller` | `@RestController` | `@Controller` returns views. `@RestController` returns JSON (adds `@ResponseBody`). |
| 4 | `@RequestParam` | `@PathVariable` | `@RequestParam` = query string `?key=val`. `@PathVariable` = URL path `/users/{id}`. |
| 5 | `@RequestBody` | `@RequestParam` | `@RequestBody` = JSON body to object. `@RequestParam` = query params to primitives. |
| 6 | `@Valid` | `@Validated` | `@Validated` supports validation groups. `@Valid` is standard JSR-303 cascading. |
| 7 | `@Autowired` | `@Inject` | `@Autowired` is Spring-specific (has `required`). `@Inject` is JSR-330 standard. |
| 8 | `@Qualifier` | `@Primary` | `@Qualifier` explicitly picks a bean. `@Primary` sets default. Qualifier wins over Primary. |
| 9 | `@Configuration` | `@Component` | `@Configuration` uses CGLIB proxy — inter-`@Bean` method calls return same instance. `@Component` with `@Bean` doesn't. |
| 10 | `@Value` | `@ConfigurationProperties` | `@Value` = single value. `@ConfigurationProperties` = group of type-safe properties. |
| 11 | `@Profile` | `@ConditionalOnProperty` | `@Profile` = environment-based (dev/prod). `@ConditionalOnProperty` = feature flag-based. |
| 12 | `@Cacheable` | `@CachePut` | `@Cacheable` skips method if cached. `@CachePut` always executes and updates cache. |
| 13 | `@Async` | `@Scheduled` | `@Async` = run method in separate thread when called. `@Scheduled` = run automatically on schedule. |
| 14 | `@ExceptionHandler` | `@ControllerAdvice` | `@ExceptionHandler` = per-controller. `@ControllerAdvice` = global across all controllers. |
| 15 | `@PreAuthorize` | `@Secured` | `@PreAuthorize` supports SpEL. `@Secured` only role names. |
| 16 | `@OneToMany(LAZY)` | `@OneToMany(EAGER)` | LAZY = load on access (recommended). EAGER = load immediately (N+1 risk). |
| 17 | `@Transactional(REQUIRED)` | `@Transactional(REQUIRES_NEW)` | REQUIRED joins existing tx. REQUIRES_NEW always creates new (suspends existing). |
| 18 | `@EventListener` | `@TransactionalEventListener` | `@EventListener` = immediate. `@TransactionalEventListener` = after tx commit/rollback. |
| 19 | `@SpringBootTest` | `@WebMvcTest` | Full context vs only web layer. `@SpringBootTest` = slow/complete. `@WebMvcTest` = fast/focused. |
| 20 | `@MockBean` | `@SpyBean` | `@MockBean` = complete mock (all methods stubbed). `@SpyBean` = real bean with selective overrides. |
| 21 | `@GetMapping` | `@PostMapping` | GET = read/idempotent. POST = create/non-idempotent. |
| 22 | `@PutMapping` | `@PatchMapping` | PUT = full replacement. PATCH = partial update. |
| 23 | `EnumType.STRING` | `EnumType.ORDINAL` | STRING stores name (safe). ORDINAL stores index (breaks if reordered). |
| 24 | `@NotNull` | `@NotBlank` | `@NotNull` = not null (empty string OK). `@NotBlank` = not null + not empty + not whitespace. |
| 25 | `@NotEmpty` | `@NotBlank` | `@NotEmpty` = works on strings, collections, arrays. `@NotBlank` = strings only + trims whitespace. |
| 26 | `@Before` (AOP) | `@Around` (AOP) | `@Before` = only pre-execution. `@Around` = wraps entire method (pre + post + exception). |
| 27 | `@Lazy` | Eager (default) | `@Lazy` = initialized on first use. Default = initialized at startup. |
| 28 | `@Scope("prototype")` | `@Scope("singleton")` | Prototype = new instance per injection. Singleton = one instance shared. |

---

## 22. When to Use / When NOT to Use Guide

### 📋 Complete Decision Matrix

| Annotation | ✅ USE When | ❌ DON'T USE When |
|---|---|---|
| `@Component` | Generic utility/helper class | Class is clearly a service/repo/controller |
| `@Service` | Business logic layer | Data access or web handling |
| `@Repository` | Data access layer (DAO) | Business logic, even if it calls DB |
| `@Controller` | Server-side views (Thymeleaf/JSP) | Building REST APIs |
| `@RestController` | REST API endpoints | Returning HTML views |
| `@Autowired` | Need DI (prefer constructor) | Static methods, non-Spring classes |
| `@Value` | 1-2 simple property values | Many related properties (use `@ConfigurationProperties`) |
| `@ConfigurationProperties` | Group of related configs | Single value injection |
| `@Bean` | Third-party class, complex init | Your own class (use `@Component`) |
| `@Transactional` | Service layer, multi-step DB ops | Controller layer, read-only queries (use `readOnly=true`) |
| `@Async` | Fire-and-forget tasks (email, logs) | Need immediate result, DB transactions |
| `@Cacheable` | Expensive, frequently read, rarely changed data | Frequently changing data, user-specific data |
| `@Scheduled` | Periodic background tasks | One-time tasks, user-triggered actions |
| `@Profile` | Environment-specific config (dev/prod) | Feature flags (use `@ConditionalOnProperty`) |
| `@Lazy` | Heavy beans used rarely, break circular deps | Frequently used beans, fails-fast needed |
| `@EntityGraph` | Solving N+1 for specific queries | All queries (over-fetching risk) |
| `@Valid` | Simple validation, nested cascading | Need validation groups |
| `@Validated` | Validation groups, method-level validation | Simple single-group validation |
| `@PreAuthorize` | Complex authorization with SpEL | Simple role check (can use `@Secured`) |
| `@EventListener` | Decoupled cross-cutting reactions | Tight coupling needed, synchronous requirement |
| `@TransactionalEventListener` | Actions after confirmed DB commit (email, notification) | Immediate processing needed |
| `@Around` (AOP) | Need full control (timing, retry, modify result) | Simple pre/post logic (use `@Before`/`@After`) |
| `@Scope("prototype")` | Stateful beans, not thread-safe | Stateless beans (singleton is better) |
| Constructor Injection | Always (testable, immutable) | — |
| Field Injection | Never in production code | — (only acceptable in test classes) |

---

## 🎯 Quick Interview Tips

### Top 10 Most Asked Annotations in Interviews:
1. `@SpringBootApplication` — what does it contain?
2. `@RestController` vs `@Controller`
3. `@Autowired` — types of injection, best practice
4. `@Transactional` — pitfalls, propagation
5. `@Qualifier` vs `@Primary`
6. `@RequestBody` vs `@RequestParam` vs `@PathVariable`
7. `@Valid` vs `@Validated`
8. `@Component` vs `@Service` vs `@Repository`
9. `@Configuration` + `@Bean` — when and why
10. `@ExceptionHandler` + `@ControllerAdvice` — global error handling

### Golden Rules:
```
1. Constructor Injection > Field Injection (ALWAYS)
2. @Transactional on Service layer (NEVER on Controller)
3. FetchType.LAZY by default (EAGER only when sure)
4. @ConfigurationProperties > @Value (for grouped properties)
5. @RestControllerAdvice for global exception handling
6. Use @Profile for env config, @ConditionalOnProperty for feature flags
7. @Async methods must be called from DIFFERENT bean
8. @Transactional methods must be PUBLIC
9. Use @EntityGraph or JOIN FETCH for N+1 problems
10. @Version for optimistic locking in concurrent updates
```

---

## 📊 Annotation Count Summary

| Category | Count |
|----------|-------|
| Core Stereotype | 5 |
| Spring Boot Application | 5 |
| REST API / Web | 17 |
| Dependency Injection | 7 |
| Configuration & Properties | 7 |
| JPA / Database | 19 |
| Transaction | 1 |
| Validation | 15 |
| Exception Handling | 3 |
| Security | 6 |
| Caching | 5 |
| Scheduling & Async | 4 |
| AOP | 6 |
| Testing | 7 |
| Conditional | 6 |
| Events | 2 |
| Actuator | 3 |
| Lombok | 8 |
| Jackson | 9 |
| **TOTAL** | **135** |

---

> 💡 **Pro Tip:** Is guide ko bookmark karo aur interview se pehle differences table aur scenarios zarur revise karo. Real interviews mein annotations ka isolated knowledge nahi puchte — scenario-based questions aate hain jahan multiple annotations ka combined usage samajhna hota hai.
