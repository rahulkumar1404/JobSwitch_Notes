# 📘 Java Spring Boot + Apache Kafka - Complete Notes (Zero to Advanced)

> **Hindi Explanation + English Interview Answers**

---

## 📑 Table of Contents

1. [Kafka Basics](#1-kafka-basics)
2. [Kafka Architecture](#2-kafka-architecture)
3. [Spring Boot + Kafka Setup](#3-spring-boot--kafka-setup)
4. [Producer Configuration](#4-producer-configuration)
5. [Consumer Configuration](#5-consumer-configuration)
6. [Topics, Partitions & Offsets](#6-topics-partitions--offsets)
7. [Consumer Groups](#7-consumer-groups)
8. [Serialization & Deserialization](#8-serialization--deserialization)
9. [Error Handling & Retry](#9-error-handling--retry)
10. [Kafka with Avro & Schema Registry](#10-kafka-with-avro--schema-registry)
11. [Kafka Streams](#11-kafka-streams)
12. [Kafka Transactions](#12-kafka-transactions)
13. [Kafka Security](#13-kafka-security)
14. [Kafka Monitoring & Performance Tuning](#14-kafka-monitoring--performance-tuning)
15. [Interview Questions & Answers](#15-interview-questions--answers)

---

## 1. Kafka Basics

### 🇮🇳 Hindi Explanation:
> Kafka ek **distributed event streaming platform** hai jo LinkedIn ne banaya tha aur ab Apache Foundation maintain karti hai. Isko hum **real-time data pipelines** aur **streaming applications** banane ke liye use karte hain. Socho ek post office ki tarah — koi letter (message) bhejta hai, post office (Kafka broker) usse store karta hai, aur receiver (consumer) apni marzi se letter le leta hai.

### 📝 English Interview Answer:
> Apache Kafka is a distributed, fault-tolerant, high-throughput event streaming platform. It is used for building real-time data pipelines and streaming applications. Kafka follows a publish-subscribe messaging model where producers publish messages to topics and consumers subscribe to those topics to process messages.

### Key Terminology:

| Term | Hindi | English |
|------|-------|---------|
| **Producer** | Message bhejne wala | Application that sends messages to Kafka |
| **Consumer** | Message padhne wala | Application that reads messages from Kafka |
| **Broker** | Kafka server jo messages store karta hai | A Kafka server that stores messages |
| **Topic** | Category jisme messages store hote hain | A named feed/category of messages |
| **Partition** | Topic ka ek hissa | A subset of a topic for parallelism |
| **Offset** | Partition me message ka position number | Unique ID of a message within a partition |
| **Zookeeper/KRaft** | Kafka cluster ko manage karta hai | Manages Kafka cluster metadata |

---

## 2. Kafka Architecture

### 🇮🇳 Hindi Explanation:
> Kafka ka architecture bohot simple hai:
> - **Producers** messages bhejte hain topics me
> - **Brokers** (servers) messages store karte hain
> - **Topics** me multiple **partitions** hoti hain (parallel processing ke liye)
> - **Consumers** messages padhte hain partitions se
> - **Consumer Group** me multiple consumers milke ek topic ko process karte hain
> - Har partition me har message ka ek unique **offset** hota hai

### 📝 English Interview Answer:
> Kafka follows a distributed architecture with producers, brokers, topics, partitions, and consumers. Brokers form a cluster managed by ZooKeeper (or KRaft in newer versions). Topics are divided into partitions for parallelism and scalability. Each partition is an ordered, immutable sequence of messages. Consumers in a consumer group divide partition ownership for load balancing.

```
┌──────────┐     ┌─────────────────────────────┐     ┌──────────┐
│ Producer │────▶│     Kafka Cluster            │────▶│ Consumer │
│          │     │  ┌───────┐  ┌───────┐       │     │  Group   │
│ Producer │────▶│  │Broker1│  │Broker2│       │────▶│ Consumer │
│          │     │  │Topic-A│  │Topic-A│       │     │          │
│ Producer │────▶│  │Part-0 │  │Part-1 │       │────▶│ Consumer │
└──────────┘     │  └───────┘  └───────┘       │     └──────────┘
                 └─────────────────────────────┘
```

### Replication:
```
Topic: orders (Replication Factor = 3)

Broker 1: Partition 0 (Leader), Partition 1 (Follower)
Broker 2: Partition 0 (Follower), Partition 1 (Leader)
Broker 3: Partition 0 (Follower), Partition 1 (Follower)
```

---

## 3. Spring Boot + Kafka Setup

### 🇮🇳 Hindi Explanation:
> Spring Boot me Kafka use karna bohot easy hai. Bas dependency add karo, application.yml me configuration do, aur Producer/Consumer classes banao.

### Step 1: Add Dependency (`pom.xml`)

```xml
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

### Step 2: Application Configuration (`application.yml`)

```yaml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    consumer:
      group-id: my-group
      auto-offset-reset: earliest
      key-deserializer: org.apache.kafka.common.serialization.StringDeserializer
      value-deserializer: org.apache.kafka.common.serialization.StringDeserializer
    producer:
      key-serializer: org.apache.kafka.common.serialization.StringSerializer
      value-serializer: org.apache.kafka.common.serialization.StringSerializer
```

### Step 3: Kafka Topic Configuration

```java
@Configuration
public class KafkaTopicConfig {

    @Bean
    public NewTopic createTopic() {
        return TopicBuilder.name("my-topic")
                .partitions(3)
                .replicas(1)
                .build();
    }
}
```

---

## 4. Producer Configuration

### 🇮🇳 Hindi Explanation:
> Producer wo component hai jo messages Kafka me bhejta hai. Spring Boot me `KafkaTemplate` use hota hai messages send karne ke liye. Hum key ke saath ya bina key ke messages bhej sakte hain. Agar key dete hain to same key wale messages same partition me jayenge.

### 📝 English Interview Answer:
> In Spring Boot, `KafkaTemplate` is the primary abstraction for sending messages to Kafka. It supports synchronous and asynchronous sending, custom partitioning, and callbacks for success/failure handling.

### Simple Producer:

```java
@Service
public class KafkaProducerService {

    private final KafkaTemplate<String, String> kafkaTemplate;

    public KafkaProducerService(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    // Simple send
    public void sendMessage(String topic, String message) {
        kafkaTemplate.send(topic, message);
    }

    // Send with key (ensures ordering within partition)
    public void sendMessageWithKey(String topic, String key, String message) {
        kafkaTemplate.send(topic, key, message);
    }

    // Async send with callback
    public void sendMessageAsync(String topic, String message) {
        CompletableFuture<SendResult<String, String>> future =
                kafkaTemplate.send(topic, message);

        future.whenComplete((result, ex) -> {
            if (ex == null) {
                System.out.println("Message sent successfully: " +
                    result.getRecordMetadata().offset());
            } else {
                System.out.println("Failed to send message: " + ex.getMessage());
            }
        });
    }
}
```

### Advanced Producer Configuration:

```java
@Configuration
public class KafkaProducerConfig {

    @Bean
    public ProducerFactory<String, String> producerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class);

        // Performance tuning
        config.put(ProducerConfig.ACKS_CONFIG, "all");          // Strongest durability
        config.put(ProducerConfig.RETRIES_CONFIG, 3);           // Retry on failure
        config.put(ProducerConfig.BATCH_SIZE_CONFIG, 16384);    // Batch size in bytes
        config.put(ProducerConfig.LINGER_MS_CONFIG, 5);         // Wait before sending batch
        config.put(ProducerConfig.BUFFER_MEMORY_CONFIG, 33554432); // Buffer memory

        // Idempotent producer (no duplicates)
        config.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, true);

        return new DefaultKafkaProducerFactory<>(config);
    }

    @Bean
    public KafkaTemplate<String, String> kafkaTemplate() {
        return new KafkaTemplate<>(producerFactory());
    }
}
```

### Producer Acknowledgments (acks):

| Value | Hindi | English |
|-------|-------|---------|
| `acks=0` | Broker ka response wait nahi karta | Fire and forget, fastest but unsafe |
| `acks=1` | Sirf leader ka acknowledgment | Leader acknowledges, moderate safety |
| `acks=all` | Sab replicas ka acknowledgment | All replicas acknowledge, safest |

---

## 5. Consumer Configuration

### 🇮🇳 Hindi Explanation:
> Consumer Kafka se messages padhta hai. Spring Boot me `@KafkaListener` annotation use hota hai. Consumer ek group me hota hai — ek group me ek partition sirf ek consumer ko milti hai. Agar consumer crash ho jaye to Kafka automatically partition doosre consumer ko de deta hai (rebalancing).

### 📝 English Interview Answer:
> Spring Kafka provides `@KafkaListener` annotation to create message listeners. Consumers belong to consumer groups for load balancing. Kafka handles partition assignment and rebalancing automatically. Offset management can be automatic or manual.

### Simple Consumer:

```java
@Service
public class KafkaConsumerService {

    // Basic listener
    @KafkaListener(topics = "my-topic", groupId = "my-group")
    public void listen(String message) {
        System.out.println("Received: " + message);
    }

    // Listener with metadata
    @KafkaListener(topics = "my-topic", groupId = "my-group")
    public void listenWithMetadata(
            @Payload String message,
            @Header(KafkaHeaders.RECEIVED_PARTITION) int partition,
            @Header(KafkaHeaders.OFFSET) long offset,
            @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {

        System.out.printf("Topic: %s, Partition: %d, Offset: %d, Message: %s%n",
                topic, partition, offset, message);
    }

    // Batch listener
    @KafkaListener(topics = "my-topic", groupId = "batch-group",
                   containerFactory = "batchFactory")
    public void listenBatch(List<String> messages) {
        System.out.println("Batch size: " + messages.size());
        messages.forEach(msg -> System.out.println("Message: " + msg));
    }
}
```

### Advanced Consumer Configuration:

```java
@Configuration
@EnableKafka
public class KafkaConsumerConfig {

    @Bean
    public ConsumerFactory<String, String> consumerFactory() {
        Map<String, Object> config = new HashMap<>();
        config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        config.put(ConsumerConfig.GROUP_ID_CONFIG, "my-group");
        config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
        config.put(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        config.put(ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG, false); // Manual commit
        config.put(ConsumerConfig.MAX_POLL_RECORDS_CONFIG, 100);
        return new DefaultKafkaConsumerFactory<>(config);
    }

    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> kafkaListenerContainerFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        factory.setConcurrency(3); // 3 consumer threads
        factory.getContainerProperties().setAckMode(ContainerProperties.AckMode.MANUAL_IMMEDIATE);
        return factory;
    }

    // Batch listener factory
    @Bean
    public ConcurrentKafkaListenerContainerFactory<String, String> batchFactory() {
        ConcurrentKafkaListenerContainerFactory<String, String> factory =
                new ConcurrentKafkaListenerContainerFactory<>();
        factory.setConsumerFactory(consumerFactory());
        factory.setBatchListener(true);
        return factory;
    }
}
```

### Manual Offset Commit:

```java
@KafkaListener(topics = "my-topic", groupId = "manual-group")
public void listenManualAck(String message, Acknowledgment acknowledgment) {
    try {
        processMessage(message);
        acknowledgment.acknowledge(); // Commit offset after successful processing
    } catch (Exception e) {
        // Don't acknowledge - message will be redelivered
        log.error("Processing failed", e);
    }
}
```

---

## 6. Topics, Partitions & Offsets

### 🇮🇳 Hindi Explanation:
> - **Topic**: Ek category hai messages ki (jaise "orders", "payments")
> - **Partition**: Topic ka hissa — parallel processing ke liye. Zyada partitions = zyada throughput
> - **Offset**: Har message ka unique number partition me. Consumer offset track karta hai ki kahan tak padha hai
> - Messages partition me **append-only** hote hain — ek baar likha to change nahi hoga
> - **Retention policy** decide karta hai ki messages kitne din tak stored rahenge

### 📝 English Interview Answer:
> Topics are logical channels for messages. Each topic is divided into partitions for parallelism. Within a partition, each message has a unique sequential offset. Partitions are distributed across brokers for load balancing. Message ordering is guaranteed only within a single partition.

### Topic Management:

```java
@Configuration
public class KafkaTopicConfig {

    @Bean
    public KafkaAdmin.NewTopics topics() {
        return new KafkaAdmin.NewTopics(
            TopicBuilder.name("orders")
                .partitions(6)
                .replicas(3)
                .config(TopicConfig.RETENTION_MS_CONFIG, "604800000") // 7 days
                .config(TopicConfig.CLEANUP_POLICY_CONFIG, "delete")
                .build(),
            TopicBuilder.name("user-events")
                .partitions(3)
                .replicas(2)
                .compact() // Log compaction
                .build()
        );
    }
}
```

### Partition Strategy:

```
Messages with Key:
  key = "user-123" → hash("user-123") % numPartitions → Partition 2
  key = "user-456" → hash("user-456") % numPartitions → Partition 0

Messages without Key:
  Round-robin assignment across partitions (Kafka 2.4+: sticky partitioner)
```

### Custom Partitioner:

```java
public class CustomPartitioner implements Partitioner {

    @Override
    public int partition(String topic, Object key, byte[] keyBytes,
                         Object value, byte[] valueBytes, Cluster cluster) {
        List<PartitionInfo> partitions = cluster.partitionsForTopic(topic);
        int numPartitions = partitions.size();

        if (key == null) {
            return ThreadLocalRandom.current().nextInt(numPartitions);
        }

        // VIP customers go to partition 0
        if (key.toString().startsWith("VIP")) {
            return 0;
        }

        return Math.abs(key.hashCode()) % numPartitions;
    }

    @Override public void close() {}
    @Override public void configure(Map<String, ?> configs) {}
}
```

---

## 7. Consumer Groups

### 🇮🇳 Hindi Explanation:
> Consumer Group ek concept hai jisme multiple consumers milke ek topic ko process karte hain.
> - Ek partition sirf ek consumer ko milti hai (ek group me)
> - Agar consumers zyada hain partitions se, to kuch idle rahenge
> - Agar koi consumer fail ho jaye, to uski partitions doosron ko mil jayengi (**rebalancing**)
> - Alag-alag groups independently messages padh sakte hain

### 📝 English Interview Answer:
> A consumer group is a set of consumers that cooperatively consume from a topic. Each partition is assigned to exactly one consumer within a group. If a consumer fails, Kafka rebalances partitions among remaining consumers. Different consumer groups read independently, enabling multiple applications to process the same messages.

```
Topic: orders (6 partitions)

Consumer Group A (3 consumers):
  Consumer-1: Partition 0, 1
  Consumer-2: Partition 2, 3
  Consumer-3: Partition 4, 5

Consumer Group B (2 consumers):
  Consumer-1: Partition 0, 1, 2
  Consumer-2: Partition 3, 4, 5
```

### Rebalancing Strategies:

| Strategy | Hindi | English |
|----------|-------|---------|
| **Eager** | Sab partitions chhod do, phir reassign karo | Revoke all, then reassign |
| **Cooperative (Incremental)** | Sirf affected partitions reassign karo | Only reassign affected partitions |

```java
// Use cooperative rebalancing
config.put(ConsumerConfig.PARTITION_ASSIGNMENT_STRATEGY_CONFIG,
    CooperativeStickyAssignor.class.getName());
```

---

## 8. Serialization & Deserialization

### 🇮🇳 Hindi Explanation:
> Kafka me messages bytes me store hote hain. Isliye Producer ko message ko bytes me convert karna padta hai (Serialization) aur Consumer ko bytes se wapas object banana padta hai (Deserialization). Hum String, JSON, Avro, Protobuf — koi bhi format use kar sakte hain.

### 📝 English Interview Answer:
> Kafka stores messages as byte arrays. Serializers convert objects to bytes (producer-side) and deserializers convert bytes back to objects (consumer-side). Common formats include String, JSON (using Jackson), Avro (with Schema Registry), and Protobuf.

### JSON Serialization:

```java
// DTO
@Data
@NoArgsConstructor
@AllArgsConstructor
public class OrderEvent {
    private String orderId;
    private String customerId;
    private BigDecimal amount;
    private LocalDateTime timestamp;
}
```

```java
// Producer Config for JSON
@Bean
public ProducerFactory<String, OrderEvent> producerFactory() {
    Map<String, Object> config = new HashMap<>();
    config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    config.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
    config.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
    return new DefaultKafkaProducerFactory<>(config);
}
```

```java
// Consumer Config for JSON
@Bean
public ConsumerFactory<String, OrderEvent> consumerFactory() {
    Map<String, Object> config = new HashMap<>();
    config.put(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    config.put(ConsumerConfig.GROUP_ID_CONFIG, "order-group");
    config.put(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class);
    config.put(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, JsonDeserializer.class);
    config.put(JsonDeserializer.TRUSTED_PACKAGES, "com.example.dto");
    config.put(JsonDeserializer.VALUE_DEFAULT_TYPE, OrderEvent.class.getName());
    return new DefaultKafkaConsumerFactory<>(config);
}
```

```java
// Producer
@Service
public class OrderProducer {
    private final KafkaTemplate<String, OrderEvent> kafkaTemplate;

    public void sendOrder(OrderEvent order) {
        kafkaTemplate.send("orders", order.getOrderId(), order);
    }
}

// Consumer
@Service
public class OrderConsumer {
    @KafkaListener(topics = "orders", groupId = "order-group")
    public void consume(OrderEvent order) {
        System.out.println("Order received: " + order.getOrderId());
    }
}
```

---

## 9. Error Handling & Retry

### 🇮🇳 Hindi Explanation:
> Production me errors aana common hai — network issue, database down, invalid message, etc. Spring Kafka me hum:
> - **Retry** kar sakte hain (3-4 baar try karo)
> - **Dead Letter Topic (DLT)** me fail messages bhej sakte hain (baad me manually fix karenge)
> - **Error Handler** set kar sakte hain jo decide kare ki kya karna hai error pe

### 📝 English Interview Answer:
> Spring Kafka provides robust error handling through `DefaultErrorHandler` with configurable retry policies (fixed/exponential backoff) and Dead Letter Topic (DLT) publishing. Failed messages can be sent to a DLT for later analysis and reprocessing.

### Retry + Dead Letter Topic:

```java
@Configuration
public class KafkaErrorConfig {

    @Bean
    public DefaultErrorHandler errorHandler(KafkaTemplate<String, Object> template) {
        // Send to DLT after retries exhausted
        DeadLetterPublishingRecoverer recoverer =
                new DeadLetterPublishingRecoverer(template,
                    (record, ex) -> new TopicPartition(record.topic() + ".DLT", -1));

        // Retry 3 times with exponential backoff
        ExponentialBackOff backOff = new ExponentialBackOff(1000L, 2.0);
        backOff.setMaxElapsedTime(10000L); // Max 10 seconds

        DefaultErrorHandler errorHandler = new DefaultErrorHandler(recoverer, backOff);

        // Don't retry for these exceptions
        errorHandler.addNotRetryableExceptions(
            DeserializationException.class,
            IllegalArgumentException.class
        );

        return errorHandler;
    }
}
```

### Retry Topic Pattern (Spring Kafka 3+):

```java
@RetryableTopic(
    attempts = "4",
    backoff = @Backoff(delay = 1000, multiplier = 2.0),
    autoCreateTopics = "true",
    topicSuffixingStrategy = TopicSuffixingStrategy.SUFFIX_WITH_INDEX_VALUE
)
@KafkaListener(topics = "orders", groupId = "order-group")
public void consume(OrderEvent order) {
    // If this throws, Spring will retry with backoff
    processOrder(order);
}

@DltHandler
public void handleDlt(OrderEvent order, @Header(KafkaHeaders.RECEIVED_TOPIC) String topic) {
    log.error("DLT received message on topic {}: {}", topic, order);
    // Save to DB for manual review
}
```

---

## 10. Kafka with Avro & Schema Registry

### 🇮🇳 Hindi Explanation:
> Avro ek binary serialization format hai jo schema ke saath kaam karta hai. **Schema Registry** ek central server hai jahan schemas store hote hain. Iska fayda:
> - Schema evolution support (naye fields add kar sakte ho bina break kiye)
> - Compact binary format (JSON se chhota)
> - Producer aur Consumer ke beech contract enforce hota hai

### 📝 English Interview Answer:
> Avro with Schema Registry provides schema evolution, compact serialization, and contract enforcement between producers and consumers. The Schema Registry stores versioned schemas and validates compatibility (backward, forward, full).

### Dependencies:

```xml
<dependency>
    <groupId>io.confluent</groupId>
    <artifactId>kafka-avro-serializer</artifactId>
    <version>7.5.0</version>
</dependency>
```

### Avro Schema (`order.avsc`):

```json
{
  "type": "record",
  "name": "Order",
  "namespace": "com.example.avro",
  "fields": [
    {"name": "orderId", "type": "string"},
    {"name": "amount", "type": "double"},
    {"name": "status", "type": {"type": "enum", "name": "Status", "symbols": ["PENDING", "COMPLETED", "CANCELLED"]}},
    {"name": "notes", "type": ["null", "string"], "default": null}
  ]
}
```

### Configuration:

```yaml
spring:
  kafka:
    properties:
      schema.registry.url: http://localhost:8081
    producer:
      value-serializer: io.confluent.kafka.serializers.KafkaAvroSerializer
    consumer:
      value-deserializer: io.confluent.kafka.serializers.KafkaAvroDeserializer
      properties:
        specific.avro.reader: true
```

---

## 11. Kafka Streams

### 🇮🇳 Hindi Explanation:
> Kafka Streams ek library hai jo Kafka me real-time data processing karti hai. Isme hum messages ko filter, transform, aggregate, join kar sakte hain — bina kisi extra framework (Spark/Flink) ke. Ye lightweight hai aur Spring Boot me directly integrate hoti hai.

### 📝 English Interview Answer:
> Kafka Streams is a client library for building real-time stream processing applications. It supports stateless operations (filter, map, flatMap) and stateful operations (aggregations, joins, windowing). It's lightweight, requires no separate cluster, and processes data exactly-once.

### Dependencies:

```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-streams</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.kafka</groupId>
    <artifactId>spring-kafka</artifactId>
</dependency>
```

### Stream Processing Example:

```java
@Configuration
@EnableKafkaStreams
public class KafkaStreamsConfig {

    @Bean(name = KafkaStreamsDefaultConfiguration.DEFAULT_STREAMS_CONFIG_BEAN_NAME)
    public KafkaStreamsConfiguration kafkaStreamsConfig() {
        Map<String, Object> props = new HashMap<>();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "order-streams-app");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        props.put(StreamsConfig.DEFAULT_KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        props.put(StreamsConfig.DEFAULT_VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass());
        return new KafkaStreamsConfiguration(props);
    }

    @Bean
    public KStream<String, String> kStream(StreamsBuilder builder) {
        KStream<String, String> stream = builder.stream("input-topic");

        // Filter + Transform + Send to output topic
        stream
            .filter((key, value) -> value.contains("important"))
            .mapValues(value -> value.toUpperCase())
            .to("output-topic");

        // Word count with state store
        stream
            .flatMapValues(value -> Arrays.asList(value.split("\\s+")))
            .groupBy((key, word) -> word)
            .count(Materialized.as("word-count-store"))
            .toStream()
            .to("word-count-output", Produced.with(Serdes.String(), Serdes.Long()));

        return stream;
    }
}
```

### Windowed Aggregation:

```java
stream
    .groupByKey()
    .windowedBy(TimeWindows.ofSizeWithNoGrace(Duration.ofMinutes(5)))
    .count()
    .toStream()
    .map((windowedKey, count) ->
        KeyValue.pair(windowedKey.key(), "Count in window: " + count))
    .to("windowed-output");
```

---

## 12. Kafka Transactions

### 🇮🇳 Hindi Explanation:
> Kafka Transactions ensure karte hain ki **exactly-once processing** ho. Matlab ek message ya to completely process hoga ya bilkul nahi. Ye banking/payment systems me bahut important hai. Spring Boot me `@Transactional` annotation ya `KafkaTemplate.executeInTransaction()` se hum transactions use kar sakte hain.

### 📝 English Interview Answer:
> Kafka supports exactly-once semantics (EOS) through idempotent producers and transactions. Transactions allow atomic writes across multiple topics/partitions and atomic read-process-write patterns. Spring Kafka integrates this with `@Transactional` support.

```java
// Producer config for transactions
@Bean
public ProducerFactory<String, String> producerFactory() {
    Map<String, Object> config = new HashMap<>();
    config.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
    config.put(ProducerConfig.TRANSACTIONAL_ID_CONFIG, "tx-"); // Enable transactions
    config.put(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, true);
    return new DefaultKafkaProducerFactory<>(config);
}

// Transactional send
@Service
public class TransactionalProducer {
    private final KafkaTemplate<String, String> kafkaTemplate;

    @Transactional
    public void sendMultipleMessages(String orderId, OrderEvent order) {
        // Both succeed or both fail atomically
        kafkaTemplate.send("orders", orderId, order.toString());
        kafkaTemplate.send("audit-log", orderId, "Order created: " + orderId);
    }

    // OR using executeInTransaction
    public void sendWithTransaction() {
        kafkaTemplate.executeInTransaction(ops -> {
            ops.send("topic1", "msg1");
            ops.send("topic2", "msg2");
            return true;
        });
    }
}
```

---

## 13. Kafka Security

### 🇮🇳 Hindi Explanation:
> Production me Kafka secure karna zaruri hai:
> - **SSL/TLS**: Data encrypt hota hai network pe (koi beech me padh nahi sakta)
> - **SASL**: Authentication — sirf authorized clients connect kar sakte hain
> - **ACLs**: Authorization — kaun kya kar sakta hai (read/write/create)

### 📝 English Interview Answer:
> Kafka supports SSL/TLS for encryption in transit, SASL (PLAIN, SCRAM, GSSAPI/Kerberos, OAUTHBEARER) for authentication, and ACLs for authorization. In production, all three should be configured for a secure deployment.

### SSL Configuration:

```yaml
spring:
  kafka:
    security:
      protocol: SSL
    ssl:
      trust-store-location: classpath:truststore.jks
      trust-store-password: changeit
      key-store-location: classpath:keystore.jks
      key-store-password: changeit
      key-password: changeit
```

### SASL Configuration:

```yaml
spring:
  kafka:
    properties:
      security.protocol: SASL_SSL
      sasl.mechanism: SCRAM-SHA-256
      sasl.jaas.config: >
        org.apache.kafka.common.security.scram.ScramLoginModule required
        username="myuser"
        password="mypassword";
```

---

## 14. Kafka Monitoring & Performance Tuning

### 🇮🇳 Hindi Explanation:
> Kafka ko monitor karna bahut important hai production me:
> - **Consumer Lag**: Kitne messages pending hain consumer ke paas (zyada lag = slow consumer)
> - **Throughput**: Kitne messages per second process ho rahe hain
> - **JMX Metrics**: Kafka expose karta hai metrics JMX ke through
> - Tools: Prometheus + Grafana, Kafka Manager, Conduktor

### 📝 English Interview Answer:
> Key Kafka metrics include consumer lag, throughput, request latency, and under-replicated partitions. Monitoring tools like Prometheus with JMX Exporter and Grafana dashboards are commonly used. Performance tuning involves optimizing batch size, linger.ms, compression, partition count, and consumer parallelism.

### Spring Boot Actuator + Micrometer:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health, metrics, kafka
  metrics:
    tags:
      application: my-kafka-app
```

### Performance Tuning Cheat Sheet:

| Parameter | Tuning Tip |
|-----------|-----------|
| `batch.size` | Increase for throughput (default 16KB) |
| `linger.ms` | Add small delay (5-10ms) to fill batches |
| `compression.type` | Use `lz4` or `snappy` for compression |
| `buffer.memory` | Increase if producer blocks often |
| `fetch.min.bytes` | Increase for consumer throughput |
| `max.poll.records` | Control batch size per poll |
| `concurrency` | Match with partition count |
| `partitions` | More partitions = more parallelism |

### Consumer Lag Monitoring:

```java
@Component
public class ConsumerLagMonitor {

    @Autowired
    private KafkaListenerEndpointRegistry registry;

    @Scheduled(fixedRate = 30000)
    public void checkLag() {
        registry.getListenerContainers().forEach(container -> {
            Map<String, Map<MetricName, ? extends Metric>> metrics = container.metrics();
            // Log or expose metrics
        });
    }
}
```

---

## 15. Interview Questions & Answers

### 🔰 Basic Level

**Q1: What is Apache Kafka?**
> Kafka is a distributed, fault-tolerant, high-throughput event streaming platform used for building real-time data pipelines. It follows pub-sub model with persistent storage.

**Q2: What is a Kafka Topic?**
> A topic is a named category/feed to which records are published. Topics are split into partitions for parallelism and distributed across brokers.

**Q3: What is a Partition?**
> A partition is an ordered, immutable sequence of records within a topic. Each record in a partition has a unique offset. Ordering is guaranteed only within a partition.

**Q4: What is a Consumer Group?**
> A consumer group is a set of consumers sharing the same group.id that cooperatively consume a topic. Each partition is consumed by exactly one consumer in the group.

**Q5: What is an Offset?**
> An offset is a unique sequential ID assigned to each record within a partition. Consumers track offsets to know which messages they've processed.

---

### 🔥 Intermediate Level

**Q6: How does Kafka ensure message ordering?**
> Kafka guarantees ordering within a partition only. To ensure ordering for related messages, use the same key (all messages with the same key go to the same partition via consistent hashing).

**Q7: What is Consumer Rebalancing?**
> When a consumer joins/leaves a group or new partitions are added, Kafka reassigns partitions among consumers. During rebalancing, consumption pauses briefly. Cooperative Sticky Assignor minimizes disruption.

**Q8: Difference between `at-most-once`, `at-least-once`, and `exactly-once`?**
> - **At-most-once**: Commit offset before processing. May lose messages.
> - **At-least-once**: Commit after processing. May get duplicates.
> - **Exactly-once**: Use transactions + idempotent producer. No loss, no duplicates.

**Q9: What is ISR (In-Sync Replicas)?**
> ISR is the set of replicas that are fully caught up with the leader. Only ISR members can become the new leader if the current leader fails. If a follower falls behind, it's removed from ISR.

**Q10: How to handle poison pill (bad message)?**
> Use a `DeserializationException` handler or `ErrorHandlingDeserializer`. Route unprocessable messages to a Dead Letter Topic (DLT) for investigation.

---

### 🚀 Advanced Level

**Q11: How does Kafka achieve high throughput?**
> - Sequential I/O (append-only log)
> - Zero-copy transfer (sendfile syscall)
> - Batching (producer batches messages)
> - Compression (reduce network I/O)
> - Page cache utilization (OS-level caching)
> - Partitioning (parallel processing)

**Q12: Explain Kafka's exactly-once semantics (EOS).**
> EOS is achieved through:
> 1. **Idempotent Producer**: Each message gets a sequence number; broker deduplicates.
> 2. **Transactions**: Atomic writes across multiple partitions.
> 3. **Consumer**: `isolation.level=read_committed` to only read committed messages.
> Combined, these ensure each message is processed exactly once end-to-end.

**Q13: What is Log Compaction?**
> Log compaction retains only the latest value for each key in a partition. Old records with the same key are removed during compaction. Useful for changelogs and state stores (e.g., KTable).

**Q14: Kafka vs RabbitMQ vs ActiveMQ?**

| Feature | Kafka | RabbitMQ | ActiveMQ |
|---------|-------|----------|----------|
| Model | Log-based pub-sub | Queue + Exchange routing | Queue-based JMS |
| Throughput | Very High (millions/sec) | Moderate | Low-Moderate |
| Ordering | Per partition | Per queue | Per queue |
| Retention | Configurable (days/forever) | Until consumed | Until consumed |
| Replay | Yes (offset-based) | No | Limited |
| Use case | Event streaming, logs | Task queues, RPC | Enterprise JMS |

**Q15: How to handle Kafka Consumer Lag in production?**
> 1. Monitor lag with tools (Burrow, Prometheus)
> 2. Increase partitions + consumers for parallelism
> 3. Optimize consumer processing time
> 4. Use batch processing (`max.poll.records`)
> 5. Scale consumer instances horizontally
> 6. Check for GC pauses, network issues
> 7. Consider async processing with internal queues

**Q16: What happens when a Kafka Broker goes down?**
> - Partitions where it was leader → leader election from ISR on other brokers
> - Partitions where it was follower → removed from ISR
> - Producers/Consumers automatically reconnect to new leaders
> - If `min.insync.replicas` isn't met → producers with `acks=all` will get errors

**Q17: Explain KRaft mode (Kafka without ZooKeeper).**
> KRaft (Kafka Raft) replaces ZooKeeper for metadata management. Controller nodes use Raft consensus protocol. Benefits: simpler deployment, faster recovery, better scalability, single security model.

**Q18: Design a Kafka-based Order Processing System.**
```
Producer (Order Service) → Topic: "orders" (partitioned by customerId)
    ↓
Consumer Group 1: Payment Service → Topic: "payments"
    ↓
Consumer Group 2: Inventory Service → Topic: "inventory-updates"
    ↓
Consumer Group 3: Notification Service → sends email/SMS

DLT: "orders.DLT" for failed messages
Schema Registry for Avro schemas
Exactly-once with transactions for payment processing
```

---

## 🐳 Docker Compose for Local Development

```yaml
version: '3.8'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:7.5.0
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181

  kafka:
    image: confluentinc/cp-kafka:7.5.0
    depends_on:
      - zookeeper
    ports:
      - "9092:9092"
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://localhost:9092
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1

  schema-registry:
    image: confluentinc/cp-schema-registry:7.5.0
    depends_on:
      - kafka
    ports:
      - "8081:8081"
    environment:
      SCHEMA_REGISTRY_HOST_NAME: schema-registry
      SCHEMA_REGISTRY_KAFKASTORE_BOOTSTRAP_SERVERS: kafka:9092

  kafka-ui:
    image: provectuslabs/kafka-ui:latest
    ports:
      - "8080:8080"
    environment:
      KAFKA_CLUSTERS_0_NAME: local
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
      KAFKA_CLUSTERS_0_SCHEMAREGISTRY: http://schema-registry:8081
```

---

## 📚 Quick Reference Commands

```bash
# Start Kafka (from Kafka directory)
bin/kafka-server-start.sh config/server.properties

# Create topic
bin/kafka-topics.sh --create --topic my-topic --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1

# List topics
bin/kafka-topics.sh --list --bootstrap-server localhost:9092

# Describe topic
bin/kafka-topics.sh --describe --topic my-topic --bootstrap-server localhost:9092

# Console producer
bin/kafka-console-producer.sh --topic my-topic --bootstrap-server localhost:9092

# Console consumer
bin/kafka-console-consumer.sh --topic my-topic --from-beginning --bootstrap-server localhost:9092

# Consumer groups
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
bin/kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-group
```

---

## ✅ Summary Cheat Sheet

| Concept | Key Point |
|---------|-----------|
| Topic | Logical channel for messages |
| Partition | Unit of parallelism & ordering |
| Offset | Message position in partition |
| Consumer Group | Load balancing across consumers |
| Replication | Fault tolerance (leader + followers) |
| ISR | In-sync replicas eligible for leadership |
| Idempotent Producer | No duplicate messages |
| Transactions | Atomic multi-partition writes |
| Exactly-Once | Idempotent + Transactions + read_committed |
| Schema Registry | Schema versioning & compatibility |
| Kafka Streams | Real-time stream processing library |
| KRaft | ZooKeeper-free Kafka (Raft consensus) |

---

> 📝 **Author Note**: These notes cover Kafka from absolute basics to production-level advanced concepts. Practice each section with the Docker Compose setup provided above.

---
