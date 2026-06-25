# 🚀 Spring Boot Microservices — Zero to Advanced
### Complete Notes with Hindi-Hinglish Explanations | 3+ Years Experience Level

> **Kaise use karein ye notes:** Har topic mein pehle concept explain hoga Hindi/Hinglish mein, phir English mein proper definition, phir hands-on code, aur end mein interview Q&A.

---

## 📋 Table of Contents

1. [Monolith vs Microservices](#1-monolith-vs-microservices)
2. [Spring Boot Basics Recap](#2-spring-boot-basics-recap)
3. [Service Discovery — Eureka](#3-service-discovery--eureka)
4. [API Gateway — Spring Cloud Gateway](#4-api-gateway--spring-cloud-gateway)
5. [Load Balancing — Ribbon / Spring Cloud LoadBalancer](#5-load-balancing)
6. [Inter-Service Communication — Feign Client & RestTemplate](#6-inter-service-communication)
7. [Circuit Breaker — Resilience4j](#7-circuit-breaker--resilience4j)
8. [Config Server — Spring Cloud Config](#8-config-server--spring-cloud-config)
9. [Distributed Tracing — Zipkin + Sleuth / Micrometer](#9-distributed-tracing)
10. [Message Queue — Kafka & RabbitMQ](#10-message-queue--kafka--rabbitmq)
11. [Security — JWT + OAuth2 + Keycloak](#11-security--jwt--oauth2--keycloak)
12. [Containerization — Docker + Docker Compose](#12-containerization--docker--docker-compose)
13. [Orchestration — Kubernetes (K8s)](#13-orchestration--kubernetes)
14. [Service Mesh — Istio Basics](#14-service-mesh--istio)
15. [Distributed Database Patterns — Saga, CQRS, Event Sourcing](#15-distributed-database-patterns)
16. [API Documentation — Swagger / OpenAPI](#16-api-documentation)
17. [Monitoring — Prometheus + Grafana + ELK Stack](#17-monitoring--prometheus--grafana--elk)
18. [Testing Microservices](#18-testing-microservices)
19. [CI/CD Pipeline](#19-cicd-pipeline)
20. [Real World Project Structure](#20-real-world-project-structure)
21. [Master Interview Q&A](#21-master-interview-qa)

---

## 1. Monolith vs Microservices

### 🗣️ Hinglish Explanation
Bhai, samajhte hain ek example se. **Monolith** matlab ek bada sa dabba — sab kuch ek hi jagah hai. Jaise ek bade dukaan mein — grocery, electronics, kapde — sab ek hi jagah. Ek banda beemar pada toh poori dukaan band. Wahi **Microservices** matlab alag alag chhote chhote dukaan — grocery alag, electronics alag. Ek band hua toh baaki chal rahe hain.

3 saal experience ke baad tumhe yeh clearly bolna chahiye ki: **"Microservices independently deployable, loosely coupled services hain jo single business capability ko handle karte hain."**

### 📚 Model Answer (English)
A **Monolithic architecture** packages all application functionality — UI, business logic, data access — into a single deployable unit. Any change requires redeploying the entire application.

**Microservices architecture** decomposes an application into small, independent services, each:
- Running in its own process
- Communicating via lightweight protocols (HTTP/REST, gRPC, messaging)
- Independently deployable and scalable
- Owning its own database (database per service pattern)

### 🔄 Comparison Table

| Aspect | Monolith | Microservices |
|--------|----------|---------------|
| Deployment | Single unit | Independent per service |
| Scalability | Scale entire app | Scale specific service |
| Technology | Single stack | Polyglot (each service can differ) |
| Failure | One failure = all down | Isolated failures |
| Team | One team | Separate teams per service |
| DB | Shared DB | DB per service |
| Complexity | Simple to start | Complex infrastructure |
| Best for | Small apps, MVPs | Large, scalable systems |

### 🏗️ When to Use Microservices (3yr Experience Answer)
```
✅ Use Microservices when:
- Team size > 8-10 developers
- Independent deployment needed per module
- Different scaling requirements per feature
- Multiple teams, clear domain boundaries
- System grows beyond 100K+ users

❌ Don't use when:
- Startup/MVP phase
- Small team (< 5 devs)
- Simple CRUD app
- Domain boundaries unclear
```

---

## 2. Spring Boot Basics Recap

### 🗣️ Hinglish Explanation
Yaar, Spring Boot matlab Spring Framework ka baap — manually XML configure karne ki zaroorat nahi. **Auto-configuration** ki wajah se Spring Boot khud samajh leta hai ki tumhe kya chahiye. Microservices banane ke liye Spring Boot best choice hai kyunki:
- Embedded Tomcat — deploy karo bina server setup ke
- Starter dependencies — ek line mein sab dependencies
- Actuator — health check free mein

### 📦 Essential Dependencies for Microservices

```xml
<!-- pom.xml — parent -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<!-- Spring Cloud BOM — MUST add for cloud dependencies -->
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>2023.0.0</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

<!-- Core dependencies every microservice needs -->
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
</dependencies>
```

### 🗂️ Standard Microservice Project Structure
```
order-service/
├── src/
│   ├── main/
│   │   ├── java/com/company/orderservice/
│   │   │   ├── OrderServiceApplication.java
│   │   │   ├── controller/
│   │   │   │   └── OrderController.java
│   │   │   ├── service/
│   │   │   │   ├── OrderService.java
│   │   │   │   └── OrderServiceImpl.java
│   │   │   ├── repository/
│   │   │   │   └── OrderRepository.java
│   │   │   ├── model/entity/
│   │   │   │   └── Order.java
│   │   │   ├── dto/
│   │   │   │   ├── OrderRequest.java
│   │   │   │   └── OrderResponse.java
│   │   │   ├── client/              ← Feign clients (other services)
│   │   │   │   └── InventoryClient.java
│   │   │   ├── config/
│   │   │   │   └── AppConfig.java
│   │   │   └── exception/
│   │   │       ├── GlobalExceptionHandler.java
│   │   │       └── OrderNotFoundException.java
│   │   └── resources/
│   │       ├── application.yml
│   │       └── application-prod.yml
│   └── test/
├── Dockerfile
└── pom.xml
```

### ⚙️ application.yml for a Microservice
```yaml
server:
  port: 8081

spring:
  application:
    name: order-service          # VERY IMPORTANT — Eureka uses this name
  datasource:
    url: jdbc:mysql://localhost:3306/orderdb
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    instance-id: ${spring.application.name}:${server.port}

management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics,prometheus
  endpoint:
    health:
      show-details: always

logging:
  level:
    com.company: DEBUG
```

---

## 3. Service Discovery — Eureka

### 🗣️ Hinglish Explanation
Yaar socho — 10 microservices hain. Har service ko dusre service ka IP aur port pata hona chahiye na? Agar hardcode karein toh kya hoga — service ka IP badal gaya, sab crash. 

**Eureka** ek **telephone directory** jaisa hai. Har service apna naam aur address Eureka ko batati hai (register). Jab kisi service ko dusri service se baat karni ho, woh Eureka se puchti hai — "bhai, `inventory-service` kahan hai?" Eureka bata deta hai. Is concept ko **Service Registry & Discovery** kehte hain.

- **Eureka Server** = Directory/Registry
- **Eureka Client** = Har microservice jo register hoti hai

### 🏗️ Setup — Eureka Server

```xml
<!-- discovery-server/pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
</dependency>
```

```java
// EurekaServerApplication.java
@SpringBootApplication
@EnableEurekaServer  // ← This is ALL you need!
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

```yaml
# discovery-server/application.yml
server:
  port: 8761

spring:
  application:
    name: eureka-server

eureka:
  instance:
    hostname: localhost
  client:
    register-with-eureka: false   # Server itself doesn't register
    fetch-registry: false          # Server doesn't fetch from itself
  server:
    wait-time-in-ms-when-sync-empty: 0  # Fast startup in dev

# Access Eureka Dashboard: http://localhost:8761
```

### 🏗️ Setup — Eureka Client (Any Microservice)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

```java
// No @EnableEurekaClient needed in Spring Boot 3.x — auto-configured!
@SpringBootApplication
public class OrderServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(OrderServiceApplication.class, args);
    }
}
```

```yaml
# order-service/application.yml
spring:
  application:
    name: order-service   # This name is used for discovery

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
  instance:
    prefer-ip-address: true   # Use IP instead of hostname
    lease-renewal-interval-in-seconds: 30
    lease-expiration-duration-in-seconds: 90
```

### 🔄 How Discovery Works — Flow Diagram
```
[order-service starts]
        ↓
  Registers with Eureka
  (name: order-service, ip: 192.168.1.10, port: 8081)
        ↓
[order-service needs inventory-service]
        ↓
  Asks Eureka: "Where is inventory-service?"
        ↓
  Eureka returns: [192.168.1.11:8082, 192.168.1.12:8082]
        ↓
  LoadBalancer picks one → makes HTTP call
```

### 🔧 Eureka High Availability (Production Setup)

```yaml
# eureka-server-1/application.yml
spring:
  application:
    name: eureka-server
eureka:
  instance:
    hostname: eureka1
  client:
    service-url:
      defaultZone: http://eureka2:8762/eureka/   # Points to peer
    register-with-eureka: true   # Register with peer
    fetch-registry: true
```

```yaml
# eureka-server-2/application.yml
eureka:
  instance:
    hostname: eureka2
  client:
    service-url:
      defaultZone: http://eureka1:8761/eureka/
```

### 💡 Alternatives to Eureka

| Tool | Type | Best For |
|------|------|----------|
| **Eureka** (Netflix OSS) | Pull-based | Spring ecosystem |
| **Consul** (HashiCorp) | Push + Health checks | Multi-platform, K8s |
| **Zookeeper** | Coordination | Kafka ecosystems |
| **etcd** | Key-value | Kubernetes native |
| **Kubernetes Service** | DNS-based | K8s environments (no Eureka needed!) |

> **Interview Tip:** "In Kubernetes, we don't need Eureka because K8s provides built-in service discovery via DNS and Services. Eureka is more relevant for traditional VM/bare-metal deployments."

---

## 4. API Gateway — Spring Cloud Gateway

### 🗣️ Hinglish Explanation
Socho ek building ka main gate — andar jaane ke liye sabko wahan se guzarna padta hai. **API Gateway** wahi kaam karta hai microservices mein. Client directly kisi bhi service ko call nahi karta, pehle Gateway se request jaati hai. Gateway decide karta hai:
- Kahan bhejna hai (routing)
- Allowed hai ya nahi (authentication)
- Zyada requests toh rok do (rate limiting)
- Log karo sab kuch (logging)

Pehle **Zuul** (Netflix) tha, ab **Spring Cloud Gateway** use karte hain (better performance, reactive).

### 🏗️ Setup

```xml
<!-- api-gateway/pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
<!-- For security -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

```yaml
# api-gateway/application.yml
server:
  port: 8080

spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      routes:
        # Route 1: Order Service
        - id: order-service
          uri: lb://order-service    # lb:// = load balanced via Eureka
          predicates:
            - Path=/api/orders/**
          filters:
            - StripPrefix=1          # Strip /api prefix before forwarding
            - AddRequestHeader=X-Gateway-Source, api-gateway

        # Route 2: User Service  
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/users/**
            - Method=GET,POST         # Only GET and POST
          filters:
            - StripPrefix=1
            - name: CircuitBreaker    # Add circuit breaker
              args:
                name: user-service-cb
                fallbackUri: forward:/fallback/user

        # Route 3: Inventory Service with rate limiting
        - id: inventory-service
          uri: lb://inventory-service
          predicates:
            - Path=/api/inventory/**
          filters:
            - StripPrefix=1
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10   # 10 req/sec
                redis-rate-limiter.burstCapacity: 20
                redis-rate-limiter.requestedTokens: 1

      # Global filters applied to ALL routes
      default-filters:
        - DedupeResponseHeader=Access-Control-Allow-Origin
        - name: Retry
          args:
            retries: 3
            statuses: BAD_GATEWAY, SERVICE_UNAVAILABLE

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

### 🔧 Custom Global Filter (JWT Authentication)

```java
@Component
@Slf4j
public class AuthenticationFilter implements GlobalFilter, Ordered {

    @Autowired
    private JwtUtil jwtUtil;

    // Endpoints that don't need authentication
    private static final List<String> OPEN_ENDPOINTS = Arrays.asList(
        "/api/users/login",
        "/api/users/register",
        "/actuator/health"
    );

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        ServerHttpRequest request = exchange.getRequest();
        String path = request.getPath().toString();

        // Skip auth for open endpoints
        if (OPEN_ENDPOINTS.stream().anyMatch(path::contains)) {
            return chain.filter(exchange);
        }

        // Check for Authorization header
        if (!request.getHeaders().containsKey(HttpHeaders.AUTHORIZATION)) {
            return onError(exchange, "Missing Authorization header", HttpStatus.UNAUTHORIZED);
        }

        String authHeader = request.getHeaders().getFirst(HttpHeaders.AUTHORIZATION);
        if (authHeader == null || !authHeader.startsWith("Bearer ")) {
            return onError(exchange, "Invalid Authorization header", HttpStatus.UNAUTHORIZED);
        }

        String token = authHeader.substring(7);

        try {
            jwtUtil.validateToken(token);
            // Add user info to downstream headers
            String userId = jwtUtil.extractUserId(token);
            String roles = jwtUtil.extractRoles(token);

            ServerHttpRequest modifiedRequest = request.mutate()
                .header("X-User-Id", userId)
                .header("X-User-Roles", roles)
                .build();

            log.info("Request authenticated for user: {} path: {}", userId, path);
            return chain.filter(exchange.mutate().request(modifiedRequest).build());

        } catch (JwtException e) {
            log.error("JWT validation failed: {}", e.getMessage());
            return onError(exchange, "Invalid or expired token", HttpStatus.UNAUTHORIZED);
        }
    }

    private Mono<Void> onError(ServerWebExchange exchange, String message, HttpStatus status) {
        ServerHttpResponse response = exchange.getResponse();
        response.setStatusCode(status);
        response.getHeaders().setContentType(MediaType.APPLICATION_JSON);

        String body = String.format("{\"error\": \"%s\", \"status\": %d}", message, status.value());
        DataBuffer buffer = response.bufferFactory().wrap(body.getBytes(StandardCharsets.UTF_8));
        return response.writeWith(Mono.just(buffer));
    }

    @Override
    public int getOrder() {
        return -1;  // Run before other filters
    }
}
```

### 🔧 Fallback Controller

```java
@RestController
@RequestMapping("/fallback")
public class FallbackController {

    @GetMapping("/user")
    public ResponseEntity<Map<String, String>> userFallback() {
        Map<String, String> response = new HashMap<>();
        response.put("message", "User service is currently unavailable");
        response.put("status", "SERVICE_UNAVAILABLE");
        return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE).body(response);
    }

    @GetMapping("/order")
    public ResponseEntity<Map<String, String>> orderFallback() {
        return ResponseEntity.status(HttpStatus.SERVICE_UNAVAILABLE)
            .body(Map.of("message", "Order service is down. Please try later."));
    }
}
```

---

## 5. Load Balancing

### 🗣️ Hinglish Explanation
Ek service ke 3 instances chal rahe hain (3 copies). Client ko decide karna hai — kisko call karein. **Load Balancer** yeh kaam karta hai. Purana tha **Ribbon** (deprecated ab), ab Spring Boot 3.x mein **Spring Cloud LoadBalancer** use karte hain.

**Client-side load balancing** matlab client khud decide karta hai kaunsi instance ko call kare (Eureka se list fetch karke). Server-side load balancing mein ek alag server hota hai jo decide karta hai (like AWS ALB, Nginx).

### 🏗️ Spring Cloud LoadBalancer

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
</dependency>
```

```java
// Config class
@Configuration
public class WebClientConfig {

    @Bean
    @LoadBalanced   // Magic annotation — enables service name resolution via Eureka
    public WebClient.Builder webClientBuilder() {
        return WebClient.builder();
    }

    @Bean
    @LoadBalanced
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```

```java
// Usage in service
@Service
public class OrderService {

    @Autowired
    private WebClient.Builder webClientBuilder;

    public InventoryResponse checkInventory(String productId) {
        // "inventory-service" is the Eureka registered name — not a URL!
        return webClientBuilder.build()
            .get()
            .uri("http://inventory-service/api/inventory/{id}", productId)
            .retrieve()
            .bodyToMono(InventoryResponse.class)
            .block();
    }
}
```

### 🔧 Custom Load Balancing Strategy

```java
// Round Robin is default. For custom strategy:
@Configuration
public class CustomLoadBalancerConfig {

    @Bean
    public ReactorLoadBalancer<ServiceInstance> randomLoadBalancer(
            Environment environment,
            LoadBalancerClientFactory loadBalancerClientFactory) {
        String name = environment.getProperty(LoadBalancerClientFactory.PROPERTY_NAME);
        return new RandomLoadBalancer(
            loadBalancerClientFactory.getLazyProvider(name, ServiceInstanceListSupplier.class),
            name
        );
    }
}

// Apply to specific service
@LoadBalancerClient(name = "inventory-service", configuration = CustomLoadBalancerConfig.class)
@Configuration
public class InventoryServiceConfig {}
```

---

## 6. Inter-Service Communication

### 🗣️ Hinglish Explanation
Ek microservice ko dusri service se data lena hai. 2 tarike hain:
1. **Synchronous** — Request bhejo, response aane tak ruko (like phone call). RestTemplate, WebClient, **Feign Client** use karte hain.
2. **Asynchronous** — Message bhej do, response ki zaroorat nahi (like WhatsApp message). Kafka/RabbitMQ use karte hain.

**Feign Client** sabse easy hai — ek interface bana do, annotations lagao, Spring khud implement kar deta hai. Magic hai yaar!

### 🔧 Option 1: Feign Client (Recommended)

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

```java
// Enable Feign in main class
@SpringBootApplication
@EnableFeignClients
public class OrderServiceApplication {}
```

```java
// Define Feign Client interface — that's it!
@FeignClient(
    name = "inventory-service",    // Eureka service name
    path = "/api/inventory",
    fallback = InventoryClientFallback.class  // Circuit breaker fallback
)
public interface InventoryClient {

    @GetMapping("/check/{productId}")
    InventoryResponse checkAvailability(@PathVariable String productId);

    @PostMapping("/reduce")
    void reduceInventory(@RequestBody InventoryRequest request);

    @GetMapping("/products")
    List<ProductResponse> getAllProducts(
        @RequestParam int page,
        @RequestParam int size
    );
}

// Fallback implementation
@Component
public class InventoryClientFallback implements InventoryClient {

    @Override
    public InventoryResponse checkAvailability(String productId) {
        // Return cached/default response when inventory-service is down
        return InventoryResponse.builder()
            .productId(productId)
            .available(false)
            .message("Inventory service unavailable, cannot check stock")
            .build();
    }

    @Override
    public void reduceInventory(InventoryRequest request) {
        // Log and possibly publish to a retry queue
        log.error("Cannot reduce inventory — service down for: {}", request.getProductId());
        throw new ServiceUnavailableException("Inventory service is down");
    }
}
```

```yaml
# Enable Feign + Resilience4j
spring:
  cloud:
    openfeign:
      circuitbreaker:
        enabled: true

feign:
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 5000
        loggerLevel: FULL    # Log full request/response in DEBUG
      inventory-service:     # Service-specific config
        connectTimeout: 3000
        readTimeout: 3000
```

### 🔧 Option 2: WebClient (Reactive / Non-blocking)

```java
@Service
@Slf4j
public class OrderService {

    private final WebClient webClient;

    public OrderService(WebClient.Builder webClientBuilder) {
        this.webClient = webClientBuilder
            .baseUrl("http://inventory-service")
            .defaultHeader(HttpHeaders.CONTENT_TYPE, MediaType.APPLICATION_JSON_VALUE)
            .build();
    }

    // Synchronous call (blocks until response)
    public InventoryResponse checkInventorySync(String productId) {
        return webClient.get()
            .uri("/api/inventory/check/{id}", productId)
            .retrieve()
            .onStatus(HttpStatus::is4xxClientError, response ->
                Mono.error(new ProductNotFoundException("Product not found: " + productId)))
            .onStatus(HttpStatus::is5xxServerError, response ->
                Mono.error(new ServiceUnavailableException("Inventory service error")))
            .bodyToMono(InventoryResponse.class)
            .timeout(Duration.ofSeconds(3))
            .doOnError(e -> log.error("Error checking inventory: {}", e.getMessage()))
            .block();
    }

    // Async/Reactive call
    public Mono<InventoryResponse> checkInventoryAsync(String productId) {
        return webClient.get()
            .uri("/api/inventory/check/{id}", productId)
            .retrieve()
            .bodyToMono(InventoryResponse.class)
            .timeout(Duration.ofSeconds(3))
            .onErrorResume(e -> {
                log.error("Inventory check failed, using fallback: {}", e.getMessage());
                return Mono.just(InventoryResponse.unavailable(productId));
            });
    }

    // Parallel calls to multiple services
    public OrderDetailsResponse getOrderDetails(String orderId) {
        Mono<Order> orderMono = getOrderById(orderId);
        Mono<UserInfo> userMono = getUserInfo(orderId);
        Mono<PaymentInfo> paymentMono = getPaymentInfo(orderId);

        // All 3 calls happen in parallel!
        return Mono.zip(orderMono, userMono, paymentMono)
            .map(tuple -> OrderDetailsResponse.of(tuple.getT1(), tuple.getT2(), tuple.getT3()))
            .block();
    }
}
```

### 🔧 Option 3: RestTemplate (Older — avoid in new projects)

```java
@Service
public class OrderService {

    @Autowired
    @LoadBalanced   // Required for service name resolution
    private RestTemplate restTemplate;

    public InventoryResponse checkInventory(String productId) {
        String url = "http://inventory-service/api/inventory/check/" + productId;
        try {
            return restTemplate.getForObject(url, InventoryResponse.class);
        } catch (HttpClientErrorException e) {
            throw new ProductNotFoundException("Product " + productId + " not found");
        } catch (ResourceAccessException e) {
            throw new ServiceUnavailableException("Cannot connect to inventory service");
        }
    }

    public void postOrderToKitchen(KitchenOrder order) {
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_JSON);
        HttpEntity<KitchenOrder> request = new HttpEntity<>(order, headers);

        restTemplate.postForEntity(
            "http://kitchen-service/api/kitchen/orders",
            request,
            Void.class
        );
    }
}
```

---

## 7. Circuit Breaker — Resilience4j

### 🗣️ Hinglish Explanation
Imagine karo — `payment-service` down hai aur `order-service` baar baar usse call kar raha hai, timeouts le raha hai, threads block ho rahe hain, eventually `order-service` bhi crash ho jata hai. Yeh **cascading failure** hai.

**Circuit Breaker** ek electrical switch jaisa hai:
- **CLOSED** (normal) — requests pass ho rahe hain
- **OPEN** (failure detected) — requests rok liye, directly fallback
- **HALF-OPEN** (testing) — thodi requests jaane do check karne ke liye

Purana tha **Hystrix** (Netflix, deprecated), ab **Resilience4j** use karte hain.

### 🏗️ Setup

```xml
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

```yaml
# application.yml
resilience4j:
  circuitbreaker:
    instances:
      inventory-service:           # Circuit breaker name
        registerHealthIndicator: true
        slidingWindowSize: 10      # Last 10 calls considered
        minimumNumberOfCalls: 5    # Minimum calls before calculation
        permittedNumberOfCallsInHalfOpenState: 3
        automaticTransitionFromOpenToHalfOpenEnabled: true
        waitDurationInOpenState: 30s   # Wait 30s before half-open
        failureRateThreshold: 50       # 50% failures → OPEN
        eventConsumerBufferSize: 10
        slowCallRateThreshold: 100
        slowCallDurationThreshold: 2s  # Calls > 2s are "slow"

  retry:
    instances:
      inventory-service:
        maxAttempts: 3
        waitDuration: 500ms
        enableExponentialBackoff: true
        exponentialBackoffMultiplier: 2  # 500ms, 1000ms, 2000ms
        retryExceptions:
          - org.springframework.web.client.ResourceAccessException
          - java.net.ConnectException

  ratelimiter:
    instances:
      inventory-service:
        limitForPeriod: 100          # 100 calls per period
        limitRefreshPeriod: 1s       # per 1 second
        timeoutDuration: 0           # 0 = don't wait if limited

  bulkhead:
    instances:
      inventory-service:
        maxConcurrentCalls: 10       # Max 10 concurrent calls
        maxWaitDuration: 100ms
```

```java
@Service
@Slf4j
public class OrderService {

    @Autowired
    private InventoryClient inventoryClient;

    // Circuit Breaker + Retry + Fallback all together
    @CircuitBreaker(name = "inventory-service", fallbackMethod = "inventoryFallback")
    @Retry(name = "inventory-service")
    @TimeLimiter(name = "inventory-service")
    public CompletableFuture<InventoryResponse> checkInventory(String productId) {
        return CompletableFuture.supplyAsync(() ->
            inventoryClient.checkAvailability(productId)
        );
    }

    // Fallback method — signature must match + add Throwable param
    public CompletableFuture<InventoryResponse> inventoryFallback(
            String productId, Throwable throwable) {
        log.warn("Circuit breaker activated for product: {}. Error: {}",
            productId, throwable.getMessage());

        // Return cached/default response
        return CompletableFuture.completedFuture(
            InventoryResponse.builder()
                .productId(productId)
                .available(false)
                .message("Service temporarily unavailable. Assuming out of stock.")
                .build()
        );
    }

    // Bulkhead — limit concurrent executions
    @Bulkhead(name = "inventory-service", type = Bulkhead.Type.SEMAPHORE)
    public InventoryResponse checkInventoryWithBulkhead(String productId) {
        return inventoryClient.checkAvailability(productId);
    }

    // Rate Limiter
    @RateLimiter(name = "inventory-service", fallbackMethod = "rateLimitFallback")
    public InventoryResponse checkInventoryRateLimited(String productId) {
        return inventoryClient.checkAvailability(productId);
    }

    public InventoryResponse rateLimitFallback(String productId, RequestNotPermitted ex) {
        throw new TooManyRequestsException("Rate limit exceeded. Please retry after 1 second.");
    }
}
```

### 🔍 Monitoring Circuit Breaker State

```java
@RestController
@RequestMapping("/circuit-breaker")
public class CircuitBreakerController {

    @Autowired
    private CircuitBreakerRegistry circuitBreakerRegistry;

    @GetMapping("/status")
    public Map<String, String> getCircuitBreakerStatus() {
        return circuitBreakerRegistry.getAllCircuitBreakers()
            .stream()
            .collect(Collectors.toMap(
                CircuitBreaker::getName,
                cb -> cb.getState().toString()
            ));
    }

    @GetMapping("/metrics/{name}")
    public CircuitBreaker.Metrics getMetrics(@PathVariable String name) {
        return circuitBreakerRegistry.circuitBreaker(name).getMetrics();
    }
}
```

---

## 8. Config Server — Spring Cloud Config

### 🗣️ Hinglish Explanation
100 microservices hain. Sab ke alag alag `application.yml` hain. Database password change hua — 100 files update karni padein? Nahi yaar! **Config Server** ek centralized configuration store hai. Sab services wahan se config fetch karti hain. Config Git repo mein rakho — version control bhi ho gaya, audit bhi!

### 🏗️ Config Server Setup

```xml
<!-- config-server/pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

```java
@SpringBootApplication
@EnableConfigServer
public class ConfigServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerApplication.class, args);
    }
}
```

```yaml
# config-server/application.yml
server:
  port: 8888

spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/yourcompany/microservices-config   # Config Git repo
          default-label: main
          search-paths: '{application}'   # Each service has its own folder
          clone-on-start: true
          # For private repo:
          username: ${GIT_USERNAME}
          password: ${GIT_TOKEN}
          
        # Alternatively — local filesystem (for dev)
        # native:
        #   search-locations: classpath:/config

encrypt:
  key: ${ENCRYPT_KEY:mySecretKey}   # For encrypting sensitive properties
```

### 📁 Config Repository Structure

```
config-repo/
├── application.yml              # Global defaults for ALL services
├── application-dev.yml          # Dev profile defaults
├── application-prod.yml         # Prod profile defaults
├── order-service/
│   ├── order-service.yml        # order-service defaults
│   ├── order-service-dev.yml    # order-service dev config
│   └── order-service-prod.yml   # order-service prod config
├── user-service/
│   ├── user-service.yml
│   └── user-service-prod.yml
└── inventory-service/
    └── inventory-service.yml
```

```yaml
# config-repo/order-service/order-service.yml
spring:
  datasource:
    url: jdbc:mysql://mysql:3306/orderdb
    username: orderuser
    password: '{cipher}AQBVBMx8...'   # Encrypted password!

order:
  max-retry: 3
  payment-timeout: 30s
  kafka:
    topic: order-events
```

### 🔧 Client Configuration

```xml
<!-- Any microservice pom.xml -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

```yaml
# bootstrap.yml (or application.yml in Spring Boot 2.4+)
spring:
  application:
    name: order-service
  config:
    import: "configserver:http://localhost:8888"
  cloud:
    config:
      fail-fast: true              # Fail if config server not reachable
      retry:
        max-attempts: 5
        initial-interval: 1000
      profile: ${SPRING_PROFILES_ACTIVE:dev}
```

### 🔄 Refresh Configuration Without Restart

```java
// Add @RefreshScope to beans that should refresh
@RestController
@RefreshScope   // ← This bean will be recreated on /actuator/refresh
public class OrderController {

    @Value("${order.max-retry}")
    private int maxRetry;

    @GetMapping("/config-test")
    public String getConfig() {
        return "Max retry: " + maxRetry;
    }
}
```

```bash
# Trigger refresh manually
curl -X POST http://localhost:8081/actuator/refresh

# With Spring Cloud Bus (refresh ALL instances at once)
curl -X POST http://localhost:8081/actuator/busrefresh

# Config auto-refresh using webhooks from Git
# → Git push → GitHub webhook → Config server → Spring Cloud Bus → All services refresh
```

---

## 9. Distributed Tracing

### 🗣️ Hinglish Explanation
Ek user request aati hai — API Gateway → Order Service → Inventory Service → Payment Service. Kahin error aa gayi. Kahan hua? Logs sab jagah hain, kaise trace karein? 

**Distributed Tracing** mein har request ko ek unique **Trace ID** milta hai. Har service is ID ko aage pass karti hai. **Zipkin** ya **Jaeger** yeh sab traces collect karke ek visual timeline dikhate hain. Pehle **Spring Cloud Sleuth** tha, ab **Micrometer Tracing** use karte hain (Spring Boot 3.x).

### 🏗️ Setup — Zipkin Server

```bash
# Run Zipkin locally with Docker
docker run -d -p 9411:9411 openzipkin/zipkin

# Access: http://localhost:9411
```

### 🔧 Client Setup (Each Microservice)

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-brave</artifactId>
</dependency>
<dependency>
    <groupId>io.zipkin.reporter2</groupId>
    <artifactId>zipkin-reporter-brave</artifactId>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

```yaml
management:
  tracing:
    sampling:
      probability: 1.0   # 1.0 = 100% sampling (use 0.1 in prod = 10%)
  zipkin:
    tracing:
      endpoint: http://localhost:9411/api/v2/spans
  metrics:
    tags:
      application: ${spring.application.name}

logging:
  pattern:
    level: "%5p [${spring.application.name:},%X{traceId:-},%X{spanId:-}]"
    # Output: INFO [order-service,abc123,def456] Your log message
```

```java
@Service
@Slf4j
public class OrderService {

    @Autowired
    private Tracer tracer;  // Micrometer Tracer

    public Order placeOrder(OrderRequest request) {
        // Add custom span
        Span span = tracer.nextSpan().name("place-order").start();

        try (Tracer.SpanInScope ws = tracer.withSpan(span)) {
            // Add custom tags to span
            span.tag("order.customerId", request.getCustomerId());
            span.tag("order.productCount", String.valueOf(request.getItems().size()));

            log.info("Placing order for customer: {}", request.getCustomerId());
            // traceId automatically appears in log due to pattern config

            Order order = processOrder(request);

            span.tag("order.id", order.getId());
            return order;

        } catch (Exception e) {
            span.error(e);
            throw e;
        } finally {
            span.end();
        }
    }
}
```

### 💡 Alternatives

| Tool | Type | Notes |
|------|------|-------|
| **Zipkin** | Self-hosted | Simple, lightweight |
| **Jaeger** | Self-hosted | CNCF project, Kubernetes-native |
| **AWS X-Ray** | Cloud | For AWS deployments |
| **Dynatrace** | Commercial | Full observability suite |
| **Datadog APM** | Commercial | Industry standard |
| **OpenTelemetry** | Standard | Vendor-neutral, future-proof |

---

## 10. Message Queue — Kafka & RabbitMQ

### 🗣️ Hinglish Explanation
Synchronous communication mein agar ek service down hai toh kaam ruk jata hai. **Async messaging** mein service ek message queue mein daal deti hai aur apna kaam khalas. Baaki service jab ready ho, message uthao aur process karo.

**Kafka** = High-throughput, distributed log. Millions of messages/second. Event streaming ke liye best.
**RabbitMQ** = Traditional message broker. Complex routing, dead-letter queues. Simpler use cases.

**Interview mein bolo:** "We use Kafka for event-driven architecture where we need high throughput and replay capability. RabbitMQ for task queues where complex routing and acknowledgments are needed."

### 🔧 Apache Kafka Setup

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

```yaml
# application.yml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.springframework.kafka.support.serializer.JsonSerializer
      acks: all              # Wait for all replicas to acknowledge
      retries: 3
      properties:
        enable.idempotence: true      # Exactly-once semantics
        max.in.flight.requests.per.connection: 1
    consumer:
      group-id: order-service-group
      auto-offset-reset: earliest    # Start from beginning if no offset
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.springframework.kafka.support.serializer.JsonDeserializer
      properties:
        spring.json.trusted.packages: "com.company.events"
    listener:
      ack-mode: manual_immediate    # Manual acknowledgment
```

```java
// Event class
@Data
@AllArgsConstructor
@NoArgsConstructor
public class OrderPlacedEvent {
    private String orderId;
    private String customerId;
    private List<OrderItem> items;
    private BigDecimal totalAmount;
    private LocalDateTime timestamp;
}
```

```java
// PRODUCER — Order Service publishes events
@Service
@Slf4j
public class OrderEventPublisher {

    @Autowired
    private KafkaTemplate<String, Object> kafkaTemplate;

    private static final String TOPIC = "order-events";

    public void publishOrderPlaced(OrderPlacedEvent event) {
        kafkaTemplate.send(TOPIC, event.getOrderId(), event)
            .whenComplete((result, ex) -> {
                if (ex == null) {
                    log.info("Order event published successfully: orderId={}, partition={}, offset={}",
                        event.getOrderId(),
                        result.getRecordMetadata().partition(),
                        result.getRecordMetadata().offset());
                } else {
                    log.error("Failed to publish order event: {}", ex.getMessage());
                    // Handle failure — retry, DLQ, etc.
                }
            });
    }
}

// CONSUMER — Inventory Service consumes events
@Service
@Slf4j
public class InventoryEventConsumer {

    @KafkaListener(
        topics = "order-events",
        groupId = "inventory-service-group",
        containerFactory = "kafkaListenerContainerFactory"
    )
    public void handleOrderPlaced(
            @Payload OrderPlacedEvent event,
            @Header(KafkaHeaders.RECEIVED_TOPIC) String topic,
            @Header(KafkaHeaders.RECEIVED_PARTITION) int partition,
            @Header(KafkaHeaders.OFFSET) long offset,
            Acknowledgment acknowledgment) {

        log.info("Received order event: orderId={}, topic={}, partition={}, offset={}",
            event.getOrderId(), topic, partition, offset);

        try {
            // Process the event — reduce inventory
            inventoryService.reduceStock(event.getItems());

            // Manually acknowledge ONLY after successful processing
            acknowledgment.acknowledge();
            log.info("Order {} processed, inventory reduced", event.getOrderId());

        } catch (InsufficientStockException e) {
            log.error("Insufficient stock for order: {}", event.getOrderId());
            // Don't acknowledge — message will be redelivered or go to DLQ
            // Publish compensating event
            eventPublisher.publishOrderFailed(event.getOrderId(), "INSUFFICIENT_STOCK");
            acknowledgment.acknowledge();  // Acknowledge to prevent infinite loop
        }
    }

    // Dead Letter Queue consumer
    @KafkaListener(topics = "order-events.DLT", groupId = "inventory-dlt-group")
    public void handleDLT(OrderPlacedEvent event, Acknowledgment acknowledgment) {
        log.error("Processing DLT message for order: {}", event.getOrderId());
        // Alert, manual intervention, store to DB for investigation
        acknowledgment.acknowledge();
    }
}
```

```java
// Kafka Configuration with DLQ
@Configuration
public class KafkaConfig {

    @Bean
    public NewTopic orderEventsTopic() {
        return TopicBuilder.name("order-events")
            .partitions(3)        // Partition count = parallelism
            .replicas(2)          // Replication for fault tolerance
            .config(TopicConfig.RETENTION_MS_CONFIG, String.valueOf(7 * 24 * 60 * 60 * 1000L)) // 7 days
            .build();
    }

    @Bean
    public NewTopic orderEventsDLTopic() {
        return TopicBuilder.name("order-events.DLT").partitions(1).replicas(1).build();
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, Object> kafkaListenerContainerFactory(
            ConsumerFactory<String, Object> consumerFactory) {
        ConcurrentKafkaListenerContainerFactory<String, Object> factory =
            new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory);
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE);
        factory.setConcurrency(3);  // 3 threads = 3 partition consumers

        // Dead Letter Queue configuration
        DefaultErrorHandler errorHandler = new DefaultErrorHandler(
            new DeadLetterPublishingRecoverer(kafkaTemplate),
            new FixedBackOff(1000L, 3L)  // Retry 3 times with 1s interval
        );
        factory.setCommonErrorHandler(errorHandler);

        return factory;
    }
}
```

### 🔧 RabbitMQ Setup

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

```java
@Configuration
public class RabbitMQConfig {

    public static final String ORDER_QUEUE = "order-queue";
    public static final String ORDER_EXCHANGE = "order-exchange";
    public static final String ORDER_ROUTING_KEY = "order.placed";
    public static final String DLQ = "order-dlq";

    @Bean
    public Queue orderQueue() {
        return QueueBuilder.durable(ORDER_QUEUE)
            .withArgument("x-dead-letter-exchange", "")
            .withArgument("x-dead-letter-routing-key", DLQ)
            .withArgument("x-message-ttl", 30000)  // 30 second TTL
            .build();
    }

    @Bean
    public Queue deadLetterQueue() {
        return QueueBuilder.durable(DLQ).build();
    }

    @Bean
    public TopicExchange orderExchange() {
        return new TopicExchange(ORDER_EXCHANGE, true, false);
    }

    @Bean
    public Binding orderBinding() {
        return BindingBuilder.bind(orderQueue())
            .to(orderExchange())
            .with(ORDER_ROUTING_KEY);
    }

    @Bean
    public MessageConverter messageConverter() {
        return new Jackson2JsonMessageConverter();
    }
}
```

```java
// RabbitMQ Producer
@Service
public class RabbitOrderPublisher {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void publishOrder(OrderPlacedEvent event) {
        rabbitTemplate.convertAndSend(
            RabbitMQConfig.ORDER_EXCHANGE,
            RabbitMQConfig.ORDER_ROUTING_KEY,
            event
        );
    }
}

// RabbitMQ Consumer
@Service
public class RabbitOrderConsumer {

    @RabbitListener(queues = RabbitMQConfig.ORDER_QUEUE)
    public void handleOrder(OrderPlacedEvent event, Channel channel,
                            @Header(AmqpHeaders.DELIVERY_TAG) long deliveryTag) throws IOException {
        try {
            processOrder(event);
            channel.basicAck(deliveryTag, false);   // Acknowledge success
        } catch (Exception e) {
            channel.basicNack(deliveryTag, false, false);  // Reject, send to DLQ
        }
    }
}
```

---

## 11. Security — JWT + OAuth2 + Keycloak

### 🗣️ Hinglish Explanation
Microservices mein security complex hai. Pehle samajhte hain options:
- **JWT (JSON Web Token)** — stateless token, khud mein user info hoti hai. Service verify kare bina database ke.
- **OAuth2** — authorization framework. "Login with Google" wala concept.
- **Keycloak** — open-source Identity Provider (IdP). Ek hi jagah se users manage karo, sab services secure ho jaati hain. Production ka best choice!

### 🔧 JWT Authentication in API Gateway

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.12.3</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.12.3</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.12.3</version>
    <scope>runtime</scope>
</dependency>
```

```java
@Component
public class JwtUtil {

    @Value("${jwt.secret}")
    private String secret;

    @Value("${jwt.expiration:86400000}") // 24 hours default
    private long expirationMs;

    private SecretKey getSigningKey() {
        return Keys.hmacShaKeyFor(Decoders.BASE64.decode(secret));
    }

    public String generateToken(String userId, String email, List<String> roles) {
        Map<String, Object> claims = new HashMap<>();
        claims.put("userId", userId);
        claims.put("email", email);
        claims.put("roles", roles);

        return Jwts.builder()
            .claims(claims)
            .subject(email)
            .issuedAt(new Date())
            .expiration(new Date(System.currentTimeMillis() + expirationMs))
            .signWith(getSigningKey())
            .compact();
    }

    public Claims validateToken(String token) {
        return Jwts.parser()
            .verifyWith(getSigningKey())
            .build()
            .parseSignedClaims(token)
            .getPayload();
    }

    public String extractUserId(String token) {
        return validateToken(token).get("userId", String.class);
    }

    public List<String> extractRoles(String token) {
        return validateToken(token).get("roles", List.class);
    }
}
```

```java
// User Service — Login endpoint
@RestController
@RequestMapping("/api/users")
public class AuthController {

    @Autowired
    private UserService userService;

    @Autowired
    private JwtUtil jwtUtil;

    @PostMapping("/login")
    public ResponseEntity<AuthResponse> login(@RequestBody @Valid LoginRequest request) {
        User user = userService.authenticate(request.getEmail(), request.getPassword());

        String token = jwtUtil.generateToken(
            user.getId().toString(),
            user.getEmail(),
            user.getRoles().stream().map(Role::getName).collect(Collectors.toList())
        );

        return ResponseEntity.ok(AuthResponse.builder()
            .token(token)
            .userId(user.getId())
            .email(user.getEmail())
            .expiresIn(86400L)
            .build());
    }
}
```

### 🔧 Keycloak Integration (Production Recommended)

```yaml
# Any microservice — resource server config
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://keycloak:8080/realms/myapp
          jwk-set-uri: http://keycloak:8080/realms/myapp/protocol/openid-connect/certs
```

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable())
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/actuator/health").permitAll()
                .requestMatchers(HttpMethod.GET, "/api/products/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(jwt -> jwt.jwtAuthenticationConverter(jwtAuthConverter()))
            );
        return http.build();
    }

    @Bean
    public JwtAuthenticationConverter jwtAuthConverter() {
        JwtGrantedAuthoritiesConverter converter = new JwtGrantedAuthoritiesConverter();
        converter.setAuthoritiesClaimName("roles");   // Keycloak uses 'roles' claim
        converter.setAuthorityPrefix("ROLE_");

        JwtAuthenticationConverter jwtConverter = new JwtAuthenticationConverter();
        jwtConverter.setJwtGrantedAuthoritiesConverter(converter);
        return jwtConverter;
    }
}

// Use @PreAuthorize at method level
@Service
public class OrderService {

    @PreAuthorize("hasRole('USER') or hasRole('ADMIN')")
    public Order placeOrder(OrderRequest request) { ... }

    @PreAuthorize("hasRole('ADMIN')")
    public void cancelOrder(String orderId) { ... }

    @PostAuthorize("returnObject.customerId == authentication.name")
    public Order getOrder(String orderId) { ... }  // Only own orders
}
```

---

## 12. Containerization — Docker + Docker Compose

### 🗣️ Hinglish Explanation
"Mere machine par toh chal raha tha!" — yeh problem Docker se khatam! Docker mein application aur uski dependencies ek saath package ho jaati hain (container). **Dockerfile** = recipe, **Image** = bana hua dish, **Container** = running dish.

### 🔧 Multi-Stage Dockerfile

```dockerfile
# Dockerfile — Order Service
# Stage 1: Build
FROM eclipse-temurin:17-jdk-alpine AS builder
WORKDIR /app
COPY pom.xml .
COPY src ./src

# Download dependencies separately (better caching)
RUN ./mvnw dependency:go-offline -B

# Build the application
RUN ./mvnw package -DskipTests

# Extract layers for better Docker caching
RUN java -Djarmode=layertools -jar target/*.jar extract

# Stage 2: Runtime (smaller image)
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app

# Create non-root user for security
RUN addgroup -S spring && adduser -S spring -G spring
USER spring:spring

# Copy layers in order (rarely changing → frequently changing)
COPY --from=builder /app/dependencies/ ./
COPY --from=builder /app/spring-boot-loader/ ./
COPY --from=builder /app/snapshot-dependencies/ ./
COPY --from=builder /app/application/ ./

EXPOSE 8081

HEALTHCHECK --interval=30s --timeout=3s --retries=3 \
    CMD wget -qO- http://localhost:8081/actuator/health | grep UP || exit 1

ENTRYPOINT ["java", \
    "-XX:+UseContainerSupport", \
    "-XX:MaxRAMPercentage=75.0", \
    "-Djava.security.egd=file:/dev/./urandom", \
    "org.springframework.boot.loader.JarLauncher"]
```

### 🔧 Docker Compose — Complete Stack

```yaml
# docker-compose.yml
version: '3.8'

services:
  # Infrastructure
  mysql:
    image: mysql:8.0
    container_name: mysql
    environment:
      MYSQL_ROOT_PASSWORD: rootpassword
      MYSQL_DATABASE: microservicesdb
    ports:
      - "3306:3306"
    volumes:
      - mysql_data:/var/lib/mysql
      - ./init-scripts:/docker-entrypoint-initdb.d
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 5

  zookeeper:
    image: confluentinc/cp-zookeeper:7.5.0
    container_name: zookeeper
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    container_name: kafka
    depends_on: [zookeeper]
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
      KAFKA_AUTO_CREATE_TOPICS_ENABLE: true

  redis:
    image: redis:7-alpine
    container_name: redis
    ports:
      - "6379:6379"

  zipkin:
    image: openzipkin/zipkin
    container_name: zipkin
    ports:
      - "9411:9411"

  # Spring Cloud Infrastructure
  eureka-server:
    build: ./eureka-server
    container_name: eureka-server
    ports:
      - "8761:8761"
    environment:
      SPRING_PROFILES_ACTIVE: docker
    healthcheck:
      test: ["CMD", "wget", "-qO-", "http://localhost:8761/actuator/health"]
      interval: 30s
      timeout: 10s
      retries: 5

  config-server:
    build: ./config-server
    container_name: config-server
    ports:
      - "8888:8888"
    depends_on:
      eureka-server:
        condition: service_healthy
    environment:
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eureka-server:8761/eureka/

  api-gateway:
    build: ./api-gateway
    container_name: api-gateway
    ports:
      - "8080:8080"
    depends_on:
      - eureka-server
      - config-server
    environment:
      SPRING_PROFILES_ACTIVE: docker
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eureka-server:8761/eureka/
      SPRING_CONFIG_IMPORT: configserver:http://config-server:8888

  # Microservices
  order-service:
    build: ./order-service
    container_name: order-service
    ports:
      - "8081:8081"
    depends_on:
      mysql:
        condition: service_healthy
      kafka:
        condition: service_started
      eureka-server:
        condition: service_healthy
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/orderdb
      SPRING_DATASOURCE_USERNAME: root
      SPRING_DATASOURCE_PASSWORD: rootpassword
      SPRING_KAFKA_BOOTSTRAP_SERVERS: kafka:9092
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eureka-server:8761/eureka/
      MANAGEMENT_ZIPKIN_TRACING_ENDPOINT: http://zipkin:9411/api/v2/spans

  user-service:
    build: ./user-service
    ports:
      - "8082:8082"
    depends_on:
      mysql:
        condition: service_healthy
      eureka-server:
        condition: service_healthy
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/userdb
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eureka-server:8761/eureka/

  inventory-service:
    build: ./inventory-service
    ports:
      - "8083:8083"
    depends_on:
      - mysql
      - eureka-server
    environment:
      EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: http://eureka-server:8761/eureka/

  # Scale a service: docker-compose up --scale order-service=3
  
volumes:
  mysql_data:
```

---

## 13. Orchestration — Kubernetes

### 🗣️ Hinglish Explanation
Docker Compose ek machine ke liye theek hai. Production mein 100 instances, multiple machines — **Kubernetes (K8s)** kaam aata hai. K8s ek **orchestrator** hai — tumhe bolo "mujhe 3 copies of order-service chahiye", K8s khud manage karta hai — container crash hua toh restart, machine khatam toh dusri machine pe move.

### 🔧 Kubernetes Manifests

```yaml
# order-service-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: order-service
  namespace: microservices
  labels:
    app: order-service
    version: v1.0.0
spec:
  replicas: 3
  selector:
    matchLabels:
      app: order-service
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0       # Zero downtime deployment!
  template:
    metadata:
      labels:
        app: order-service
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "8081"
        prometheus.io/path: "/actuator/prometheus"
    spec:
      containers:
        - name: order-service
          image: yourregistry/order-service:1.0.0
          ports:
            - containerPort: 8081
          env:
            - name: SPRING_PROFILES_ACTIVE
              value: "kubernetes"
            - name: DB_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: db-credentials
                  key: password
          envFrom:
            - configMapRef:
                name: order-service-config
          resources:
            requests:
              memory: "256Mi"
              cpu: "250m"
            limits:
              memory: "512Mi"
              cpu: "500m"
          livenessProbe:
            httpGet:
              path: /actuator/health/liveness
              port: 8081
            initialDelaySeconds: 60
            periodSeconds: 10
            failureThreshold: 3
          readinessProbe:
            httpGet:
              path: /actuator/health/readiness
              port: 8081
            initialDelaySeconds: 30
            periodSeconds: 5

---
apiVersion: v1
kind: Service
metadata:
  name: order-service
  namespace: microservices
spec:
  selector:
    app: order-service
  ports:
    - port: 8081
      targetPort: 8081
  type: ClusterIP    # Internal only; use LoadBalancer for external

---
# Horizontal Pod Autoscaler
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: order-service-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: order-service
  minReplicas: 2
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 70    # Scale up when CPU > 70%
    - type: Resource
      resource:
        name: memory
        target:
          type: Utilization
          averageUtilization: 80
```

```yaml
# ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: order-service-config
data:
  EUREKA_CLIENT_SERVICEURL_DEFAULTZONE: "http://eureka-server:8761/eureka/"
  SPRING_KAFKA_BOOTSTRAP_SERVERS: "kafka:9092"

---
# Secret (base64 encoded)
apiVersion: v1
kind: Secret
metadata:
  name: db-credentials
type: Opaque
data:
  password: cGFzc3dvcmQ=    # base64 of "password"
  username: cm9vdA==         # base64 of "root"
```

```bash
# Useful K8s commands
kubectl apply -f k8s/                           # Apply all manifests
kubectl get pods -n microservices               # List pods
kubectl logs -f order-service-abc123 -n ms      # Tail logs
kubectl describe pod order-service-abc123       # Debug pod issues
kubectl exec -it order-service-abc123 -- sh    # Shell into pod
kubectl scale deployment order-service --replicas=5   # Scale manually
kubectl rollout status deployment/order-service        # Check rollout
kubectl rollout undo deployment/order-service          # Rollback!
kubectl top pods                                        # CPU/memory usage
```

> **K8s vs Eureka:** In Kubernetes, use **K8s Service** for service discovery (DNS-based). Eureka is NOT needed. Spring Boot apps use `http://service-name:port` directly since K8s DNS resolves it.

---

## 14. Service Mesh — Istio

### 🗣️ Hinglish Explanation
K8s ke saath kaam karta hai Istio. Har pod ke saath ek **sidecar proxy** (Envoy) inject ho jaata hai. Yeh proxy handle karta hai:
- mTLS (mutual TLS) — services ke beech encrypted communication
- Traffic management — canary deployments, A/B testing
- Observability — automatic tracing, metrics bina code change ke

```yaml
# Istio Traffic Management — Canary Deployment
apiVersion: networking.istio.io/v1alpha3
kind: VirtualService
metadata:
  name: order-service
spec:
  http:
    - match:
        - headers:
            user-type:
              exact: beta-tester      # Beta users get v2
      route:
        - destination:
            host: order-service
            subset: v2
    - route:
        - destination:
            host: order-service
            subset: v1
          weight: 90         # 90% traffic to v1
        - destination:
            host: order-service
            subset: v2
          weight: 10         # 10% to v2 (canary)
```

---

## 15. Distributed Database Patterns

### 🗣️ Hinglish Explanation
Microservices mein **database per service** pattern follow karte hain. Problem: transaction across services kaise manage karein? Bank mein paise transfer — Order service ne order create kiya, Payment service ne paise kat liye, Inventory service ne stock ghata liya. Payment fail hua toh sab rollback kaise?

**SAGA Pattern** answer hai — compensating transactions.

### 🔧 SAGA Pattern — Choreography Based

```java
// Order Service — publishes events, doesn't orchestrate
@Service
public class OrderService {

    @Transactional
    public Order placeOrder(OrderRequest request) {
        // Step 1: Create order in PENDING state
        Order order = Order.builder()
            .customerId(request.getCustomerId())
            .status(OrderStatus.PENDING)
            .items(request.getItems())
            .build();
        order = orderRepository.save(order);

        // Step 2: Publish event — others will react
        eventPublisher.publish(new OrderCreatedEvent(order.getId(), order.getItems()));

        return order;
    }

    // Listens for payment success
    @KafkaListener(topics = "payment-events")
    public void handlePaymentEvent(PaymentEvent event) {
        if (event.getType() == PaymentEventType.SUCCESS) {
            orderRepository.updateStatus(event.getOrderId(), OrderStatus.CONFIRMED);
            eventPublisher.publish(new OrderConfirmedEvent(event.getOrderId()));
        } else {
            // Payment failed — compensate (cancel order)
            orderRepository.updateStatus(event.getOrderId(), OrderStatus.CANCELLED);
            eventPublisher.publish(new OrderCancelledEvent(event.getOrderId(), "PAYMENT_FAILED"));
        }
    }

    // Listens for inventory failure
    @KafkaListener(topics = "inventory-events")
    public void handleInventoryEvent(InventoryEvent event) {
        if (event.getType() == InventoryEventType.INSUFFICIENT_STOCK) {
            orderRepository.updateStatus(event.getOrderId(), OrderStatus.CANCELLED);
            // Also trigger refund if payment already done
            eventPublisher.publish(new RefundRequestedEvent(event.getOrderId()));
        }
    }
}
```

### 🔧 CQRS Pattern

```java
// COMMAND side — writes
@Service
public class OrderCommandService {

    @Autowired
    private OrderRepository orderRepository;  // Write DB (MySQL)

    @Autowired
    private EventBus eventBus;

    @Transactional
    public String placeOrder(PlaceOrderCommand command) {
        Order order = new Order(command);
        orderRepository.save(order);

        // Publish event for read model sync
        eventBus.publish(new OrderCreatedEvent(order));
        return order.getId();
    }
}

// QUERY side — reads (from separate read DB/cache)
@Service
public class OrderQueryService {

    @Autowired
    private OrderReadRepository readRepository;  // Read DB / Elasticsearch / Redis

    public List<OrderSummary> getOrdersByCustomer(String customerId) {
        return readRepository.findByCustomerId(customerId);
    }

    // Event handler — keeps read model in sync
    @EventListener
    public void on(OrderCreatedEvent event) {
        OrderSummary summary = OrderSummary.from(event);
        readRepository.save(summary);
    }
}
```

### 🔧 Outbox Pattern (Reliable Event Publishing)

```java
// Solve: Transaction committed but Kafka message failed — inconsistency!
@Service
public class OrderService {

    @Transactional
    public Order placeOrder(OrderRequest request) {
        Order order = orderRepository.save(new Order(request));

        // Save event to OUTBOX TABLE in same transaction — atomic!
        OutboxEvent outboxEvent = OutboxEvent.builder()
            .aggregateId(order.getId())
            .eventType("OrderCreated")
            .payload(objectMapper.writeValueAsString(new OrderCreatedEvent(order)))
            .status(OutboxStatus.PENDING)
            .build();
        outboxRepository.save(outboxEvent);

        // No Kafka publish here!
        return order;
    }
}

// Separate poller publishes outbox events to Kafka
@Component
@Scheduled(fixedDelay = 1000)
public class OutboxPoller {

    @Transactional
    public void publishPendingEvents() {
        List<OutboxEvent> pending = outboxRepository.findByStatus(OutboxStatus.PENDING);
        for (OutboxEvent event : pending) {
            try {
                kafkaTemplate.send("order-events", event.getPayload());
                event.setStatus(OutboxStatus.PUBLISHED);
                outboxRepository.save(event);
            } catch (Exception e) {
                log.error("Failed to publish outbox event: {}", event.getId());
            }
        }
    }
}
```

---

## 16. API Documentation

### 🔧 Springdoc OpenAPI (Swagger 3)

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>2.3.0</version>
</dependency>
```

```java
@RestController
@RequestMapping("/api/orders")
@Tag(name = "Order Management", description = "APIs for managing orders")
public class OrderController {

    @Operation(
        summary = "Place a new order",
        description = "Creates a new order with the provided items",
        responses = {
            @ApiResponse(responseCode = "201", description = "Order created successfully",
                content = @Content(schema = @Schema(implementation = OrderResponse.class))),
            @ApiResponse(responseCode = "400", description = "Invalid request"),
            @ApiResponse(responseCode = "503", description = "Inventory service unavailable")
        }
    )
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public OrderResponse placeOrder(
            @RequestBody @Valid @io.swagger.v3.oas.annotations.parameters.RequestBody(
                description = "Order details", required = true)
            OrderRequest request,
            @Parameter(hidden = true) @RequestHeader("X-User-Id") String userId) {
        return orderService.placeOrder(request, userId);
    }
}

// Swagger access: http://localhost:8081/swagger-ui.html
// OpenAPI JSON: http://localhost:8081/v3/api-docs
```

---

## 17. Monitoring — Prometheus + Grafana + ELK

### 🗣️ Hinglish Explanation
Production mein bina monitoring ke andhe ho. **Metrics** (numbers — requests/sec, latency), **Logs** (text — what happened), **Traces** (flow — kahan se kahan gaya). Ye teen cheezein milke **Observability** banati hain.

- **Prometheus** — metrics collect karta hai (scrapes /actuator/prometheus every 15s)
- **Grafana** — metrics visualize karta hai (dashboards)
- **ELK Stack** — Elasticsearch + Logstash + Kibana = log management

### 🔧 Custom Metrics

```java
@Service
public class OrderService {

    private final Counter orderCounter;
    private final Timer orderProcessingTimer;
    private final Gauge activeOrdersGauge;

    public OrderService(MeterRegistry meterRegistry) {
        this.orderCounter = Counter.builder("orders.placed.total")
            .description("Total orders placed")
            .tag("service", "order-service")
            .register(meterRegistry);

        this.orderProcessingTimer = Timer.builder("orders.processing.time")
            .description("Time to process an order")
            .register(meterRegistry);

        this.activeOrdersGauge = Gauge.builder("orders.active", this, service ->
                orderRepository.countByStatus(OrderStatus.PENDING))
            .description("Currently active orders")
            .register(meterRegistry);
    }

    public Order placeOrder(OrderRequest request) {
        return orderProcessingTimer.record(() -> {
            Order order = processOrder(request);
            orderCounter.increment(1,
                Tags.of("status", "success", "customerId", request.getCustomerId()));
            return order;
        });
    }
}
```

```yaml
# prometheus.yml
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'order-service'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['order-service:8081']

  - job_name: 'user-service'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['user-service:8082']
```

---

## 18. Testing Microservices

### 🗣️ Hinglish Explanation
Microservices testing ka **Testing Pyramid**:
- **Unit Tests** (base — most) — single method/class
- **Integration Tests** — service + DB, service + Kafka
- **Contract Tests** — service A aur B ke beech contract verify
- **E2E Tests** (top — least) — poora flow

### 🔧 Unit Test

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private InventoryClient inventoryClient;

    @Mock
    private KafkaTemplate<String, Object> kafkaTemplate;

    @InjectMocks
    private OrderServiceImpl orderService;

    @Test
    @DisplayName("Should place order when inventory is available")
    void placeOrder_WhenInventoryAvailable_ShouldCreateOrder() {
        // Arrange
        OrderRequest request = OrderRequest.builder()
            .customerId("customer-1")
            .items(List.of(new OrderItem("product-1", 2)))
            .build();

        when(inventoryClient.checkAvailability("product-1"))
            .thenReturn(InventoryResponse.available("product-1", 10));
        when(orderRepository.save(any(Order.class)))
            .thenAnswer(invocation -> {
                Order order = invocation.getArgument(0);
                order.setId("order-1");
                return order;
            });

        // Act
        Order result = orderService.placeOrder(request);

        // Assert
        assertThat(result.getId()).isEqualTo("order-1");
        assertThat(result.getStatus()).isEqualTo(OrderStatus.PENDING);
        verify(orderRepository, times(1)).save(any(Order.class));
        verify(kafkaTemplate, times(1)).send(eq("order-events"), any(), any());
    }

    @Test
    @DisplayName("Should throw exception when inventory insufficient")
    void placeOrder_WhenInventoryInsufficient_ShouldThrow() {
        when(inventoryClient.checkAvailability(any()))
            .thenReturn(InventoryResponse.unavailable("product-1"));

        assertThatThrownBy(() -> orderService.placeOrder(createRequest()))
            .isInstanceOf(InsufficientStockException.class)
            .hasMessageContaining("product-1");
    }
}
```

### 🔧 Integration Test with Testcontainers

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@Testcontainers
class OrderIntegrationTest {

    @Container
    static MySQLContainer<?> mysql = new MySQLContainer<>("mysql:8.0")
        .withDatabaseName("testdb")
        .withUsername("test")
        .withPassword("test");

    @Container
    static KafkaContainer kafka = new KafkaContainer(
        DockerImageName.parse("confluentinc/cp-kafka:7.5.0")
    );

    @DynamicPropertySource
    static void configureProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", mysql::getJdbcUrl);
        registry.add("spring.datasource.username", mysql::getUsername);
        registry.add("spring.datasource.password", mysql::getPassword);
        registry.add("spring.kafka.bootstrap-servers", kafka::getBootstrapServers);
    }

    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void placeOrder_ShouldCreateOrderAndPublishEvent() {
        // Full integration test with real DB and Kafka!
        OrderRequest request = buildOrderRequest();

        ResponseEntity<OrderResponse> response = restTemplate.postForEntity(
            "/api/orders", request, OrderResponse.class
        );

        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.CREATED);
        assertThat(response.getBody().getOrderId()).isNotNull();
    }
}
```

### 🔧 Contract Testing with Spring Cloud Contract

```groovy
// contracts/order-service/place_order.groovy (in producer)
Contract.make {
    description "Should create order when valid request"
    request {
        method POST()
        url '/api/orders'
        body(customerId: "cust-1", items: [[productId: "prod-1", quantity: 2]])
        headers { contentType(applicationJson()) }
    }
    response {
        status 201
        body(orderId: anyNonEmptyString(), status: "PENDING")
    }
}
```

---

## 19. CI/CD Pipeline

### 🔧 GitHub Actions Pipeline

```yaml
# .github/workflows/microservice-ci-cd.yml
name: Microservice CI/CD

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Set up JDK 17
        uses: actions/setup-java@v4
        with:
          java-version: '17'
          distribution: 'temurin'
          cache: maven

      - name: Run tests
        run: mvn test -pl order-service

      - name: Generate coverage report
        run: mvn jacoco:report -pl order-service

      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3

  build-and-push:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v4

      - name: Log in to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Build and push Docker image
        uses: docker/build-push-action@v5
        with:
          context: ./order-service
          push: true
          tags: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/order-service:${{ github.sha }}
          cache-from: type=gha
          cache-to: type=gha,mode=max

  deploy:
    needs: build-and-push
    runs-on: ubuntu-latest
    environment: production
    steps:
      - name: Deploy to Kubernetes
        uses: azure/k8s-deploy@v4
        with:
          manifests: k8s/
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}/order-service:${{ github.sha }}
```

---

## 20. Real World Project Structure

### 🏗️ Complete E-Commerce Microservices Architecture

```
ecommerce-microservices/
├── infrastructure/
│   ├── eureka-server/                 # Service Discovery
│   ├── config-server/                 # Centralized Config
│   ├── api-gateway/                   # Entry point
│   └── kafka-setup/                   # Kafka topics config
│
├── services/
│   ├── user-service/                  # User registration, auth (port: 8082)
│   ├── product-service/               # Product catalog (port: 8083)
│   ├── inventory-service/             # Stock management (port: 8084)
│   ├── order-service/                 # Order placement (port: 8085)
│   ├── payment-service/               # Payment processing (port: 8086)
│   ├── notification-service/          # Email/SMS notifications (port: 8087)
│   └── review-service/                # Product reviews (port: 8088)
│
├── shared/
│   ├── common-dto/                    # Shared DTOs/events
│   └── common-utils/                  # Shared utilities
│
├── docker-compose.yml
├── docker-compose-infrastructure.yml
└── k8s/
    ├── namespace.yaml
    ├── configmaps/
    ├── secrets/
    └── services/
```

### 🔄 Complete Request Flow

```
Client Request: "Place Order"
        ↓
API Gateway (8080) — JWT validation, rate limiting
        ↓
Order Service (8085)
    ├── Validates request
    ├── Calls Inventory Service (Feign) — check stock
    ├── Creates Order (PENDING) in MySQL
    ├── Saves to Outbox table
    └── Publishes OrderCreatedEvent → Kafka
        ↓
Payment Service (Kafka consumer)
    ├── Processes payment
    └── Publishes PaymentSuccessEvent → Kafka
        ↓
Order Service (Kafka consumer)
    └── Updates Order status → CONFIRMED
        ↓
Inventory Service (Kafka consumer)
    └── Reduces stock
        ↓
Notification Service (Kafka consumer)
    └── Sends confirmation email/SMS
```

---

## 21. Master Interview Q&A

### 🔴 Tier 1 — Must Know (3yr experience)

**Q1: What is the difference between Monolith and Microservices?**

**A:** Monolith is a single deployable unit with all functionality bundled together. Any change requires redeploying the entire application. Microservices decompose the application into small, independently deployable services, each owning its data and having a single business responsibility. Microservices enable independent scaling, technology diversity, and team autonomy, but introduce distributed system challenges like network latency, data consistency, and operational complexity.

---

**Q2: How do microservices communicate with each other?**

**A:** Two patterns:
- **Synchronous**: REST (Feign Client, WebClient), gRPC. Used when immediate response is needed.
- **Asynchronous**: Message queues like Kafka, RabbitMQ. Used for event-driven flows, decoupling, and resilience.

In practice, I use Feign Client for synchronous calls (it integrates with Eureka for load-balanced service discovery) and Kafka for async event publishing — for example, after placing an order, we publish an `OrderCreatedEvent` to Kafka, and payment/inventory/notification services react to it.

---

**Q3: What is Service Discovery and how does Eureka work?**

**A:** Service Discovery solves the problem of dynamic service locations in cloud environments where IP addresses change. **Eureka** uses a client-side discovery pattern:
1. Each service (client) registers itself with the Eureka Server on startup, sending heartbeats every 30 seconds
2. When Service A needs to call Service B, it queries Eureka for B's instances
3. A client-side load balancer (Spring Cloud LoadBalancer) picks one instance
4. Direct HTTP call is made

In Kubernetes environments, Eureka is replaced by K8s built-in DNS-based service discovery.

---

**Q4: What is a Circuit Breaker? Explain states.**

**A:** A Circuit Breaker prevents cascading failures in distributed systems. It wraps calls to external services and monitors failure rates.

Three states:
- **CLOSED**: Normal operation, requests flow through. Failure rate tracked.
- **OPEN**: Failure threshold exceeded. All requests fail immediately with fallback response (no calls to the failing service). Reduces load on failing service.
- **HALF-OPEN**: After a configured wait duration, a limited number of test requests are allowed through. If they succeed, circuit transitions to CLOSED; if they fail, back to OPEN.

I use **Resilience4j** (replaced deprecated Hystrix). Configuring `failureRateThreshold: 50%`, `waitDurationInOpenState: 30s`, and always providing a meaningful fallback response.

---

**Q5: How do you handle distributed transactions in microservices?**

**A:** ACID transactions spanning multiple services are not possible. We use the **SAGA pattern**:

- **Choreography**: Services publish events and react to others' events. No central coordinator. Better for simple flows.
- **Orchestration**: A central saga orchestrator tells each service what to do and handles compensations.

For failures, we define **compensating transactions** — if payment fails after order creation, we publish an `OrderCancellationEvent`.

I also use the **Outbox Pattern** to ensure reliable event publishing: instead of publishing directly to Kafka (which can fail after DB commit), I save events to an outbox table in the same transaction, then a separate poller publishes them to Kafka.

---

**Q6: What is API Gateway? Why is it needed?**

**A:** API Gateway is the single entry point for all client requests. It handles:
- **Routing** — forwards requests to appropriate services
- **Authentication** — validates JWT tokens once, propagates user info via headers
- **Rate Limiting** — prevents abuse
- **Load Balancing** — distributes requests
- **SSL Termination** — handles HTTPS at gateway level
- **Request/Response transformation** — modify headers, body

I use **Spring Cloud Gateway** (reactive, non-blocking) over the older Zuul. Without a gateway, clients would need to know all service addresses and handle auth separately.

---

**Q7: What is the difference between Kafka and RabbitMQ?**

**A:**

| | Kafka | RabbitMQ |
|---|---|---|
| Model | Distributed log, pull-based | Message broker, push-based |
| Throughput | Millions/sec | ~50K/sec |
| Message retention | Configurable (hours/days) | Deleted after consumption |
| Consumer groups | Multiple consumers, each gets all messages | Competing consumers share messages |
| Use case | Event streaming, audit log, event sourcing | Task queues, RPC, complex routing |
| Replay | Yes (seek to offset) | No (once consumed, gone) |

For microservices event-driven architecture, I prefer Kafka for its replay capability and high throughput.

---

**Q8: How do you secure microservices?**

**A:** Multi-layered security:
1. **External communication**: API Gateway validates JWT tokens. Only authenticated requests reach services.
2. **Service-to-service**: In Kubernetes with Istio, mutual TLS (mTLS) is enforced automatically. Without Istio, we propagate JWT tokens and each service validates them.
3. **OAuth2/OIDC with Keycloak**: For production, use Keycloak as Identity Provider. Services are configured as OAuth2 resource servers that validate tokens against Keycloak's JWKS endpoint.
4. **HTTPS everywhere**: TLS in transit.
5. **Secrets management**: Kubernetes Secrets or HashiCorp Vault — never hardcode credentials.

---

**Q9: What is the Database per Service pattern? Challenges?**

**A:** Each microservice owns its database exclusively — no other service can access it directly. This ensures loose coupling and independent scaling.

**Challenges:**
- **No joins** across services — must use API composition or CQRS
- **Distributed transactions** — use Saga pattern
- **Data consistency** — eventual consistency instead of strong consistency
- **Data duplication** — some data is duplicated across services

This is a trade-off we accept for better service autonomy.

---

**Q10: How do you implement health checks?**

**A:** Spring Boot Actuator provides:
- `/actuator/health` — overall health (used by K8s liveness/readiness probes)
- `/actuator/health/liveness` — is the app alive (restart if failing)
- `/actuator/health/readiness` — is the app ready to receive traffic

Custom health indicator:
```java
@Component
public class KafkaHealthIndicator extends AbstractHealthIndicator {
    @Override
    protected void doHealthCheck(Health.Builder builder) throws Exception {
        kafkaTemplate.send("health-check-topic", "ping").get(3, TimeUnit.SECONDS);
        builder.up().withDetail("kafka", "Connected");
    }
}
```

---

### 🔵 Tier 2 — Good to Know

**Q11: What is CQRS? When to use it?**

**A:** Command Query Responsibility Segregation separates write operations (Commands) from read operations (Queries) using different models and often different databases. Commands update state and publish events; those events update the read model optimized for queries. Use when read and write loads are very different, or when complex queries would hurt write performance.

---

**Q12: Explain Event Sourcing.**

**A:** Instead of storing current state, Event Sourcing stores the sequence of events that led to the current state. To get current state, replay all events. Benefits: complete audit trail, ability to replay events, temporal queries. Challenges: complexity, eventual consistency, query difficulty (usually combined with CQRS for read projections).

---

**Q13: How do you handle service versioning in microservices?**

**A:** Several strategies:
- **URI versioning**: `/api/v1/orders`, `/api/v2/orders` (most common, visible)
- **Header versioning**: `Accept: application/vnd.company.v2+json`
- **Query param**: `/api/orders?version=2`

For backward compatibility, I always maintain the previous version for at least 1-2 release cycles, documenting breaking changes clearly in OpenAPI spec.

---

**Q14: What is Bulkhead pattern?**

**A:** Isolates failures in one part of the system from others — like ship bulkheads that prevent flooding from sinking the entire ship. In microservices, we limit the number of concurrent calls to a specific downstream service. Even if inventory-service is slow, it won't exhaust all threads and bring down order-service. Implemented with Resilience4j's `@Bulkhead` annotation with a semaphore or thread pool.

---

**Q15: How do you do Zero Downtime Deployment?**

**A:** Several strategies:
- **Rolling Update** (K8s default): Gradually replace old pods with new ones. Requires backward compatibility.
- **Blue-Green**: Run two identical environments; switch traffic from Blue to Green instantaneously. Easy rollback.
- **Canary**: Route small % of traffic (e.g., 5%) to new version; monitor metrics; gradually increase.

In Kubernetes, I configure `maxUnavailable: 0` and `maxSurge: 1` in the deployment rolling update strategy for zero downtime.

---

**Q16: What is Spring Cloud Bus?**

**A:** Spring Cloud Bus links distributed system nodes together using a message broker (Kafka or RabbitMQ). Primary use: propagate state changes like configuration refreshes across all service instances. Instead of calling `/actuator/refresh` on every pod, call `/actuator/busrefresh` on one — it broadcasts the refresh event to all service instances via the message bus.

---

**Q17: gRPC vs REST for microservices?**

**A:**
- **REST**: Human-readable JSON, broad tool support, stateless. Best for external APIs.
- **gRPC**: Binary protobuf (10x smaller, 5x faster), strongly typed contracts, streaming support, auto-generated clients. Best for internal service-to-service calls where performance matters.

I use REST for external-facing APIs (easier to consume) and gRPC for internal high-frequency service calls like between order-service and inventory-service.

---

**Q18: What is a Sidecar pattern?**

**A:** Deploy a helper container alongside the main application container in the same pod (Kubernetes) or same host (VMs). The sidecar handles cross-cutting concerns like logging (Fluentd), service mesh proxy (Envoy/Istio), configuration refresh, or secret injection — without changing the main application code. Istio uses this pattern — injects Envoy proxy as sidecar to handle mTLS, tracing, and traffic management transparently.

---

**Q19: How would you debug a slow API in microservices?**

**A:** Systematic approach:
1. **Distributed traces in Zipkin/Jaeger** — identify which service/span is slow
2. **Service metrics in Grafana** — check response time histograms, error rates per service
3. **Application logs** — correlate by trace ID across services
4. **Database query analysis** — EXPLAIN queries, missing indexes
5. **Thread dump / heap dump** — if JVM-level issue
6. **Network latency** — check if inter-service calls have high latency
7. **Circuit breaker state** — check if any service is in OPEN state causing cascading waits

I always start with distributed traces to narrow down the problem to a specific service before diving deep.

---

**Q20: What technologies can be used to build microservices? (Full Stack)**

**A:** The ecosystem is wide:

| Category | Options |
|----------|---------|
| **Frameworks** | Spring Boot, Quarkus, Micronaut, Helidon, Node.js/Express |
| **Service Discovery** | Eureka, Consul, etcd, K8s DNS |
| **API Gateway** | Spring Cloud Gateway, Kong, AWS API GW, Nginx, Traefik |
| **Config** | Spring Cloud Config, Consul KV, AWS Parameter Store |
| **Messaging** | Kafka, RabbitMQ, ActiveMQ, AWS SQS/SNS, Pulsar |
| **Tracing** | Zipkin, Jaeger, AWS X-Ray, Datadog, Dynatrace |
| **Monitoring** | Prometheus + Grafana, ELK, Splunk, Datadog |
| **Circuit Breaker** | Resilience4j, Istio (sidecar) |
| **Security** | Keycloak, Auth0, Okta, AWS Cognito |
| **Containers** | Docker, Podman |
| **Orchestration** | Kubernetes, AWS ECS, Docker Swarm |
| **Service Mesh** | Istio, Linkerd, Consul Connect |
| **CI/CD** | GitHub Actions, Jenkins, GitLab CI, ArgoCD |
| **Database** | MySQL, PostgreSQL, MongoDB, Redis, Cassandra |

---

### 🟡 Common Gotchas / Tricky Questions

**Q: N+1 problem in microservices?**

**A:** When fetching an order with 10 items and making a separate service call for each item. Solution: Batch API call — send all product IDs in one request and get all products back. Or use GraphQL for flexible data fetching.

---

**Q: How to prevent data inconsistency in Saga pattern?**

**A:** Use **idempotency** — ensure each step can be retried safely. Use unique idempotency keys. Use the **Outbox pattern** to ensure event publishing is atomic with the DB transaction. Implement compensating transactions for rollback scenarios.

---

**Q: Why not use 2PC (Two-Phase Commit) in microservices?**

**A:** 2PC requires all participants to be available during the commit phase (blocking protocol), causing high latency and single point of failure. In microservices with potentially hundreds of services, the probability of partial failure is high. Saga pattern with eventual consistency is preferred — it's non-blocking and handles failures gracefully.

---

## 📚 Quick Reference — Technologies Summary

```
COMMUNICATION
├── Sync:  Feign Client → REST → WebClient → gRPC
└── Async: Kafka (streaming) → RabbitMQ (routing) → AWS SQS

SERVICE MESH
├── Eureka     → Traditional VM/bare metal
├── Consul     → Multi-platform, health checks
└── K8s DNS    → Kubernetes environments (no Eureka needed)

RESILIENCE
├── Circuit Breaker: Resilience4j (OPEN/CLOSED/HALF-OPEN)
├── Retry:           Resilience4j @Retry with backoff
├── Rate Limiter:    Resilience4j / API Gateway
└── Bulkhead:        Thread pool / Semaphore isolation

OBSERVABILITY
├── Metrics:  Prometheus + Grafana
├── Logs:     ELK Stack / Splunk
└── Traces:   Zipkin / Jaeger (via Micrometer Tracing)

SECURITY
├── External: JWT at API Gateway
├── Internal: mTLS (Istio) or JWT propagation
└── IdP:      Keycloak / Auth0 / Okta

DEPLOYMENT
├── Dev:   Docker Compose
├── Staging: Kubernetes (minikube/kind)
└── Prod:  Kubernetes (EKS/GKE/AKS) + Helm charts
```

---

## 🎯 3-Year Experience Talking Points

1. **Architecture decisions**: "We chose choreography-based Saga because our domain boundaries were clear and we wanted loose coupling. The tradeoff was harder debugging."

2. **Real problems solved**: "We had cascading failures between order and payment service. Implemented Circuit Breaker with Resilience4j — reduced error rate from 40% to 0.1% during downstream outages."

3. **Scale**: "We scaled inventory-service independently during sale events — went from 2 to 20 instances via Kubernetes HPA, with zero code changes."

4. **Trade-offs**: "We moved from synchronous Feign calls to Kafka events for non-critical flows (notifications, analytics). Improved API response time by 300ms."

5. **Observability**: "Implemented distributed tracing with Micrometer + Zipkin. Reduced MTTR (Mean Time to Resolve) from 2 hours to 15 minutes for production issues."

---

*Notes compiled from: Spring Cloud Official Docs, Microservices.io patterns, Martin Fowler's blog, Netflix Tech Blog, Chris Richardson's patterns, and hands-on production experience.*

*Last updated: 2024 | Spring Boot 3.x | Spring Cloud 2023.x*
