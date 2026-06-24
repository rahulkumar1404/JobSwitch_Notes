# Java Full Stack Interview Preparation Guide
### Rahul Kumar — 20 LPA Target | 2-Hour Interview | 3 Years Experience Level

> **How to use this guide:** Every answer is written as if you are speaking to the interviewer directly. Read it, internalize it, and practice saying it out loud. The goal is zero hesitation and zero follow-up questions from the interviewer.

---

## Table of Contents

1. [Java Core](#1-java-core)
2. [Spring Boot](#2-spring-boot)
3. [Microservices & System Design](#3-microservices--system-design)
4. [Apache Kafka](#4-apache-kafka)
5. [React & Frontend](#5-react--frontend)
6. [AWS & DevOps](#6-aws--devops)
7. [Databases & SQL](#7-databases--sql)
8. [System Design Deep Dives](#8-system-design-deep-dives)
9. [Hands-On Coding Questions](#9-hands-on-coding-questions)
10. [Security — OAuth2, JWT, Keycloak](#10-security--oauth2-jwt-keycloak)
11. [HR & Behavioral](#11-hr--behavioral)

---

## 1. Java Core

---

### Q1. What is the difference between JDK, JRE, and JVM?

**Model Answer:**

JVM — Java Virtual Machine — is the runtime engine that executes compiled Java bytecode. It handles memory management, garbage collection, and platform abstraction. JRE — Java Runtime Environment — is JVM plus the standard class libraries needed to run a Java application. JDK — Java Development Kit — is JRE plus the compiler `javac`, debugger, and other development tools needed to write and build Java programs.

In production deployments, you only need JRE — or in modern containerized setups, just the JVM. In development and CI/CD pipelines, you need JDK. Since Java 11, the distinction is somewhat blurred because Oracle no longer ships a standalone JRE, but the conceptual difference remains important for interviews and architecture discussions.

---

### Q2. Explain the four OOP pillars with real examples.

**Model Answer:**

**Encapsulation** means hiding the internal state of an object and exposing only what is necessary through a controlled interface. In my billing system at Cognizant, the `Invoice` class keeps its `totalAmount` field private. External classes cannot modify it directly — they go through `addLineItem()` which applies business rules like tax calculation before updating the total. This prevents invalid states.

**Inheritance** allows a class to reuse behavior from a parent. In my contact management system, I had a base `User` class with `name`, `email`, and `authenticate()`. Then `AdminUser extends User` added `manageUsers()` without duplicating the common fields.

**Polymorphism** means one interface, multiple behaviors. I have a `NotificationService` interface with a `send()` method. `EmailNotificationService` and `SMSNotificationService` both implement it differently. My code depends only on the interface — at runtime, the correct implementation is injected by Spring, so adding a new channel requires zero changes to existing code.

**Abstraction** means exposing what an object does without revealing how it does it. My `PaymentProcessor` interface exposes `processPayment(PaymentRequest)` — callers don't know whether it talks to a payment gateway, writes to a queue, or does both. The implementation is hidden behind the abstraction.

---

### Q3. Difference between `==` and `.equals()`. When does `==` give wrong results?

**Model Answer:**

`==` compares references — it checks whether two variables point to the exact same object in memory. `.equals()` compares content — it checks whether two objects are logically equivalent based on the contract defined in the class.

The classic trap is with `String`. Java interns string literals, so `"hello" == "hello"` is actually `true` because both point to the same pool entry. But `new String("hello") == new String("hello")` is `false` because `new` always creates a new heap object. This is why you should always use `.equals()` for object comparisons.

The same issue applies to `Integer`. Java caches `Integer` values from -128 to 127, so `Integer a = 127; Integer b = 127; a == b` is `true`. But `Integer a = 128; Integer b = 128; a == b` is `false` because 128 is outside the cache range and two separate objects are created. This is a common source of subtle production bugs.

Rule of thumb: use `==` only for primitives and `null` checks. Use `.equals()` for all object comparisons. Use `Objects.equals(a, b)` when either operand might be null, because it handles null safely.

---

### Q4. What is autoboxing/unboxing? Where can it cause NullPointerException?

**Model Answer:**

Autoboxing is Java's automatic conversion of a primitive like `int` to its wrapper class `Integer`. Unboxing is the reverse. The compiler inserts the conversion code transparently, so `Integer x = 5` compiles to `Integer x = Integer.valueOf(5)`.

The NPE trap happens during unboxing when the wrapper is null. For example:

```java
Integer value = null; // comes from a DB column that returned NULL
int result = value;   // compiler unboxes: value.intValue() → NullPointerException
```

This is especially dangerous when you receive Integer values from a database via JPA, a map lookup that returns null, or an Optional that isn't properly handled. I always check for null before assigning a wrapper type to a primitive.

Another performance pitfall: autoboxing inside a tight loop creates thousands of short-lived objects. `for (int i = 0; i < 1000000; i++) { sum += list.get(i); }` where list is `List<Integer>` unboxes each element — much slower than a primitive array.

---

### Q5. Explain `final`, `finally`, `finalize` — are they related?

**Model Answer:**

They share the same root word but are completely unrelated concepts.

`final` is a modifier. On a variable, it means the reference cannot be reassigned — but if it's an object, the object's internal state can still change. On a method, it means the method cannot be overridden in a subclass. On a class, it means the class cannot be extended — `String` is final for this reason.

`finally` is a block in exception handling. Code inside `finally` always executes whether or not an exception was thrown or caught. It is used to release resources — closing file handles, database connections, releasing locks. With modern Java, `try-with-resources` is preferred over explicit `finally` for resource management.

`finalize` is a deprecated method in `Object` that the garbage collector was supposed to call before reclaiming an object. In practice it was unreliable — no guarantee when or if it would be called, caused GC pauses, and could even resurrect objects accidentally. It has been removed in Java 18. Use `Cleaner` or `AutoCloseable` for deterministic cleanup.

---

### Q6. Explain Java Memory Model: Stack vs Heap vs MetaSpace.

**Model Answer:**

The JVM memory is divided into several regions, each with a distinct purpose.

**Stack** is thread-local memory. Each thread has its own stack. Every method call creates a stack frame containing local variables, method parameters, and the return address. When the method returns, its frame is popped. Stack memory is fast, automatically managed, and never shared between threads, so it has no concurrency issues. Stack overflow happens when there are too many nested calls — infinite recursion is the classic cause.

**Heap** is the shared memory region where all objects live. It is divided into Young Generation (Eden + Survivor spaces) and Old Generation. New objects are allocated in Eden. Minor GC runs frequently in Young Gen — most short-lived objects die here. Objects that survive several GC cycles are promoted to Old Gen. Major/Full GC runs on Old Gen and is more expensive. Tuning -Xms and -Xmx controls the initial and maximum heap size.

**MetaSpace** (replaced PermGen in Java 8) stores class metadata — class structures, method bytecode, field definitions. Unlike PermGen, MetaSpace grows dynamically from native memory. OOM errors in MetaSpace happen when you load too many classes — common with dynamic class generation (like CGLIB proxies in Spring) or classloader leaks in hot-redeploy scenarios.

In a production Spring Boot app, I monitor heap via Actuator's `/actuator/metrics/jvm.memory.used` and alert when Old Gen usage exceeds 80%.

---

### Q7. How does HashMap work internally? Explain rehashing and load factor.

**Model Answer:**

HashMap internally is an array of buckets. Each bucket is a linked list (in Java 8+, a red-black tree when a bucket has more than 8 entries). When you call `put(key, value)`:

1. `key.hashCode()` is called and then the hash is spread further with a secondary hash to reduce clustering.
2. The bucket index is computed as `hash & (capacity - 1)` — bitwise AND with capacity minus one, which works because capacity is always a power of two.
3. If the bucket is empty, the entry is placed there. If there is a collision, the new entry is appended to the linked list (or inserted into the tree if the bucket is large). `equals()` is used to find exact matches.

**Load factor** is the threshold at which the map resizes. Default is 0.75, meaning when 75% of the buckets are occupied, the map doubles its capacity and rehashes all existing entries — every entry's bucket index is recomputed. Rehashing is O(n) but amortized over many insertions, so overall put is O(1) amortized.

**Why this matters in production:** If you know the map will hold 10,000 entries, initialize it as `new HashMap<>(16384, 0.75f)` — 10000 / 0.75 ≈ 13333, rounded up to the next power of two = 16384. This eliminates all rehashing entirely.

**Key contract:** If you override `equals()`, you must override `hashCode()`. Two equal objects must have the same hash code, otherwise the map breaks — you'd put an entry and then never find it again.

---

### Q8. Difference between ArrayList and LinkedList — when do you choose which?

**Model Answer:**

ArrayList is backed by a dynamic array. Random access by index is O(1) because it's just a pointer offset. Insertion or deletion in the middle is O(n) because all subsequent elements must be shifted.

LinkedList is a doubly linked list. Insertion or deletion at any known node is O(1). But random access is O(n) because you must traverse from the head. Also, each node stores two extra pointers (previous and next), making memory overhead significantly higher.

In practice, I almost always choose ArrayList. CPU cache efficiency matters enormously at runtime — ArrayList elements are contiguous in memory, so iterating a large ArrayList is fast due to cache line prefetching. LinkedList elements are scattered in memory, causing cache misses.

The only time I'd consider LinkedList is when I need a `Deque` (double-ended queue) with frequent head and tail insertions — even then, `ArrayDeque` usually outperforms `LinkedList` due to the same cache efficiency argument.

At my level, the interviewer wants to hear that I understand the memory layout implications, not just the textbook O(n) vs O(1).

---

### Q9. Explain ConcurrentHashMap vs synchronizedMap vs Hashtable.

**Model Answer:**

`Hashtable` is legacy — every method is `synchronized` on the entire object, meaning only one thread can access any method at a time. Complete lock contention.

`Collections.synchronizedMap()` wraps any map and synchronizes every method on a single mutex — same bottleneck as Hashtable, just applied dynamically. Iteration is not thread-safe without external synchronization.

`ConcurrentHashMap` is designed for high concurrency. In Java 7, it used segment-level locking — 16 segments by default, so 16 threads could write simultaneously. In Java 8+, it uses `synchronized` blocks at the individual bucket level plus CAS (Compare-And-Swap) operations for lock-free reads. This means reads are almost always non-blocking and writes contend only when two threads hit the exact same bucket.

The practical difference: under high concurrent load, `ConcurrentHashMap` throughput scales almost linearly with thread count. `synchronizedMap` degrades to serial execution.

Important nuance: `ConcurrentHashMap` does not allow null keys or null values — it throws `NullPointerException`. This is intentional: in a concurrent context, a null return from `get()` would be ambiguous — does it mean the key is absent, or that the value is null?

---

### Q10. What are Java Generics? Explain type erasure and the PECS rule.

**Model Answer:**

Generics provide compile-time type safety. Without generics, a `List` holds `Object` and you'd need to cast every element — a potential `ClassCastException` at runtime. With `List<String>`, the compiler guarantees only Strings are added and removes the need for explicit casts.

**Type erasure** means that generic type information is erased at compile time and is not available at runtime. `List<String>` and `List<Integer>` are both just `List` at the bytecode level. This is why you cannot do `instanceof List<String>` or `new T[]` — T is not known at runtime. It is also why you sometimes see `@SuppressWarnings("unchecked")` in framework code that uses reflection.

**PECS — Producer Extends, Consumer Super** — is the rule for using bounded wildcards correctly:

- If a collection is a producer of elements (you read from it), use `? extends T`. Example: `void process(List<? extends Animal> animals)` — you can read Animals but cannot add to the list, because you don't know the exact subtype.
- If a collection is a consumer of elements (you write to it), use `? super T`. Example: `void fill(List<? super Dog> list)` — you can add Dogs, but you cannot safely read typed elements.

In my fitness tracker I used this when building a generic event publisher that accepts `List<? extends DomainEvent>` — any subtype of DomainEvent can be passed in.

---

### Q11. Explain Java 8 Stream API — write a complete pipeline.

**Model Answer:**

The Stream API provides a functional, declarative way to process collections. Streams are lazy — intermediate operations like `map`, `filter`, and `flatMap` are not executed until a terminal operation like `collect`, `reduce`, or `forEach` is called. This allows the JVM to optimize the pipeline and short-circuit where possible.

**Key operations:**

- `filter(Predicate)` — keeps elements matching the predicate
- `map(Function)` — transforms each element
- `flatMap(Function)` — transforms and flattens nested structures
- `sorted(Comparator)` — sorts elements
- `distinct()` — removes duplicates
- `limit(n)` — takes first n elements
- `reduce(identity, BinaryOperator)` — aggregates to a single value
- `collect(Collector)` — materializes into a collection

**Example from my billing work:**

```java
// Given a list of invoices, find the top 3 unpaid invoices above 10,000
// grouped by customer, returning a map of customerId → total unpaid amount

Map<String, Double> top3UnpaidByCustomer = invoices.stream()
    .filter(inv -> !inv.isPaid() && inv.getAmount() > 10_000)
    .sorted(Comparator.comparingDouble(Invoice::getAmount).reversed())
    .limit(3)
    .collect(Collectors.groupingBy(
        Invoice::getCustomerId,
        Collectors.summingDouble(Invoice::getAmount)
    ));
```

`flatMap` example — flattening orders with multiple items:

```java
List<String> allProductNames = orders.stream()
    .flatMap(order -> order.getItems().stream())
    .map(Item::getProductName)
    .distinct()
    .collect(Collectors.toList());
```

**Parallel streams:** `stream().parallel()` splits the source across ForkJoinPool threads. Useful only for CPU-intensive operations on large datasets (100k+ elements) where the operation is stateless and the source splits cheaply. For I/O-bound work, `CompletableFuture` is better.

---

### Q12. What are functional interfaces? Write a custom one.

**Model Answer:**

A functional interface has exactly one abstract method. It can have default and static methods. The `@FunctionalInterface` annotation is optional but good practice — it causes a compile error if you accidentally add a second abstract method.

The four built-in functional interfaces cover almost all use cases:
- `Function<T, R>` — takes T, returns R
- `Predicate<T>` — takes T, returns boolean
- `Supplier<T>` — takes nothing, returns T
- `Consumer<T>` — takes T, returns nothing

**Custom example from my AI fitness tracker:**

```java
@FunctionalInterface
public interface ActivityScorer {
    double score(Activity activity);

    // Default method — allowed in functional interfaces
    default ActivityScorer andThen(ActivityScorer after) {
        return activity -> this.score(activity) + after.score(activity);
    }
}

// Usage with lambda
ActivityScorer intensityScorer = activity -> activity.getHeartRate() * 0.4;
ActivityScorer durationScorer = activity -> activity.getDurationMinutes() * 0.6;
ActivityScorer combined = intensityScorer.andThen(durationScorer);

double totalScore = combined.score(myActivity);
```

Method references are a concise lambda syntax: `String::toUpperCase` instead of `s -> s.toUpperCase()`. You can reference static methods, instance methods, and constructors.

---

### Q13. Explain CompletableFuture — how is it different from Future?

**Model Answer:**

`Future` is blocking. `future.get()` blocks the calling thread until the result is ready. You cannot compose multiple Futures or attach callbacks without blocking.

`CompletableFuture` is non-blocking and composable. You can chain operations that execute asynchronously on a thread pool without ever blocking.

**Key methods:**
- `supplyAsync(Supplier, executor)` — starts an async computation
- `thenApply(Function)` — transforms the result synchronously on the completion thread
- `thenApplyAsync(Function)` — transforms on a different thread
- `thenCompose(Function)` — flat-map for CompletableFuture (avoids `CompletableFuture<CompletableFuture<T>>`)
- `thenCombine(other, BiFunction)` — wait for two futures and combine results
- `exceptionally(Function)` — handle errors in the chain
- `allOf(futures...)` — wait for all to complete

**Real example from my fitness tracker's AI service:**

```java
// Activity is saved, then AI recommendation is fetched asynchronously
public CompletableFuture<AIRecommendation> processActivityAsync(Activity activity) {
    return CompletableFuture
        .supplyAsync(() -> activityRepository.save(activity), taskExecutor)
        .thenCompose(saved -> 
            CompletableFuture.supplyAsync(() -> 
                geminiClient.getRecommendation(saved), aiExecutor))
        .thenApply(recommendation -> {
            mongoRepository.save(recommendation);
            return recommendation;
        })
        .exceptionally(ex -> {
            log.error("AI processing failed for activity {}", activity.getId(), ex);
            return AIRecommendation.defaultRecommendation();
        });
}
```

This entire chain is non-blocking. The HTTP request thread returns immediately after triggering `supplyAsync`. In Java 21 with virtual threads, the advantage is even more pronounced.

---

### Q14. Explain synchronized, ReentrantLock, and volatile.

**Model Answer:**

**`volatile`** guarantees visibility — a write to a volatile variable is immediately visible to all other threads. It does not guarantee atomicity. `volatile boolean stopFlag = false` is correct for a stop signal because the write is a single operation. But `volatile int counter; counter++` is broken — `counter++` is read-modify-write, three operations, and another thread can interleave between them.

**`synchronized`** provides mutual exclusion via the object's monitor lock. Only one thread can execute a synchronized block on the same monitor at a time. It also provides a happens-before guarantee — everything done inside a synchronized block before releasing the lock is visible to the next thread that acquires it. Drawback: you cannot interrupt a waiting thread, you cannot try to acquire with a timeout.

**`ReentrantLock`** is an explicit lock with more flexibility:
- `tryLock(timeout, unit)` — acquire with timeout, avoid deadlock
- `lockInterruptibly()` — can be interrupted while waiting
- Fairness mode: `new ReentrantLock(true)` ensures FIFO ordering, prevents thread starvation
- `newCondition()` — multiple condition variables on one lock (more flexible than `wait()/notify()`)

```java
ReentrantLock lock = new ReentrantLock();
if (lock.tryLock(100, TimeUnit.MILLISECONDS)) {
    try {
        // critical section
    } finally {
        lock.unlock(); // ALWAYS in finally
    }
} else {
    // could not acquire, handle gracefully
}
```

The `finally` block is mandatory with `ReentrantLock` — `synchronized` releases the lock automatically on exception, but `ReentrantLock.unlock()` must be called manually.

---

### Q15. Explain Java GC — G1 vs ZGC vs Parallel GC. How do you tune for production?

**Model Answer:**

**Parallel GC** (throughput collector) uses multiple threads for both minor and major GC, but pauses the application completely during collection. Best for batch jobs where you want maximum throughput and can tolerate occasional long pauses.

**G1 GC** (Garbage First, default since Java 9) divides the heap into equal-sized regions and prioritizes collecting the regions with the most garbage first. It aims for predictable pause times — you set a soft target via `-XX:MaxGCPauseMillis=200`. It performs concurrent marking (while the application runs) and only stops briefly for evacuation. Good balance of throughput and latency for most production services.

**ZGC** (Java 15+, production-ready) performs almost all work concurrently with the application. Pause times are sub-millisecond regardless of heap size — even 1TB heaps pause for < 1ms. Trade-off: slightly lower throughput than G1 because of the concurrent work overhead. Best for latency-sensitive services where GC pauses cause SLA violations.

**Production tuning I apply:**

```bash
# For a typical Spring Boot microservice
-Xms2g -Xmx2g           # Same min/max prevents dynamic resizing overhead
-XX:+UseG1GC
-XX:MaxGCPauseMillis=200
-XX:G1HeapRegionSize=16m
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/var/log/app/heapdump.hprof
-Xlog:gc*:file=/var/log/app/gc.log:time,uptime:filecount=5,filesize=20m
```

For Kubernetes: set JVM heap to ~70% of container memory limit, and set `-XX:MaxRAMPercentage=70.0` instead of fixed `-Xmx` so the JVM respects container limits automatically.

---

### Q16. How do you diagnose a memory leak in a Spring Boot production app?

**Model Answer:**

I follow a systematic process. First, I confirm the symptom — heap usage grows steadily over time and does not drop after GC cycles. Old Gen occupancy keeps climbing. This is visible in `/actuator/metrics/jvm.memory.used?tag=area:heap` or CloudWatch.

**Step 1: Capture a heap dump.** Either let `-XX:+HeapDumpOnOutOfMemoryError` capture one on OOM, or trigger it manually via `jmap -dump:format=b,file=heapdump.hprof <pid>`, or via `/actuator/heapdump`.

**Step 2: Analyze with Eclipse MAT (Memory Analyzer Tool).** Open the heap dump, run the Leak Suspects report. Look at the Dominator Tree — it shows which objects are retaining the most memory. Usually one or two object types dominate.

**Common culprits I've seen:**
- **Static collections** — `static Map<> cache` that grows unboundedly because items are never removed.
- **ThreadLocal leaks** — In a thread pool, `ThreadLocal` values survive if not explicitly `remove()`d. Spring's `RequestContextHolder` uses `ThreadLocal` — always clean up in filters.
- **Hibernate first-level cache** in long-running transactions — loading thousands of entities in a loop inside one transaction keeps all of them in the session cache.
- **Spring beans holding references** — an @ApplicationScope bean holds a reference to a request-scoped object. The request is gone but the object cannot be GC'd.
- **Unclosed connections** — HikariCP connection pool exhausted because connections are not returned (missing `finally` block or try-with-resources).

**Step 3: Reproduce in staging.** Add the fix, run a load test, verify heap stabilizes.

---

### Q17. What are Java virtual threads (Project Loom, Java 21)?

**Model Answer:**

Virtual threads are JVM-managed lightweight threads, not OS threads. A traditional OS thread requires ~1MB of stack memory and is expensive to create and context-switch. Virtual threads require only a few KB and can be created by the millions.

The key mechanism is that when a virtual thread makes a blocking call (reading from a socket, waiting for a database result), the JVM automatically unmounts the virtual thread from its carrier OS thread. The OS thread becomes free to run other virtual threads. When the blocking operation completes, the virtual thread is remounted — possibly on a different carrier thread.

**Why this matters for microservices:** In a traditional thread-per-request model, if your thread blocks waiting for a database query for 50ms, that OS thread sits idle. With virtual threads, that 50ms of blocking frees the carrier thread to handle 50ms worth of other requests. Throughput scales massively without changing your synchronous blocking code.

**Before virtual threads:**
```java
// Traditional: 1 OS thread per request → limits throughput to ~1000 concurrent
Executors.newFixedThreadPool(200)
```

**With virtual threads (Java 21):**
```java
// Millions of concurrent virtual threads, same blocking code style
Executors.newVirtualThreadPerTaskExecutor()
```

In Spring Boot 3.2+, you enable virtual threads with a single property: `spring.threads.virtual.enabled=true`. This upgrades Tomcat's request handling to virtual threads automatically.

I used this in my fitness tracker project with Java 21 — it simplified my async code because I could write blocking I/O calls naturally without needing `CompletableFuture` chains everywhere.

---

## 2. Spring Boot

---

### Q18. What is Spring IoC? Difference between @Component, @Service, @Repository, @Controller?

**Model Answer:**

IoC — Inversion of Control — means the framework controls the lifecycle and wiring of objects instead of your application code. Instead of `new PaymentService()`, Spring creates and manages the `PaymentService` bean and injects it where needed. This makes components loosely coupled, testable, and replaceable.

All four annotations are specializations of `@Component` — they all result in a Spring-managed bean. The differences are:

`@Component` is the generic stereotype for any Spring-managed class.

`@Service` marks a class as containing business logic. Functionally equivalent to `@Component` but semantically meaningful — it tells developers and tools that this is a service layer class. In my billing system, `InvoiceService` and `PaymentProcessingService` are annotated this way.

`@Repository` marks a class as a data access object. It additionally enables Spring's exception translation — JDBC SQLExceptions and JPA PersistenceExceptions are translated into Spring's `DataAccessException` hierarchy. This decouples your service layer from the specific persistence technology.

`@Controller` marks a class as an MVC web controller for rendering views. `@RestController` is `@Controller + @ResponseBody` — returns data directly as JSON/XML instead of a view name.

In a microservice, the layering is: `@RestController` → `@Service` → `@Repository`. This separation makes each layer independently testable.

---

### Q19. What is dependency injection? Constructor vs field vs setter injection — which do you prefer?

**Model Answer:**

Dependency injection is the pattern where an object receives its dependencies from an external source rather than creating them itself. Spring's IoC container is the injector.

**Constructor injection:**
```java
@Service
public class InvoiceService {
    private final PaymentRepository paymentRepository;
    private final NotificationService notificationService;

    public InvoiceService(PaymentRepository paymentRepository,
                          NotificationService notificationService) {
        this.paymentRepository = paymentRepository;
        this.notificationService = notificationService;
    }
}
```

**Field injection:**
```java
@Service
public class InvoiceService {
    @Autowired
    private PaymentRepository paymentRepository; // bad practice
}
```

I strongly prefer constructor injection for several reasons:

First, dependencies are explicit — anyone reading the constructor signature immediately knows what this class needs to function. Field injection hides dependencies.

Second, immutability — fields can be `final`, guaranteeing they are set once and never changed. This is thread-safe by design.

Third, testability — in unit tests, you can instantiate the class directly without Spring context: `new InvoiceService(mockRepo, mockNotification)`. With field injection, you need Mockito's `@InjectMocks` magic or Spring's test context.

Fourth, fail-fast — if a required bean is missing, the application fails at startup. Field injection may fail silently or later at runtime.

The Spring team itself recommends constructor injection. In Spring 4.3+, if a class has a single constructor, `@Autowired` is not even required — Spring automatically uses it.

---

### Q20. How does Spring Boot autoconfiguration work?

**Model Answer:**

`@SpringBootApplication` combines three annotations: `@Configuration` (marks the class as a bean definition source), `@ComponentScan` (scans the current package and subpackages for beans), and `@EnableAutoConfiguration` (triggers autoconfiguration).

Autoconfiguration works through `@Conditional` annotations. Spring Boot includes hundreds of autoconfiguration classes — each one is annotated with conditions like:
- `@ConditionalOnClass(DataSource.class)` — only apply if DataSource is on the classpath
- `@ConditionalOnMissingBean(DataSource.class)` — only apply if you haven't already defined one
- `@ConditionalOnProperty("spring.datasource.url")` — only apply if this property is set

When you add `spring-boot-starter-data-jpa` to your pom.xml, it brings `HikariCP`, `Hibernate`, and `Spring Data JPA` onto the classpath. Spring Boot's `DataSourceAutoConfiguration` detects `DataSource.class` on the classpath and your `spring.datasource.url` in properties, and automatically creates a configured `HikariDataSource` bean — without any `@Bean` definition from you.

You can see all applied autoconfiguration by running your app with `--debug` flag. It prints a conditions report showing which configs were applied and why.

To disable a specific autoconfiguration: `@SpringBootApplication(exclude = {DataSourceAutoConfiguration.class})`.

---

### Q21. What is `@Transactional`? Explain propagation, isolation, and pitfalls.

**Model Answer:**

`@Transactional` is Spring's declarative transaction management. When you annotate a method, Spring wraps it in a transaction proxy — a transaction is begun before the method, committed on success, and rolled back on unchecked exceptions.

**Propagation** controls what happens when a transactional method is called from within another transaction:

- `REQUIRED` (default) — join the existing transaction, or create one if none exists.
- `REQUIRES_NEW` — always create a new transaction, suspending the outer one. Used when you want a nested operation to commit independently — for example, writing an audit log even if the main transaction rolls back.
- `SUPPORTS` — join existing transaction if present, otherwise run non-transactionally.
- `MANDATORY` — must run within an existing transaction, throw if none.
- `NOT_SUPPORTED` — suspend the current transaction and run without one.
- `NEVER` — throw if a transaction exists.

**Isolation** prevents concurrency anomalies:

- `READ_COMMITTED` (Spring's default for most DBs) — a transaction reads only committed data. Prevents dirty reads but allows non-repeatable reads.
- `REPEATABLE_READ` — same query returns same rows within a transaction. Prevents non-repeatable reads but allows phantom reads.
- `SERIALIZABLE` — full isolation. Slowest. Prevents all anomalies.

**Critical pitfalls:**

1. **Self-invocation bypasses the proxy.** If method A calls `this.methodB()` and methodB is `@Transactional`, the transaction is ignored because the call goes directly to the object, not through the Spring proxy. Fix: inject `self` via `@Autowired private MyService self` and call `self.methodB()`.

2. **Checked exceptions do not roll back by default.** Only `RuntimeException` and `Error` trigger rollback. To rollback on checked exceptions: `@Transactional(rollbackFor = Exception.class)`.

3. **`@Transactional` on private methods has no effect.** Spring proxies cannot intercept private methods.

4. **Long-running transactions cause lock contention.** Keep transactions short. Never perform slow I/O (HTTP calls, file writes) inside a `@Transactional` method.

---

### Q22. How does Spring Security work? Explain the filter chain.

**Model Answer:**

Spring Security intercepts every HTTP request through a chain of servlet filters before it reaches your controller. The key filters in order:

1. `SecurityContextPersistenceFilter` — restores the `SecurityContext` from the session (or JWT token) at the start of each request.
2. `UsernamePasswordAuthenticationFilter` — handles form-based login by extracting credentials and calling the `AuthenticationManager`.
3. `BearerTokenAuthenticationFilter` — for JWT/OAuth2, extracts the Bearer token from the `Authorization` header.
4. `ExceptionTranslationFilter` — catches `AuthenticationException` and `AccessDeniedException` and converts them to HTTP 401/403 responses.
5. `FilterSecurityInterceptor` — the last filter, applies access control rules (does this user have the required role to access this URL?).

**Authentication flow:**

The filter calls `AuthenticationManager.authenticate(authentication)`. The default implementation `ProviderManager` delegates to a list of `AuthenticationProvider`s. `DaoAuthenticationProvider` loads the user via `UserDetailsService.loadUserByUsername()`, then compares the provided password against the stored hash using `PasswordEncoder`. If successful, it returns an authenticated `Authentication` object that is stored in `SecurityContextHolder`.

**In my fitness tracker with Keycloak:**

The API Gateway validates the JWT token issued by Keycloak. It extracts claims (user ID, roles) and creates a `JwtAuthenticationToken`. Downstream services receive the token in the `Authorization` header and use Spring Security's resource server config to validate it — they trust Keycloak's public key to verify the signature without calling Keycloak again.

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/actuator/health").permitAll()
            .anyRequest().authenticated());
    return http.build();
}
```

---

### Q23. Explain Spring Boot Actuator — what endpoints matter in production?

**Model Answer:**

Spring Boot Actuator exposes operational endpoints for monitoring and managing a running application. The most important ones in production:

**`/actuator/health`** — returns `UP` or `DOWN` with detailed sub-checks (database, disk, Kafka, Redis). Kubernetes uses this for liveness and readiness probes. I configure separate `/actuator/health/liveness` and `/actuator/health/readiness` — readiness returns DOWN during startup or graceful shutdown to drain traffic before the pod terminates.

**`/actuator/metrics`** — exposes JVM metrics (heap, GC, threads), HTTP request metrics (count, latency percentiles), Hikari connection pool metrics, and Kafka consumer lag. Prometheus scrapes this endpoint every 15 seconds. I alert on `jvm.memory.used{area=heap}` above 85% and `http.server.requests{status=5xx}` spiking.

**`/actuator/loggers`** — allows runtime log level changes without restart. In production incidents I set a specific package to DEBUG temporarily: `curl -X POST /actuator/loggers/com.cognizant.billing -d '{"configuredLevel":"DEBUG"}' -H 'Content-Type: application/json'`.

**`/actuator/threaddump`** — dumps all thread states. Invaluable for diagnosing deadlocks and thread starvation. I've used this to catch Hikari pool exhaustion — all request threads in WAITING state waiting for a connection.

**`/actuator/heapdump`** — triggers a heap dump download. Used during OOM investigations.

**`/actuator/env`** — shows all configuration properties. Must be secured — can expose database passwords. I expose only `/health` publicly and protect all others behind an internal network or admin role.

---

### Q24. How do you handle exceptions globally in Spring Boot?

**Model Answer:**

There are two approaches: `@ExceptionHandler` on individual controllers (local scope), and `@ControllerAdvice` for global exception handling across all controllers.

`@ControllerAdvice` is the correct approach for any real application:

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ProblemDetail> handleNotFound(ResourceNotFoundException ex) {
        ProblemDetail detail = ProblemDetail.forStatusAndDetail(
            HttpStatus.NOT_FOUND, ex.getMessage());
        detail.setTitle("Resource Not Found");
        detail.setProperty("timestamp", Instant.now());
        return ResponseEntity.status(HttpStatus.NOT_FOUND).body(detail);
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ProblemDetail> handleValidation(MethodArgumentNotValidException ex) {
        Map<String, String> errors = ex.getBindingResult().getFieldErrors().stream()
            .collect(Collectors.toMap(
                FieldError::getField, 
                fe -> Objects.requireNonNullElse(fe.getDefaultMessage(), "Invalid")));
        ProblemDetail detail = ProblemDetail.forStatus(HttpStatus.BAD_REQUEST);
        detail.setTitle("Validation Failed");
        detail.setProperty("errors", errors);
        return ResponseEntity.badRequest().body(detail);
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ProblemDetail> handleGeneric(Exception ex) {
        log.error("Unhandled exception", ex);
        ProblemDetail detail = ProblemDetail.forStatusAndDetail(
            HttpStatus.INTERNAL_SERVER_ERROR, "An unexpected error occurred");
        return ResponseEntity.internalServerError().body(detail);
    }
}
```

`ProblemDetail` follows RFC 7807 — the standard for HTTP API error responses, supported natively in Spring Boot 3+. It includes `type`, `title`, `status`, `detail`, and `instance` fields, plus custom properties.

Key rules: never expose stack traces in production responses (log them, don't return them), always return machine-readable error codes, always return the correct HTTP status code.

---

### Q25. Explain Spring Data JPA N+1 problem and how to fix it.

**Model Answer:**

The N+1 problem occurs when you load a list of entities and then access a lazy-loaded association on each one, causing one additional SQL query per entity.

Example: Loading 100 customers, then accessing `customer.getOrders()` in a loop triggers 1 query for the 100 customers plus 100 separate queries for each customer's orders — 101 queries total instead of 1.

```java
// N+1 problem
List<Customer> customers = customerRepo.findAll(); // 1 query
customers.forEach(c -> 
    c.getOrders().forEach(o -> log.info(o.getAmount()))); // 100 queries
```

**Fix 1: JPQL JOIN FETCH**
```java
@Query("SELECT c FROM Customer c JOIN FETCH c.orders WHERE c.status = :status")
List<Customer> findByStatusWithOrders(@Param("status") String status);
```
This generates a single JOIN SQL query loading customers and orders together.

**Fix 2: @EntityGraph**
```java
@EntityGraph(attributePaths = {"orders", "orders.items"})
List<Customer> findByStatus(String status);
```
Declarative, no JPQL required. Can specify nested paths.

**Fix 3: @BatchSize on the collection**
```java
@OneToMany(mappedBy = "customer")
@BatchSize(size = 20)
private List<Order> orders;
```
Instead of N queries, Hibernate groups them into batches of 20, reducing N queries to N/20.

**Detecting N+1 in development:**
```yaml
spring:
  jpa:
    show-sql: true
    properties:
      hibernate:
        format_sql: true
        generate_statistics: true
```

In production, I use Hibernate statistics via Actuator or a `StatisticsService` bean that logs query counts per request. A spike in query count per HTTP request is the signal.

---

### Q26. How do you implement circuit breaker in Spring Boot with Resilience4j?

**Model Answer:**

Hystrix is deprecated. Resilience4j is the standard for Spring Boot 3+.

A circuit breaker has three states:
- **CLOSED** — normal operation, requests pass through
- **OPEN** — failure threshold exceeded, all requests fail immediately (fail fast) with a `CallNotPermittedException`
- **HALF_OPEN** — after a wait duration, a limited number of probe requests are allowed through; if they succeed, circuit closes; if they fail, it reopens

Configuration:
```yaml
resilience4j:
  circuitbreaker:
    instances:
      gemini-ai-service:
        slidingWindowSize: 10
        failureRateThreshold: 50        # open if 50% of last 10 calls fail
        waitDurationInOpenState: 30s
        permittedNumberOfCallsInHalfOpenState: 3
        minimumNumberOfCalls: 5
  retry:
    instances:
      gemini-ai-service:
        maxAttempts: 3
        waitDuration: 1s
        exponentialBackoffMultiplier: 2
```

Usage in my AI Service:
```java
@CircuitBreaker(name = "gemini-ai-service", fallbackMethod = "defaultRecommendation")
@Retry(name = "gemini-ai-service")
public AIRecommendation getRecommendation(Activity activity) {
    return geminiClient.analyze(activity);
}

public AIRecommendation defaultRecommendation(Activity activity, Exception ex) {
    log.warn("Gemini API unavailable, returning default recommendation", ex);
    return AIRecommendation.defaultFor(activity.getType());
}
```

The `@Retry` and `@CircuitBreaker` annotations compose — retries happen first, then the circuit breaker counts the final outcome.

**Bulkhead** limits concurrent calls:
```java
@Bulkhead(name = "gemini-ai-service", type = Bulkhead.Type.THREADPOOL)
```
This prevents one slow dependency from exhausting your entire thread pool.

---

## 3. Microservices & System Design

---

### Q27. What is a microservice? How is it different from a monolith?

**Model Answer:**

A microservice is a small, independently deployable service with a single business responsibility, its own data store, and clear API boundaries. Services communicate via APIs (REST, gRPC) or events (Kafka, RabbitMQ).

A monolith is a single deployable unit where all modules share the same process, memory space, and database. Modules communicate through direct method calls.

**Monolith advantages:** Simple to develop, test, and deploy initially. No network overhead for inter-module calls. Easy transactions across modules. Good for small teams or early-stage products.

**Monolith disadvantages:** As it grows, a change in the billing module requires redeploying the entire application. A memory leak in the notifications module takes down billing. Scaling requires scaling the entire application even if only one component is the bottleneck.

**Microservices advantages:** Independent deployment — I can deploy the billing service six times a day without touching the customer service. Independent scaling — I can give 20 pods to the billing service and 2 to the settings service. Technology choice per service.

**Microservices disadvantages:** Distributed systems complexity — network failures, latency, partial failures. Distributed transactions are hard (no ACID across services). Observability requires centralized logging and tracing. Operational overhead.

In my work at Cognizant, I am migrating Deutsche Telekom's monolithic billing system to microservices using the Strangler Fig pattern — routing new features to microservices while keeping the monolith for existing features, gradually shifting all traffic.

---

### Q28. Explain the Saga pattern for distributed transactions.

**Model Answer:**

In a microservices architecture, you cannot use a traditional 2-phase commit across services — it requires all services to be available simultaneously and creates tight coupling. The Saga pattern replaces 2PC with a sequence of local transactions, each with a compensating transaction that reverses it if a later step fails.

**Choreography-based Saga** uses events. Each service listens for events and publishes events when done. No central coordinator.

Example: Order placement in e-commerce:
1. Order Service: creates order → publishes `OrderCreated` event
2. Payment Service: listens for `OrderCreated` → charges customer → publishes `PaymentProcessed`
3. Inventory Service: listens for `PaymentProcessed` → reserves stock → publishes `StockReserved`
4. If stock is unavailable: publishes `StockReservationFailed` → Payment Service listens → issues refund → publishes `PaymentRefunded` → Order Service cancels order

Advantage: no single point of failure. Disadvantage: hard to track the overall transaction state, complex event choreography.

**Orchestration-based Saga** uses a central saga orchestrator (a service or state machine) that explicitly commands each participant and handles failures.

The orchestrator sends `ProcessPayment` command to Payment Service, waits for reply, then sends `ReserveStock` to Inventory Service. On failure, it sends compensating commands in reverse order.

Advantage: clear workflow, easy to track state. Disadvantage: orchestrator is a single point of coordination.

**In my billing system context:** A telecom billing run involves charging the customer, updating the usage record, and generating an invoice — these must either all succeed or all compensate. I would use orchestration here because billing workflows are complex and require clear audit trails.

---

### Q29. What is the Outbox Pattern? Why do you need it with Kafka?

**Model Answer:**

The Outbox Pattern solves the dual-write problem: how do you atomically write to a database AND publish an event to Kafka?

The naive approach fails:
```java
// WRONG - not atomic
orderRepository.save(order);           // succeeds
kafkaTemplate.send("orders", event);   // fails? order saved but event not published → inconsistency
```

Or the reverse — event published but database write fails. Either way, you have a ghost order or a lost event.

**The Outbox pattern:**

1. In the same database transaction that writes the business data, also write the event to an `outbox` table.
2. A separate process (the message relay) polls the outbox table, publishes events to Kafka, and marks them as sent.

```sql
CREATE TABLE outbox_events (
    id UUID PRIMARY KEY,
    aggregate_id VARCHAR(255),
    event_type VARCHAR(255),
    payload JSONB,
    created_at TIMESTAMP,
    published_at TIMESTAMP NULL
);
```

```java
@Transactional
public Activity saveActivity(ActivityRequest request) {
    Activity activity = activityRepository.save(new Activity(request));
    
    // Same transaction — atomic
    outboxRepository.save(OutboxEvent.of(
        activity.getId(), "ActivityCreated", toJson(activity)));
    
    return activity;
}
```

The relay (can be a scheduled job, Debezium CDC, or a dedicated poller) reads unpublished outbox events and publishes them to Kafka. Once published, it marks `published_at`.

This guarantees **at-least-once delivery** — if the relay crashes after publishing but before marking, it will republish on restart. Consumers must be **idempotent** to handle duplicates (check if event ID has already been processed).

In my fitness tracker, I used this pattern between Activity Service and Kafka to guarantee every saved activity triggers the AI recommendation pipeline.

---

### Q30. Explain Kafka consumer groups, partition assignment, and rebalancing.

**Model Answer:**

Kafka topics are divided into partitions. Each partition is an ordered, append-only log. Multiple consumers can be grouped into a **consumer group** — Kafka assigns each partition to exactly one consumer within the group at any time. This enables parallel processing.

If my `activity-events` topic has 6 partitions and I start 6 consumer instances in the same group, each instance gets exactly 1 partition. If I start only 3 instances, each gets 2 partitions. If I start 7 instances, one sits idle — you cannot have more consumers than partitions.

**Partition assignment strategies:**
- `RangeAssignor` — assigns contiguous ranges per topic (default)
- `RoundRobinAssignor` — distributes evenly across all partitions and consumers
- `StickyAssignor` — minimizes partition movement on rebalance (preferred in production)
- `CooperativeStickyAssignor` — incremental rebalance, no stop-the-world pause

**Rebalancing** is triggered when a consumer joins or leaves the group, a partition count changes, or a consumer fails a heartbeat. During classic rebalancing, all consumers stop processing (stop-the-world) while the group coordinator reassigns partitions. With `CooperativeStickyAssignor`, only the partitions that need to move are revoked and reassigned — other consumers continue processing.

**Offset management:** Each consumer commits offsets to Kafka (or Zookeeper in older versions). The offset is the position of the last successfully processed message. If a consumer crashes and restarts, it resumes from the committed offset.

```yaml
spring:
  kafka:
    consumer:
      group-id: ai-fitness-group
      auto-offset-reset: earliest  # start from beginning if no committed offset
      enable-auto-commit: false    # manual commit for exactly-once semantics
      partition-assignment-strategy: org.apache.kafka.clients.consumer.CooperativeStickyAssignor
```

I use manual commits in my fitness tracker to ensure AI recommendations are only committed after successful storage in MongoDB, preventing message loss.

---

### Q31. Design a rate limiter for an API Gateway handling 10,000 requests/sec.

**Model Answer:**

At 10k rps across a cluster of 10 gateway pods, I need a distributed rate limiter — in-memory per pod is insufficient.

**Algorithm choice:**

- **Token bucket** — tokens refill at a fixed rate, burst allowed up to bucket capacity. Good for allowing short bursts (a user uploading multiple files at once).
- **Sliding window log** — store timestamps of all requests in a sorted set, count requests in the last N seconds. Exact but memory-intensive.
- **Sliding window counter** — hybrid: tracks count in current and previous windows, calculates weighted rate. Approximate but memory-efficient.

I would use **token bucket with Redis** for production:

```lua
-- Lua script (atomic in Redis)
local key = KEYS[1]
local capacity = tonumber(ARGV[1])
local refill_rate = tonumber(ARGV[2])  -- tokens per second
local now = tonumber(ARGV[3])          -- current timestamp in ms

local state = redis.call('HMGET', key, 'tokens', 'last_refill')
local tokens = tonumber(state[1]) or capacity
local last_refill = tonumber(state[2]) or now

local elapsed = (now - last_refill) / 1000.0
local refilled = math.min(capacity, tokens + elapsed * refill_rate)

if refilled >= 1 then
    redis.call('HMSET', key, 'tokens', refilled - 1, 'last_refill', now)
    redis.call('EXPIRE', key, 3600)
    return 1  -- allowed
else
    return 0  -- rejected
end
```

Rate limit key by `userId:endpoint` or `apiKey:endpoint`. Spring Cloud Gateway has built-in Redis rate limiting via `RequestRateLimiter` filter.

**Returning the right response:**
- HTTP 429 Too Many Requests
- `Retry-After` header indicating when to retry
- `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset` headers

**Handling Redis failure:** Use a local fallback rate limiter with a lower limit, or fail open (allow traffic) with an alert, depending on business requirements.

---

### Q32. How do you implement distributed tracing across 6 microservices?

**Model Answer:**

Distributed tracing correlates a single user request across multiple service hops. Without it, you have 6 separate log files with no way to reconstruct what happened during a single user action.

The standard is **OpenTelemetry** (OTel) — a vendor-neutral observability framework.

**Concepts:**
- **Trace** — the full end-to-end journey of a request
- **Span** — a single unit of work within a trace (a service call, a DB query)
- **Trace ID** — propagated across all service boundaries in HTTP headers (`traceparent`) and Kafka record headers
- **Span ID** — unique identifier for each span within the trace

**Spring Boot setup:**

```xml
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-tracing-bridge-otel</artifactId>
</dependency>
<dependency>
    <groupId>io.opentelemetry.instrumentation</groupId>
    <artifactId>opentelemetry-spring-boot-starter</artifactId>
</dependency>
```

```yaml
management:
  tracing:
    sampling:
      probability: 0.1  # sample 10% in production
otel:
  exporter:
    otlp:
      endpoint: http://jaeger:4317
```

Spring Boot 3.x auto-instruments HTTP requests, `RestTemplate`, `WebClient`, and Spring Data queries. Trace ID and span ID are automatically included in all log entries.

**Kafka trace propagation:** When publishing to Kafka, inject trace context into record headers. On the consumer side, extract and restore the trace context before processing.

In my fitness tracker, a single "add activity" request creates a trace spanning: API Gateway → Activity Service (DB write + Kafka publish) → AI Service (Kafka consume + Gemini API call + MongoDB write). In Jaeger, I can see the full timeline with timing for each step.

---

## 4. Apache Kafka

---

### Q33. Explain Kafka architecture — topics, partitions, brokers, consumer groups.

**Model Answer:**

Kafka is a distributed, fault-tolerant, high-throughput message streaming platform.

**Broker** is a Kafka server. A Kafka cluster has multiple brokers for fault tolerance and horizontal scaling. Each broker holds a subset of partition replicas.

**Topic** is a logical stream of messages, like a database table but for events. Topics are divided into **partitions** for parallelism. Each partition is an ordered, immutable sequence of records.

**Replication:** Each partition has a **leader** and configurable **replicas** on other brokers. All reads and writes go to the leader. Replicas sync from the leader. If the leader broker fails, a replica is elected as the new leader. `replication.factor=3` is standard for production — tolerates 2 broker failures.

**Producer** writes to a topic. The partition for a message is determined by:
1. Explicit partition specified by the producer
2. `key.hashCode() % numPartitions` if a key is provided
3. Round-robin if no key

**Consumer group** — each message in a partition is delivered to exactly one consumer within a group. Multiple consumer groups can read the same topic independently — each group maintains its own offsets. This is how Kafka can simultaneously feed a billing service, an analytics service, and an audit service from the same stream.

**Retention:** Kafka retains messages for a configurable duration (e.g., 7 days) regardless of whether they have been consumed. This allows consumers to replay events — invaluable for debugging and reprocessing.

In my fitness tracker: Activity Service publishes to `activity-events` topic with `userId` as the partition key — this guarantees all activities for a given user are ordered and go to the same AI Service consumer instance.

---

### Q34. Your Kafka consumer is lagging by 1 million messages. How do you recover?

**Model Answer:**

Consumer lag means the difference between the latest produced offset and the consumer's committed offset. 1 million messages behind is serious — first step is understanding why.

**Diagnosis:**
1. Check `kafka-consumer-groups.sh --describe --group <group>` — see lag per partition, which partitions are worst.
2. Compare producer throughput vs consumer throughput. Is the producer publishing faster than the consumer can process?
3. Check consumer processing time — is each message taking longer than expected? Perhaps the Gemini API call is slow.
4. Check consumer health — any consumers crashed or unhealthy?

**Recovery steps:**

**Step 1: Scale up consumers.** Add consumer instances up to the partition count. If you have 6 partitions and 2 consumers, add 4 more — each processes its partition in parallel. This is the first and safest fix.

**Step 2: Optimize the consumer.** In my AI Service, the bottleneck was the synchronous Gemini API call. I switched to async batch processing — consume 100 messages, send them to Gemini in a batch, commit offsets after all 100 are processed.

**Step 3: Increase `max.poll.records`.** Default is 500. Increasing to 2000 allows processing more messages per poll cycle.

**Step 4: If lag is from a historical backfill:** Temporarily spin up dedicated catch-up consumer group, reset offset to the beginning, process at maximum throughput, then stop.

**Step 5: If messages are truly stale and data loss is acceptable:** `kafka-consumer-groups.sh --reset-offsets --to-latest` resets the consumer to the latest offset, skipping old messages. This should be a last resort with explicit business approval.

**Prevention:** Alert on consumer lag exceeding a threshold (e.g., > 100k messages or > 5 minutes behind). Auto-scale consumers with KEDA (Kubernetes Event-Driven Autoscaling) based on consumer lag metric.

---

## 5. React & Frontend

---

### Q35. What is the Virtual DOM? How does React reconciliation work?

**Model Answer:**

The Virtual DOM is a lightweight JavaScript object representation of the actual DOM tree. React maintains a virtual DOM in memory — when state changes, React creates a new virtual DOM tree and diffs it against the previous one.

**Reconciliation** is the algorithm React uses to compute the minimal set of real DOM mutations needed to update the UI. The key insight is that diffing two arbitrary trees is O(n³) — too slow. React uses heuristics to make it O(n):

1. **Elements of different types produce different trees.** If a `<div>` becomes a `<section>`, React tears down the old tree and builds a fresh one from scratch.

2. **The `key` prop tells React which elements are stable across renders.** For lists: `<li key={item.id}>`. Without keys, React uses index — this causes incorrect behavior when items are reordered or inserted, because React matches by position and updates the wrong element.

3. **Components of the same type preserve state.** If `<Counter count={1}>` re-renders as `<Counter count={2}>`, React updates the props and triggers `render()` — it does not unmount and remount.

**React Fiber** (React 16+) is the reimplemented reconciliation engine. It breaks rendering work into incremental units that can be paused, aborted, and resumed. This enables:
- Time-slicing: React can yield to the browser for higher-priority work (user input)
- Concurrent features: `useTransition`, `Suspense`, `startTransition`
- React 18's concurrent rendering

In my fitness tracker's activity dashboard, I use `React.memo` on the `ActivityCard` component to skip re-renders when the activity data hasn't changed — the reconciler still runs but React shortcuts the update before re-rendering the component.

---

### Q36. Explain useState vs useRef — when do you use useRef?

**Model Answer:**

`useState` returns a stateful value and a setter. Calling the setter triggers a re-render. This is how you drive UI updates.

`useRef` returns a mutable container object whose `.current` property can hold any value. Changing `.current` does NOT trigger a re-render. The ref persists across renders — it is the same object throughout the component's lifecycle.

**Use cases for useRef:**

**1. DOM access:**
```javascript
const inputRef = useRef(null);
<input ref={inputRef} />
// Later: inputRef.current.focus()
```

**2. Storing previous value without re-render:**
```javascript
const prevCount = useRef(count);
useEffect(() => { prevCount.current = count; });
// prevCount.current is the count from the previous render
```

**3. Storing interval/timeout IDs:**
```javascript
const timerRef = useRef(null);
const startTimer = () => {
    timerRef.current = setInterval(() => {
        // do work
    }, 1000);
};
const stopTimer = () => clearInterval(timerRef.current);
```
If stored in state, updating the interval ID would trigger a re-render — unnecessary and potentially causing an infinite loop.

**4. Breaking stale closure in event handlers:**
When a callback captures a stale value from closure, storing the latest value in a ref and reading `ref.current` inside the callback always gives the latest value without needing the callback to be recreated.

The key mental model: state is for values that, when changed, should update what the user sees. Refs are for values that need to persist across renders but do not affect the rendered output.

---

### Q37. Explain useEffect — dependency array pitfalls.

**Model Answer:**

`useEffect(callback, deps)` runs the callback after the component renders. The deps array controls when it re-runs.

- No deps array: runs after every render — usually a mistake, easily causes infinite loops.
- `[]` empty array: runs once after mount, like `componentDidMount`. Cleanup runs on unmount.
- `[dep1, dep2]`: runs after mount and after any render where dep1 or dep2 changed (compared by reference using `Object.is`).

**The cleanup function:**
```javascript
useEffect(() => {
    const subscription = eventBus.subscribe(handleEvent);
    return () => subscription.unsubscribe(); // cleanup before next effect runs + on unmount
}, []);
```

**Pitfall 1: Missing dependencies (stale closure)**
```javascript
// BUG: count is stale — the effect captures count=0 and never updates
const [count, setCount] = useState(0);
useEffect(() => {
    const id = setInterval(() => {
        console.log(count); // always 0
    }, 1000);
    return () => clearInterval(id);
}, []); // count is missing from deps

// FIX: use functional updater to avoid capturing count
setCount(prev => prev + 1);
```

**Pitfall 2: Object/array as dependency**
```javascript
// BUG: new object created every render → effect runs every render
useEffect(() => { fetchData(config); }, [{ page: 1 }]); 

// FIX: use primitive values or useMemo/useCallback for stable references
const stableConfig = useMemo(() => ({ page }), [page]);
useEffect(() => { fetchData(stableConfig); }, [stableConfig]);
```

**Pitfall 3: Async in useEffect**
```javascript
// WRONG: useEffect callback cannot be async directly
useEffect(async () => { /* ... */ }, []);

// CORRECT: define async function inside and call it
useEffect(() => {
    const load = async () => {
        const data = await fetchActivities();
        setActivities(data);
    };
    load();
}, []);
```

---

### Q38. Explain React Context vs Redux Toolkit — when do you use which?

**Model Answer:**

**React Context** is a built-in mechanism for passing data through the component tree without prop drilling. Every component that consumes the context re-renders when the context value changes — even if only a small part of the value changed.

Context is appropriate for: authentication state, theme, locale, feature flags — global values that change rarely and are read by many components. For my fitness tracker, I used Context for the authenticated user object, which only changes on login/logout.

**Redux Toolkit** is a state management library with a predictable state container, DevTools, time-travel debugging, middleware, and fine-grained subscriptions via `useSelector`.

Redux is appropriate for: complex client-side state with many interactions (e.g., a real-time dashboard with filters, sorting, pagination, selected items, loading states), shared state that changes frequently, state that needs to be accessed and updated from many unrelated components.

In my fitness tracker, I used Redux Toolkit for the activity dashboard state because:
- Activity list, loading state, filters, selected date range, AI recommendation panel open/closed — many interacting states
- Needed `createAsyncThunk` for API calls with loading/error states
- Redux DevTools made debugging state transitions trivial
- `createSlice` made reducers concise and type-safe

```javascript
const activitiesSlice = createSlice({
    name: 'activities',
    initialState: { items: [], status: 'idle', error: null },
    reducers: {
        filterByType: (state, action) => {
            state.activeFilter = action.payload;
        }
    },
    extraReducers: builder => {
        builder
            .addCase(fetchActivities.pending, state => { state.status = 'loading'; })
            .addCase(fetchActivities.fulfilled, (state, action) => {
                state.status = 'succeeded';
                state.items = action.payload;
            });
    }
});
```

Rule of thumb: start with Context, migrate to Redux when you feel the pain of complex state interactions.

---

### Q39. How does OAuth2 PKCE flow work in your React frontend with Keycloak?

**Model Answer:**

PKCE — Proof Key for Code Exchange — is the secure OAuth2 flow for SPAs (Single Page Applications) and mobile apps. SPAs cannot keep a client secret private (the code is in the browser), so PKCE replaces the client secret with a cryptographic proof.

**Flow in my fitness tracker:**

1. **React generates a `code_verifier`** — a random 32-byte string, base64url encoded.
2. **React computes `code_challenge`** — `SHA256(code_verifier)`, base64url encoded.
3. **React redirects to Keycloak** with `response_type=code`, `code_challenge`, `code_challenge_method=S256`, `redirect_uri`, `client_id`.
4. **User authenticates** in Keycloak's hosted login page.
5. **Keycloak redirects back** to the React app with an authorization `code` in the URL.
6. **React exchanges the code for tokens** — sends `code` + `code_verifier` to Keycloak's token endpoint. Keycloak verifies that `SHA256(code_verifier) == code_challenge` stored from step 3. Only the original requester can complete the exchange.
7. **Tokens returned:** access token (short-lived, 5 min), refresh token (longer-lived, 30 min).

**Token storage strategy:**
- Access token: in memory (JavaScript variable / Redux store) — lost on refresh, that's okay because we use refresh token to get a new one.
- Refresh token: in an `httpOnly`, `Secure`, `SameSite=Strict` cookie — not accessible to JavaScript, protected from XSS.
- **Never store tokens in `localStorage`** — XSS vulnerability.

When the API Gateway receives a request, it validates the access token's signature against Keycloak's public key (fetched from `/.well-known/jwks.json`) without contacting Keycloak on every request.

---

## 6. AWS & DevOps

---

### Q40. Explain your GitHub Actions CI/CD pipeline.

**Model Answer:**

In my Spring Boot CI/CD project, I built a complete pipeline that eliminated all manual deployment steps. Here is the full flow:

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Set up JDK 21
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: maven
      
      - name: Run tests
        run: mvn test
      
      - name: Build JAR
        run: mvn package -DskipTests
      
      - name: Build Docker image
        run: docker build -t my-app:${{ github.sha }} .
      
      - name: Push to Docker Hub
        run: |
          echo ${{ secrets.DOCKER_PASSWORD }} | docker login -u ${{ secrets.DOCKER_USERNAME }} --password-stdin
          docker push my-app:${{ github.sha }}
      
  deploy:
    needs: build-and-test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to EC2
        uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.EC2_HOST }}
          username: ubuntu
          key: ${{ secrets.EC2_SSH_KEY }}
          script: |
            docker pull my-app:${{ github.sha }}
            docker stop app || true
            docker run -d --name app -p 8080:8080 \
              -e SPRING_PROFILES_ACTIVE=prod \
              my-app:${{ github.sha }}
```

**What I improved over basic setups:**
- Tests run on every push, including PRs — prevents broken code from reaching main
- Docker layer caching speeds up builds
- GitHub Secrets for all credentials — never hardcoded
- Deploy only on main branch merges — PRs trigger build+test but not deploy
- `docker stop || true` prevents pipeline failure if no container exists

**Next iteration for production:** Add health check after deploy — if `/actuator/health` returns non-200 within 60 seconds, automatically roll back to the previous image tag.

---

### Q41. Explain Kubernetes — Pod, Deployment, Service, Ingress, ConfigMap, Secret.

**Model Answer:**

**Pod** is the smallest deployable unit in Kubernetes — one or more containers that share the same network namespace and storage volumes. Containers in a pod communicate via `localhost`. Pods are ephemeral — they are created and destroyed, not updated in place.

**Deployment** manages a set of identical pod replicas. It declares the desired state — "I want 3 replicas of my billing service running image v2.1". The Deployment controller continuously reconciles actual state with desired state. It handles rolling updates: creates new pods with the new image, waits for them to be ready, then terminates old ones.

**Service** provides a stable network endpoint for a set of pods. Pods have ephemeral IPs — they change when pods restart. A Service has a fixed ClusterIP and DNS name (e.g., `billing-service:8080`), and load-balances traffic across healthy pods using label selectors.

**Ingress** manages external HTTP/HTTPS access into the cluster. It routes requests to different Services based on hostname or URL path — `api.myapp.com/billing` → billing-service, `api.myapp.com/activities` → activity-service. Handles SSL termination.

**ConfigMap** stores non-sensitive configuration as key-value pairs — database URLs, feature flags, Spring profiles. Mounted as environment variables or files in pods.

**Secret** stores sensitive data — passwords, API keys, TLS certificates. Base64-encoded in etcd (not encrypted by default — enable encryption at rest in production). Accessed via environment variables or volume mounts. In production, use AWS Secrets Manager with the Secrets Store CSI driver instead of plain Kubernetes Secrets.

**Readiness vs Liveness probes:**
- Readiness: "Is this pod ready to receive traffic?" — gates Service load balancing. Returns DOWN during startup, shutdown, or when DB is unavailable.
- Liveness: "Is this pod alive?" — if it fails, kubelet restarts the pod. Returns DOWN only for unrecoverable states (deadlock, OOM).

---

### Q42. Blue/Green vs Canary vs Rolling deployment — which for billing?

**Model Answer:**

**Rolling deployment** gradually replaces old pods with new ones. At any point, some pods run the old version and some run the new. K8s default. Low resource cost — no extra infrastructure needed. Risk: if the new version is buggy, some users get errors while rollout is in progress. Rollback requires another rolling update.

**Blue/Green deployment** maintains two identical environments — Blue (current production) and Green (new version). Traffic is switched all at once at the load balancer level. Benefits: instant rollback (switch back to Blue), zero mixed-version exposure. Cost: requires 2x infrastructure.

**Canary deployment** routes a small percentage of traffic (e.g., 5%) to the new version while 95% stays on the old version. You observe error rates, latency, and business metrics. If metrics look healthy, gradually increase the percentage. If problems appear, immediately route 0% to the canary. Requires sophisticated traffic splitting (Istio, AWS ALB weighted target groups).

**For Deutsche Telekom's billing system, I recommend Blue/Green:**

Billing involves financial transactions. Dual writes, invoice generation, payment processing — these cannot have a period where two versions are simultaneously active. A rolling update means a request might go to a pod running old billing logic, then the next request to a pod running new billing logic, with potentially incompatible database state.

Blue/Green gives a clean cutover: the new version is fully tested on the Green environment, database migration is applied, then all traffic switches in one atomic operation. Rollback is instant — redirect traffic back to Blue, no re-migration needed (assuming backward-compatible schema changes).

---

## 7. Databases & SQL

---

### Q43. Write a SQL query to find the top 3 billing amounts per customer in the last 30 days.

**Model Answer:**

```sql
SELECT 
    customer_id,
    invoice_id,
    amount,
    invoice_date,
    rank_within_customer
FROM (
    SELECT 
        customer_id,
        invoice_id,
        amount,
        invoice_date,
        RANK() OVER (
            PARTITION BY customer_id 
            ORDER BY amount DESC
        ) AS rank_within_customer
    FROM invoices
    WHERE invoice_date >= NOW() - INTERVAL 30 DAY
      AND status = 'PAID'
) ranked
WHERE rank_within_customer <= 3
ORDER BY customer_id, rank_within_customer;
```

**Why RANK() vs ROW_NUMBER() vs DENSE_RANK():**
- `ROW_NUMBER()` — assigns unique sequential numbers, ties get different numbers arbitrarily. Use when you need exactly N rows.
- `RANK()` — ties get the same rank, next rank skips (1, 1, 3). Use when tied amounts should both appear as "top 3".
- `DENSE_RANK()` — ties get the same rank, next rank does not skip (1, 1, 2). Use when you want exactly 3 distinct rank levels.

For "top 3 billing amounts", RANK() or DENSE_RANK() is semantically correct — if two invoices tie at #2, both should appear.

**Performance:** Index on `(customer_id, invoice_date, amount)` — the WHERE clause filters by date (uses index range scan), PARTITION BY customer_id and ORDER BY amount are satisfied by the index without additional sort.

---

### Q44. Explain database indexing — B-tree vs Hash vs Composite indexes.

**Model Answer:**

An index is a data structure that allows the database to find rows without scanning the entire table. The trade-off: faster reads, slower writes (index must be updated on every INSERT/UPDATE/DELETE), more storage.

**B-tree index** (default in MySQL InnoDB) stores keys in a balanced tree. Supports equality queries (`WHERE customer_id = 5`), range queries (`WHERE amount BETWEEN 100 AND 500`), and `ORDER BY`. Approximately O(log n) lookup. Most queries benefit from B-tree.

**Hash index** stores a hash of the indexed column. O(1) lookup for exact equality only. Cannot support range queries or ORDER BY. MySQL InnoDB does not support explicit hash indexes (it uses adaptive hash index internally). Good for in-memory databases or exact-match lookup tables.

**Composite index** covers multiple columns: `INDEX idx_customer_date (customer_id, invoice_date)`. The leftmost prefix rule: this index is used for queries on `customer_id` alone, or on `customer_id + invoice_date`, but NOT on `invoice_date` alone.

**Covering index:** An index that includes all columns needed for a query, so the database never has to read the actual table rows. Example: if you always query `SELECT amount FROM invoices WHERE customer_id = ? AND invoice_date > ?`, an index on `(customer_id, invoice_date, amount)` covers the entire query.

**Slow query diagnosis in my billing system:** I enable `slow_query_log` in MySQL, set `long_query_time=1`. Then use `EXPLAIN` on slow queries — look for `type: ALL` (full table scan) or `Using filesort` (sort not satisfied by index). Add targeted indexes to convert to `type: ref` or `range`.

---

## 8. System Design Deep Dives

---

### Q45. Design the billing system you built at Cognizant.

**Model Answer:**

I'll walk through this systematically.

**Requirements (clarify with interviewer):**
- Functional: generate invoices for Deutsche Telekom customers based on usage, support delivery port billing, mark invoices as paid
- Non-functional: consistent (billing must be accurate), durable (no invoice can be lost), auditable, idempotent (billing a customer twice must not double-charge)

**High-level architecture:**

```
API Gateway → Billing Service → MySQL (invoices, line items)
                ↓
         Usage Aggregation Service → MySQL (usage records)
                ↓
         Payment Service → External Payment Gateway
                ↓
         Notification Service → Email/SMS
```

**Database schema:**
```sql
customers (id, name, plan_id, status)
billing_periods (id, customer_id, start_date, end_date, status)
invoices (id, customer_id, billing_period_id, total_amount, status, created_at, idempotency_key)
invoice_line_items (id, invoice_id, description, quantity, unit_price, amount)
payments (id, invoice_id, amount, gateway_reference, status, created_at)
```

**Idempotency:** Every billing run generates a unique `idempotency_key = hash(customer_id + billing_period_id)`. Before inserting, check if a key exists — if yes, return the existing invoice without creating a duplicate.

**The 5 REST APIs I built for the delivery port module:**
1. `POST /billing-runs/{period}` — trigger billing for a period
2. `GET /invoices/{customerId}` — list invoices with pagination
3. `GET /invoices/{invoiceId}` — get specific invoice with line items
4. `POST /invoices/{invoiceId}/payments` — record payment
5. `GET /billing-runs/{period}/status` — check billing run completion

**Concurrency control:** Billing runs are long operations. I use optimistic locking (`@Version` in JPA) on billing period records to prevent two runs from processing the same period simultaneously.

---

### Q46. Design a system to send 10 million notifications per day.

**Model Answer:**

10M/day = ~115/second average, but traffic is spiky (peak during business hours: ~500-1000/sec).

**Architecture:**

```
Notification API → Kafka (notification-requests topic)
                         ↓
           ┌─────────────┼─────────────┐
     Email Worker    Push Worker    SMS Worker
     (AWS SES)      (FCM/APNS)    (Twilio)
           └─────────────┼─────────────┘
                         ↓
                MongoDB (delivery-tracking)
```

**Kafka topic design:**
- `notification-requests` — raw notification events from all services
- `notification-email` — email-specific, partitioned by `userId % 32`
- `notification-push` — push-specific
- `notification-dlq` — dead-letter for failed notifications

**Worker design:**
```java
@KafkaListener(topics = "notification-email", containerFactory = "batchFactory")
public void processEmailBatch(List<NotificationEvent> events) {
    // Group by template to optimize SES send
    Map<String, List<NotificationEvent>> byTemplate = events.stream()
        .collect(groupingBy(NotificationEvent::getTemplateId));
    
    byTemplate.forEach((template, batch) -> {
        sesClient.sendBulk(batch);  // SES bulk send API
        mongoRepository.markDelivered(batch.stream().map(NotificationEvent::getId).toList());
    });
}
```

**Retry strategy:**
- Attempt 1: immediate
- Attempt 2: after 1 minute
- Attempt 3: after 5 minutes
- After 3 failures → move to DLQ → alert on-call team

**Idempotency:** Each notification has a `notificationId`. Workers check if already delivered before sending.

**Priority queues:** VIP customers or transactional notifications (OTP, payment confirmation) go to high-priority partitions with dedicated workers. Marketing notifications go to low-priority.

**Monitoring:** Alert on `consumer_lag > 50000`, DLQ message count > 0, email bounce rate > 5%.

---

## 9. Hands-On Coding Questions

---

### Q47. Implement a thread-safe LRU Cache in Java.

**Model Answer:**

LRU (Least Recently Used) cache evicts the entry that was used least recently when the cache is full.

**Data structures needed:**
- `HashMap<K, Node>` for O(1) lookup by key
- Doubly linked list for O(1) insertion, deletion, and maintaining access order

```java
public class LRUCache<K, V> {
    
    private static class Node<K, V> {
        K key;
        V value;
        Node<K, V> prev, next;
        
        Node(K key, V value) {
            this.key = key;
            this.value = value;
        }
    }
    
    private final int capacity;
    private final Map<K, Node<K, V>> map;
    private final Node<K, V> head; // dummy head (most recently used end)
    private final Node<K, V> tail; // dummy tail (least recently used end)
    private final ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
    
    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.map = new HashMap<>();
        this.head = new Node<>(null, null);
        this.tail = new Node<>(null, null);
        head.next = tail;
        tail.prev = head;
    }
    
    public V get(K key) {
        lock.writeLock().lock(); // write lock because we move node to front
        try {
            Node<K, V> node = map.get(key);
            if (node == null) return null;
            moveToFront(node);
            return node.value;
        } finally {
            lock.writeLock().unlock();
        }
    }
    
    public void put(K key, V value) {
        lock.writeLock().lock();
        try {
            Node<K, V> node = map.get(key);
            if (node != null) {
                node.value = value;
                moveToFront(node);
            } else {
                Node<K, V> newNode = new Node<>(key, value);
                map.put(key, newNode);
                addToFront(newNode);
                if (map.size() > capacity) {
                    Node<K, V> lru = removeTail();
                    map.remove(lru.key);
                }
            }
        } finally {
            lock.writeLock().unlock();
        }
    }
    
    private void addToFront(Node<K, V> node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }
    
    private void removeNode(Node<K, V> node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void moveToFront(Node<K, V> node) {
        removeNode(node);
        addToFront(node);
    }
    
    private Node<K, V> removeTail() {
        Node<K, V> lru = tail.prev;
        removeNode(lru);
        return lru;
    }
}
```

**Time complexity:** O(1) for both get and put.
**Space complexity:** O(capacity).

**Note:** I used `ReentrantReadWriteLock` — technically both get and put need write lock because get moves a node. A simpler approach is just `synchronized` on the method. For better concurrent throughput, you'd use segment-based locking similar to ConcurrentHashMap.

---

### Q48. Implement producer-consumer using BlockingQueue.

**Model Answer:**

```java
public class ProducerConsumer {
    
    private static final int QUEUE_CAPACITY = 100;
    private static final int NUM_PRODUCERS = 3;
    private static final int NUM_CONSUMERS = 5;
    
    // Poison pill to signal consumers to stop
    private static final String POISON_PILL = "STOP";
    
    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<String> queue = new LinkedBlockingQueue<>(QUEUE_CAPACITY);
        ExecutorService executor = Executors.newFixedThreadPool(NUM_PRODUCERS + NUM_CONSUMERS);
        
        // Start producers
        for (int i = 0; i < NUM_PRODUCERS; i++) {
            final int producerId = i;
            executor.submit(() -> {
                try {
                    for (int j = 0; j < 10; j++) {
                        String item = "Item-" + producerId + "-" + j;
                        queue.put(item); // blocks if queue is full
                        System.out.println("Produced: " + item);
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
        }
        
        // Start consumers
        for (int i = 0; i < NUM_CONSUMERS; i++) {
            executor.submit(() -> {
                try {
                    while (true) {
                        String item = queue.take(); // blocks if queue is empty
                        if (POISON_PILL.equals(item)) {
                            queue.put(POISON_PILL); // re-queue for other consumers
                            break;
                        }
                        System.out.println("Consumed: " + item + " by " + Thread.currentThread().getName());
                        Thread.sleep(50); // simulate processing
                    }
                } catch (InterruptedException e) {
                    Thread.currentThread().interrupt();
                }
            });
        }
        
        // Wait for all producers to finish, then signal consumers
        executor.shutdown();
        executor.awaitTermination(30, TimeUnit.SECONDS);
        
        queue.put(POISON_PILL); // one poison pill cascades to all consumers
    }
}
```

**Key points:**
- `LinkedBlockingQueue` uses separate locks for head and tail — producer and consumer can operate concurrently without contending on the same lock.
- `put()` blocks when full, `take()` blocks when empty — no busy-waiting, no `Thread.sleep()` polling.
- Poison pill pattern for graceful shutdown — cleaner than `AtomicBoolean stopFlag` because the flag check has a race condition between the check and `take()`.
- Always handle `InterruptedException` by restoring the interrupt flag — `Thread.currentThread().interrupt()`.

---

### Q49. Write a Kafka producer and consumer in Spring Boot with retry and DLQ.

**Model Answer:**

```java
// Producer
@Service
public class ActivityEventProducer {
    
    private final KafkaTemplate<String, ActivityEvent> kafkaTemplate;
    
    public void publishActivity(Activity activity) {
        ActivityEvent event = ActivityEvent.from(activity);
        
        kafkaTemplate.send("activity-events", activity.getUserId(), event)
            .thenAccept(result -> log.info("Published: offset={}", 
                result.getRecordMetadata().offset()))
            .exceptionally(ex -> {
                log.error("Failed to publish activity event: {}", activity.getId(), ex);
                // Could write to outbox table here for guaranteed delivery
                return null;
            });
    }
}

// Consumer with Retry and DLQ
@Configuration
public class KafkaConsumerConfig {
    
    @Bean
    public RetryTopicConfiguration retryTopicConfig(KafkaTemplate<String, ActivityEvent> template) {
        return RetryTopicConfigurationBuilder
            .newInstance()
            .fixedBackOff(1000L)                // 1 second between retries
            .maxAttempts(3)                      // 3 total attempts
            .retryTopicSuffix("-retry")
            .dltSuffix("-dlt")                   // activity-events-dlt
            .retryOn(TransientDataAccessException.class)
            .notRetryOn(ValidationException.class)  // don't retry business logic errors
            .create(template);
    }
}

@Service
public class ActivityEventConsumer {
    
    @KafkaListener(
        topics = "activity-events",
        groupId = "ai-fitness-group",
        containerFactory = "kafkaListenerContainerFactory"
    )
    public void consume(ActivityEvent event, Acknowledgment ack) {
        try {
            log.info("Processing activity: {}", event.getActivityId());
            aiRecommendationService.process(event);
            ack.acknowledge(); // manual commit after successful processing
        } catch (TransientDataAccessException e) {
            // Retryable — throw to trigger retry
            throw e;
        } catch (Exception e) {
            log.error("Non-retryable error for activity {}", event.getActivityId(), e);
            ack.acknowledge(); // commit offset to avoid reprocessing
            // Optionally publish to a specific DLQ manually
        }
    }
    
    @KafkaListener(topics = "activity-events-dlt", groupId = "dlt-group")
    public void consumeDlt(ActivityEvent event) {
        log.error("DLT: activity event failed all retries: {}", event.getActivityId());
        // Alert on-call, write to DB for manual investigation
        failedEventRepository.save(FailedEvent.from(event));
    }
}
```

The `RetryTopicConfiguration` creates intermediate retry topics automatically (`activity-events-retry-0`, `activity-events-retry-1`). Messages that fail are republished to the retry topic with a header indicating the retry count and next scheduled time. After all retries are exhausted, they land in the DLT.

---

## 10. Security — OAuth2, JWT, Keycloak

---

### Q50. How does JWT work? What's in a JWT? How do you validate it?

**Model Answer:**

JWT — JSON Web Token — is a compact, self-contained token for transmitting claims between parties. It consists of three Base64url-encoded parts separated by dots: `header.payload.signature`.

**Header:**
```json
{ "alg": "RS256", "typ": "JWT" }
```

**Payload (claims):**
```json
{
  "sub": "user-123",         // subject (user ID)
  "iss": "https://keycloak/realms/fitness",  // issuer
  "aud": "activity-service",  // intended audience
  "exp": 1719244800,          // expiry timestamp
  "iat": 1719241200,          // issued at
  "roles": ["ROLE_USER", "ROLE_ADMIN"]
}
```

**Signature:** `RS256(base64url(header) + "." + base64url(payload), privateKey)`

For RS256 (RSA + SHA256), Keycloak signs with its private key. Any service can verify the signature using Keycloak's public key (available at `/.well-known/jwks.json`) — without calling Keycloak on every request.

**Validation steps in my API Gateway:**
1. Extract Bearer token from `Authorization` header
2. Decode and check the header's `alg` — reject `none` algorithm (a known attack)
3. Verify the signature using Keycloak's public key (cached, refreshed periodically)
4. Check `exp` — reject expired tokens
5. Check `iss` — must match expected Keycloak issuer URL
6. Check `aud` — must include the current service name
7. Extract `roles` from claims and populate Spring Security context

**JWT is stateless:** The server holds no session state. But this means you cannot immediately revoke a JWT before it expires. For high-security actions (logout, password change), use short-lived tokens (5 minutes) + refresh token rotation, and maintain a small token revocation list in Redis.

---

## 11. HR & Behavioral

---

### Q51. Tell me about yourself — 2 minutes.

**Model Answer:**

I'm Rahul Kumar, a Java Full Stack Developer currently working at Cognizant Technology Solutions in Kolkata. I've been here for about 2 years, working with a 6-member Agile team on billing and delivery port modules for Deutsche Telekom, a major European telecom enterprise.

On the technical side, I work daily with Java, Spring Boot, microservices, MySQL, and React. In the last few months, I've been deeply involved in migrating a legacy monolithic billing application to a microservices architecture — which has given me firsthand experience with the real challenges of distributed systems, not just the theory.

Outside of work, I've built several full-stack projects to push my skills further. The one I'm most proud of is an AI-powered fitness tracker I completed this year — it's a 6-service microservices platform with Kafka, Keycloak OAuth2, the Google Gemini API, React 19, MongoDB, and Docker. I also built a complete CI/CD pipeline using GitHub Actions and AWS EC2, which genuinely changed how I think about software delivery.

I graduated with a 9.32 CGPA in Computer Science from UEM Kolkata, and I've been building toward a senior-level role where I can take ownership of systems end-to-end, not just deliver features. That's why I'm here — 20 LPA reflects the level of ownership and impact I'm confident I can deliver.

---

### Q52. Why are you leaving Cognizant after 2 years?

**Model Answer:**

Cognizant has been a great foundation. I've gone from setting up development environments to building production REST APIs for Deutsche Telekom, to now leading the migration of a monolithic system to microservices. I've genuinely learned a lot.

But I've reached a point where I want to move from working in a feature delivery team within a large enterprise engagement to actually owning a product or system. At Cognizant, architectural decisions are made above my level — I implement them. I want to be in a role where I design the system, choose the technology, and am accountable for its reliability and performance.

The 20 LPA I'm targeting is not just about compensation — it signals a senior-level role where that kind of ownership is expected. I've proven I can deliver production-quality features. I've self-built a complete microservices platform to demonstrate I can architect, not just code. Now I want an environment that challenges me at that level.

---

### Q53. Tell me about a production bug you caused and how you fixed it.

**Model Answer:**

Early in my time at Cognizant, I deployed a change to one of the billing REST APIs. The change involved adding a new optional field to the request DTO. During code review, we verified the backend logic — but we missed testing the actual API contract.

In production, an upstream system calling our API started getting 400 errors because it was sending requests without the new field, and I had accidentally marked it `@NotNull` instead of making it truly optional. The upstream system was not under our control — it took 2 hours to coordinate with the other team.

Immediate fix: I pushed a hotfix within 20 minutes removing the `@NotNull` constraint and making the field genuinely optional with a sensible default. We verified the fix in staging and pushed it.

Root cause: no contract testing. After this incident, I set up a basic suite of API contract tests that ran on every CI build. More importantly, I added backward compatibility as an explicit checklist item in our code review process.

What I learned: API changes are different from code changes. A code change only breaks things you can control. An API change can break consumers you've never spoken to. Since then, I default to additive changes — add new optional fields, never rename or remove existing ones — and I always check if there are existing API consumers before any contract change.

---

### Q54. How do you handle disagreements with your tech lead?

**Model Answer:**

I start by making sure I understand their position completely. Often what looks like a disagreement is actually an information gap — they know something about constraints, history, or future plans that I don't.

If after understanding their position I still disagree, I present my case with data. Not opinion — data. If I think a different approach is better, I write it up: here's what I propose, here's why I think it's better, here are the trade-offs I see with both approaches. I might even prototype it if it can be done quickly.

I've had a situation at Cognizant where I recommended introducing Redis caching for a frequently-accessed lookup table, and my TL felt it added unnecessary infrastructure complexity. I wrote a short analysis showing the query volume, the p99 latency impact, and the actual operational cost of adding Redis to our existing setup (which was near zero since we already had it in the cluster). He reviewed it and agreed to try it.

That said — once a decision is made, I commit to it, even if I disagree. I've learned that teams that relitigate every decision constantly are less effective than teams that make a call and execute it. If the decision proves wrong, the data will show it, and there's a right time to revisit.

---

### Q55. Where do you see yourself in 2 years?

**Model Answer:**

In 2 years, I want to be a senior engineer who owns a product area — not just a feature, but a service or a set of services end-to-end. I want to be making architectural decisions, doing capacity planning, defining API contracts, and mentoring engineers who are 1-2 years behind me.

Technically, I want to deepen my expertise in distributed systems reliability — I've built microservices and I understand the patterns, but I want experience with the hard problems at scale: consistency under network partitions, zero-downtime schema migrations on large tables, observability that actually helps you find problems before users report them.

I'm not particularly focused on a title. What I care about is that in 2 years, when a critical system has a problem at 2am, I'm one of the people who gets called — not because I'm on-call by rotation, but because I actually know how that system works end-to-end.

---

*End of Interview Preparation Guide*

---

> **Final checklist before your interview:**
> - Revisit your fitness tracker project — be ready to walk through every architectural decision
> - Practice the hands-on coding questions on paper (no IDE)
> - For every answer, have one concrete example from your own work
> - Know your resume line by line — every bullet point is a potential deep-dive
> - For 20 LPA: demonstrate ownership, not just execution
