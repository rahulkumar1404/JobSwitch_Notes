# 📘 Apache Kafka - Complete Course Notes (Hindi + English + Code)

> Based on: [Apache Kafka Series - Learn Apache Kafka for Beginners v3](https://cognizant.udemy.com/course/apache-kafka/)

---

## 📑 Table of Contents

1. [Kafka Introduction](#1-kafka-introduction)
2. [Topics, Partitions & Offsets](#2-topics-partitions--offsets)
3. [Producers & Message Keys](#3-producers--message-keys)
4. [Consumers & Deserialization](#4-consumers--deserialization)
5. [Consumer Groups & Offsets](#5-consumer-groups--offsets)
6. [Brokers & Clusters](#6-brokers--clusters)
7. [Topic Replication](#7-topic-replication)
8. [Producer Acknowledgements (acks)](#8-producer-acknowledgements-acks)
9. [Zookeeper](#9-zookeeper)
10. [KRaft Mode (No Zookeeper)](#10-kraft-mode-no-zookeeper)
11. [Kafka CLI - kafka-topics](#11-kafka-cli---kafka-topics)
12. [Kafka CLI - kafka-console-producer](#12-kafka-cli---kafka-console-producer)
13. [Kafka CLI - kafka-console-consumer](#13-kafka-cli---kafka-console-consumer)
14. [Kafka CLI - kafka-consumer-groups](#14-kafka-cli---kafka-consumer-groups)
15. [Java Producer](#15-java-producer)
16. [Java Producer with Keys](#16-java-producer-with-keys)
17. [Java Producer with Callback](#17-java-producer-with-callback)
18. [Java Consumer](#18-java-consumer)
19. [Java Consumer in Group](#19-java-consumer-in-group)
20. [Consumer Rebalancing](#20-consumer-rebalancing)
21. [Kafka Connect](#21-kafka-connect)
22. [Kafka Streams](#22-kafka-streams)
23. [Schema Registry](#23-schema-registry)
24. [Real World Architecture](#24-real-world-architecture)
25. [Topic Configuration & Log Compaction](#25-topic-configuration--log-compaction)
26. [Idempotent Producer](#26-idempotent-producer)
27. [Kafka Security (SSL/SASL)](#27-kafka-security-sslsasl)

---

## 1. Kafka Introduction

**Hindi:** Apache Kafka ek distributed streaming platform hai jo real-time data ko publish, subscribe, store aur process karta hai. Ye LinkedIn ne banaya tha aur ab Apache Foundation maintain karti hai.

**English Model Answer:**
Apache Kafka is a distributed event streaming platform used to build real-time data pipelines and streaming applications. It is horizontally scalable, fault-tolerant, and capable of handling trillions of events per day.

**Use Cases:** Messaging, Activity Tracking, Log Aggregation, Stream Processing, Microservices Communication.

---

## 2. Topics, Partitions & Offsets

**Hindi:** Topic ek category hai jisme messages store hote hain. Har topic ke andar partitions hote hain jo data ko parallel process karne mein madad karte hain. Har message ka ek unique offset (ID) hota hai partition ke andar.

**English Model Answer:**
A **Topic** is a named feed of messages. Topics are split into **Partitions** for parallelism. Each message within a partition gets a unique incremental ID called an **Offset**. Offsets are immutable and messages are ordered only within a partition.

```
Topic: "orders"
├── Partition 0: [offset 0] [offset 1] [offset 2] ...
├── Partition 1: [offset 0] [offset 1] ...
└── Partition 2: [offset 0] [offset 1] [offset 2] [offset 3] ...
```

---

## 3. Producers & Message Keys

**Hindi:** Producer wo component hai jo Kafka topic mein data bhejta hai. Agar message ke saath key di jaaye toh same key wale messages hamesha same partition mein jayenge (ordering guarantee).

**English Model Answer:**
A **Producer** writes data to topics. If a **message key** is provided (e.g., `user_id`), Kafka uses a hashing mechanism (partitioner) to always send messages with the same key to the same partition, ensuring ordering for that key.

```java
// Producer sending message with key
ProducerRecord<String, String> record = 
    new ProducerRecord<>("orders", "user_123", "order_data_here");
producer.send(record);
```

---

## 4. Consumers & Deserialization

**Hindi:** Consumer wo component hai jo topic se data padhta hai. Data binary format mein aata hai, isliye deserializer use hota hai jaise StringDeserializer ya JsonDeserializer jo bytes ko readable format mein convert karta hai.

**English Model Answer:**
A **Consumer** reads data from topics. Data in Kafka is stored as bytes, so consumers use **Deserializers** to convert bytes back to objects (e.g., `StringDeserializer`, `IntegerDeserializer`). Consumers read from partitions in order (by offset).

```java
Properties props = new Properties();
props.setProperty(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
props.setProperty(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
```

---

## 5. Consumer Groups & Offsets

**Hindi:** Consumer Group mein multiple consumers milke ek topic ke partitions ko divide karke read karte hain. Kafka offsets commit karta hai taaki agar consumer restart ho toh wahi se reading shuru ho jahan chhoda tha.

**English Model Answer:**
A **Consumer Group** allows multiple consumers to share the work of reading from a topic. Each partition is consumed by exactly one consumer in the group. Kafka stores committed **offsets** in an internal topic `__consumer_offsets`, enabling consumers to resume from where they left off.

```
Topic (3 partitions) + Consumer Group (3 consumers)
├── Partition 0 → Consumer 1
├── Partition 1 → Consumer 2
└── Partition 2 → Consumer 3
```

---

## 6. Brokers & Clusters

**Hindi:** Broker ek Kafka server hai jo data store karta hai. Multiple brokers milke ek cluster banate hain. Har broker ke paas kuch partitions hoti hain aur ek broker controller ka kaam karta hai.

**English Model Answer:**
A **Broker** is a Kafka server that receives, stores, and serves data. A **Kafka Cluster** is composed of multiple brokers (identified by IDs). Each broker contains certain topic partitions. One broker acts as the **Controller** managing partition leadership and administrative tasks.

---

## 7. Topic Replication

**Hindi:** Kafka mein har partition ki copies (replicas) alag brokers pe hoti hain taaki koi broker fail ho jaye toh data safe rahe. Ek partition ka leader hota hai jo read/write handle karta hai, baaki ISR (In-Sync Replicas) backup hote hain.

**English Model Answer:**
**Topic Replication Factor** (e.g., 3) means each partition is replicated across multiple brokers. One replica is the **Leader** (handles all reads/writes), and others are **ISR (In-Sync Replicas)**. If the leader broker fails, an ISR becomes the new leader — ensuring fault tolerance.

```
Partition 0: Leader on Broker 1, Replicas on Broker 2 & Broker 3
```

---

## 8. Producer Acknowledgements (acks)

**Hindi:** Producer ko batana hota hai ki kitna confirmation chahiye message successful hone ka. `acks=0` (no wait), `acks=1` (leader confirm), `acks=all` (sab replicas confirm - safest).

**English Model Answer:**
- `acks=0`: Producer doesn't wait for acknowledgment (possible data loss, fastest).
- `acks=1`: Producer waits for leader acknowledgment (limited data loss).
- `acks=all` (or `-1`): Producer waits for leader + all ISR replicas (no data loss, safest but slowest).

```java
props.setProperty(ProducerConfig.ACKS_CONFIG, "all");
```

---

## 9. Zookeeper

**Hindi:** Zookeeper Kafka cluster ko manage karta tha - brokers track karna, leader election karna, topic metadata store karna. Lekin ab Kafka isse hata raha hai (KRaft mode).

**English Model Answer:**
**Zookeeper** was used to manage broker metadata, perform leader elections, and track cluster membership. It runs as a separate ensemble (odd number of servers: 3, 5, 7). Since Kafka 3.x+, Zookeeper is being replaced by **KRaft** for simplified architecture.

---

## 10. KRaft Mode (No Zookeeper)

**Hindi:** KRaft (Kafka Raft) mode mein Kafka khud apna metadata manage karta hai bina Zookeeper ke. Ye faster startup, simpler architecture aur better scalability deta hai. Kafka 4.0+ mein ye default hai.

**English Model Answer:**
**KRaft** (Kafka Raft Metadata Mode) eliminates the Zookeeper dependency. Kafka brokers themselves handle metadata management using a Raft consensus protocol. Benefits: faster shutdown/recovery, simpler deployment, better scalability. Production-ready since Kafka 3.3+.

```bash
# Generate cluster ID
kafka-storage.sh random-uuid

# Format storage for KRaft
kafka-storage.sh format -t <uuid> -c config/kraft/server.properties

# Start Kafka in KRaft mode
kafka-server-start.sh config/kraft/server.properties
```

---

## 11. Kafka CLI - kafka-topics

**Hindi:** `kafka-topics` command se hum topics create, list, describe aur delete kar sakte hain.

**English Model Answer:**
The `kafka-topics` CLI is used to manage Kafka topics.

```bash
# Create topic
kafka-topics.sh --bootstrap-server localhost:9092 --create --topic first_topic --partitions 3 --replication-factor 1

# List all topics
kafka-topics.sh --bootstrap-server localhost:9092 --list

# Describe a topic
kafka-topics.sh --bootstrap-server localhost:9092 --describe --topic first_topic

# Delete a topic
kafka-topics.sh --bootstrap-server localhost:9092 --delete --topic first_topic
```

---

## 12. Kafka CLI - kafka-console-producer

**Hindi:** Ye command line se Kafka topic mein messages bhejne ke liye use hota hai. Key ke saath ya bina key ke messages bhej sakte hain.

**English Model Answer:**
The `kafka-console-producer` sends messages to a topic from the terminal.

```bash
# Simple producer
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic
> Hello World
> My first Kafka message

# Producer with keys
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic \
  --property parse.key=true --property key.separator=:
> user1:Hello from user1
> user2:Hello from user2

# With acks=all
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic first_topic --producer-property acks=all
```

---

## 13. Kafka CLI - kafka-console-consumer

**Hindi:** Ye command se topic ke messages read kar sakte hain. `--from-beginning` lagane se saare purane messages bhi mil jayenge.

**English Model Answer:**
The `kafka-console-consumer` reads messages from a topic.

```bash
# Read new messages only
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic

# Read all messages from beginning
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --from-beginning

# Read with key, value, and timestamp
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --from-beginning \
  --formatter kafka.tools.DefaultMessageFormatter \
  --property print.timestamp=true \
  --property print.key=true \
  --property print.value=true

# Read with consumer group
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic first_topic --group my-app-group
```

---

## 14. Kafka CLI - kafka-consumer-groups

**Hindi:** Ye command se consumer groups ki details dekh sakte hain - kaun sa consumer kaunsi partition read kar raha hai, lag kitna hai, offsets reset kar sakte hain.

**English Model Answer:**
The `kafka-consumer-groups` CLI manages and monitors consumer groups.

```bash
# List all groups
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list

# Describe a group (shows lag, offsets, partitions)
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-app-group

# Reset offsets to earliest
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group my-app-group \
  --reset-offsets --to-earliest --execute --topic first_topic

# Reset offsets by shifting
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group my-app-group \
  --reset-offsets --shift-by -2 --execute --topic first_topic
```

---

## 15. Java Producer

**Hindi:** Java mein Kafka producer banane ke liye `KafkaProducer` class use hoti hai. Properties set karke producer create karo, `ProducerRecord` banao aur `send()` call karo.

**English Model Answer:**
A Java Kafka Producer sends records to topics programmatically.

```java
import org.apache.kafka.clients.producer.*;
import org.apache.kafka.common.serialization.StringSerializer;
import java.util.Properties;

public class ProducerDemo {
    public static void main(String[] args) {
        // 1. Create Producer Properties
        Properties properties = new Properties();
        properties.setProperty(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        properties.setProperty(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());
        properties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, StringSerializer.class.getName());

        // 2. Create Producer
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // 3. Create a ProducerRecord
        ProducerRecord<String, String> record = 
            new ProducerRecord<>("first_topic", "Hello Kafka from Java!");

        // 4. Send data (async)
        producer.send(record);

        // 5. Flush and close
        producer.flush();
        producer.close();
    }
}
```

**Maven Dependency:**
```xml
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka-clients</artifactId>
    <version>3.6.0</version>
</dependency>
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-simple</artifactId>
    <version>2.0.9</version>
</dependency>
```

---

## 16. Java Producer with Keys

**Hindi:** Key ke saath message bhejne se Kafka guarantee deta hai ki same key wale messages same partition mein jayenge. Ye ordering maintain karne ke liye important hai.

**English Model Answer:**
Sending messages with keys ensures messages with the same key always go to the same partition.

```java
for (int i = 0; i < 10; i++) {
    String topic = "first_topic";
    String key = "id_" + i;
    String value = "Hello World " + i;

    ProducerRecord<String, String> record = new ProducerRecord<>(topic, key, value);

    producer.send(record, (metadata, exception) -> {
        if (exception == null) {
            System.out.println("Key: " + key + " | Partition: " + metadata.partition());
        }
    });
}
// Same key always goes to same partition!
```

---

## 17. Java Producer with Callback

**Hindi:** Callback se hume pata chalta hai ki message successfully send hua ya nahi, kaunsi partition mein gaya, kya offset mila - ye debugging aur monitoring ke liye useful hai.

**English Model Answer:**
A **Callback** provides metadata about the sent message or error information.

```java
producer.send(record, new Callback() {
    @Override
    public void onCompletion(RecordMetadata metadata, Exception exception) {
        if (exception == null) {
            System.out.println("Received metadata:");
            System.out.println("Topic: " + metadata.topic());
            System.out.println("Partition: " + metadata.partition());
            System.out.println("Offset: " + metadata.offset());
            System.out.println("Timestamp: " + metadata.timestamp());
        } else {
            System.out.println("Error while producing: " + exception.getMessage());
        }
    }
});
```

---

## 18. Java Consumer

**Hindi:** Java Consumer banane ke liye `KafkaConsumer` class use hoti hai. Topic subscribe karo aur poll loop mein continuously messages read karo.

**English Model Answer:**
A Java Kafka Consumer subscribes to topics and polls for new records.

```java
import org.apache.kafka.clients.consumer.*;
import org.apache.kafka.common.serialization.StringDeserializer;
import java.time.Duration;
import java.util.*;

public class ConsumerDemo {
    public static void main(String[] args) {
        // 1. Properties
        Properties properties = new Properties();
        properties.setProperty(ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");
        properties.setProperty(ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        properties.setProperty(ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class.getName());
        properties.setProperty(ConsumerConfig.GROUP_ID_CONFIG, "my-java-app");
        properties.setProperty(ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest"); // earliest/latest/none

        // 2. Create Consumer
        KafkaConsumer<String, String> consumer = new KafkaConsumer<>(properties);

        // 3. Subscribe to topic
        consumer.subscribe(Arrays.asList("first_topic"));

        // 4. Poll for data
        while (true) {
            ConsumerRecords<String, String> records = consumer.poll(Duration.ofMillis(1000));
            for (ConsumerRecord<String, String> record : records) {
                System.out.println("Key: " + record.key());
                System.out.println("Value: " + record.value());
                System.out.println("Partition: " + record.partition());
                System.out.println("Offset: " + record.offset());
            }
        }
    }
}
```

---

## 19. Java Consumer in Group

**Hindi:** Jab ek hi group mein multiple consumers chalte hain, toh Kafka automatically partitions unke beech divide kar deta hai. Ek naya consumer add/remove hone pe rebalancing hoti hai.

**English Model Answer:**
When multiple consumers share the same `group.id`, Kafka distributes partitions among them. Adding/removing a consumer triggers a **rebalance**.

```java
// Consumer 1 and Consumer 2 both use:
properties.setProperty(ConsumerConfig.GROUP_ID_CONFIG, "my-java-app");

// If topic has 3 partitions:
// Consumer 1 → Partition 0, 1
// Consumer 2 → Partition 2
// (Kafka auto-assigns)
```

---

## 20. Consumer Rebalancing

**Hindi:** Jab consumer group mein consumer add ya remove hota hai, toh partitions dobara assign hoti hain. 3 strategies hain: Eager (sab chhod ke reassign), CooperativeSticky (sirf zaroori partitions move karo).

**English Model Answer:**
- **Eager Rebalance:** All consumers stop, all partitions are revoked and reassigned. (Short period of inactivity)
- **Cooperative Sticky:** Only a small subset of partitions are moved. Consumers can continue processing non-affected partitions.

```java
// Use Cooperative Sticky Assignor (recommended)
properties.setProperty(ConsumerConfig.PARTITION_ASSIGNMENT_STRATEGY_CONFIG, 
    CooperativeStickyAssignor.class.getName());
```

---

## 21. Kafka Connect

**Hindi:** Kafka Connect ka use hota hai bina code likhe external systems (databases, files, APIs) se data Kafka mein laane (Source) ya Kafka se bahar bhejne (Sink) ke liye. Pre-built connectors available hain.

**English Model Answer:**
**Kafka Connect** is a tool for scalable, reliable data streaming between Kafka and external systems without writing code.
- **Source Connector:** Imports data from external systems INTO Kafka (e.g., MySQL → Kafka)
- **Sink Connector:** Exports data FROM Kafka to external systems (e.g., Kafka → ElasticSearch)

```json
// Example: File Source Connector config
{
  "name": "file-source-connector",
  "config": {
    "connector.class": "org.apache.kafka.connect.file.FileStreamSourceConnector",
    "tasks.max": "1",
    "file": "/tmp/input.txt",
    "topic": "file-topic"
  }
}
```

---

## 22. Kafka Streams

**Hindi:** Kafka Streams ek Java library hai jo Kafka topics pe real-time data processing karti hai - filtering, transforming, aggregating sab bina kisi extra cluster (like Spark) ke.

**English Model Answer:**
**Kafka Streams** is a client library for building real-time stream processing applications. Data is read from Kafka topics, processed (filter, map, join, aggregate), and written back to Kafka topics.

```java
import org.apache.kafka.streams.*;
import org.apache.kafka.streams.kstream.*;

public class StreamsExample {
    public static void main(String[] args) {
        Properties props = new Properties();
        props.put(StreamsConfig.APPLICATION_ID_CONFIG, "word-count-app");
        props.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, "localhost:9092");

        StreamsBuilder builder = new StreamsBuilder();
        
        // Read from topic, filter, write to another topic
        KStream<String, String> inputStream = builder.stream("input-topic");
        KStream<String, String> filteredStream = inputStream
            .filter((key, value) -> value.contains("important"));
        filteredStream.to("output-topic");

        KafkaStreams streams = new KafkaStreams(builder.build(), props);
        streams.start();
    }
}
```

---

## 23. Schema Registry

**Hindi:** Schema Registry schemas (Avro, Protobuf, JSON Schema) ko centrally store aur manage karta hai. Ye ensure karta hai ki producer aur consumer same data format use karein, nahi toh errors aayenge.

**English Model Answer:**
**Schema Registry** provides a centralized repository for managing and validating schemas. It ensures data compatibility between producers and consumers, supporting schema evolution (backward, forward, full compatibility).

```java
// Avro Producer with Schema Registry
properties.setProperty("schema.registry.url", "http://localhost:8081");
properties.setProperty(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, 
    KafkaAvroSerializer.class.getName());
```

---

## 24. Real World Architecture

**Hindi:** Real world mein Kafka ko microservices ke beech event bus ki tarah use karte hain. Producers (apps/DBs) → Kafka Cluster → Consumers (analytics/DBs/apps). CQRS aur Event Sourcing patterns common hain.

**English Model Answer:**
A typical production Kafka architecture:

```
[Web Apps/Microservices] → Producers → KAFKA CLUSTER (multi-broker)
                                            ↓
                          ┌─────────────────┼─────────────────┐
                          ↓                 ↓                 ↓
                    [ElasticSearch]   [Analytics DB]    [Email Service]
                    (Sink Connector)  (Kafka Streams)   (Consumer App)
```

**Common Patterns:**
- Event-Driven Microservices
- CQRS (Command Query Responsibility Segregation)
- Event Sourcing
- Log Aggregation Pipeline

---

## 25. Topic Configuration & Log Compaction

**Hindi:** Topic configurations jaise `retention.ms` (data kitni der rakhe), `cleanup.policy=compact` (sirf latest value per key rakhe, baaki delete karo) production mein important hain.

**English Model Answer:**
Key topic configurations:
- `retention.ms`: How long to keep messages (default: 7 days)
- `retention.bytes`: Max size per partition
- `cleanup.policy=delete`: Delete old segments
- `cleanup.policy=compact`: **Log Compaction** — keeps only the latest value for each key

```bash
# Set retention to 1 day
kafka-configs.sh --bootstrap-server localhost:9092 --alter \
  --entity-type topics --entity-name my-topic \
  --add-config retention.ms=86400000

# Enable log compaction
kafka-configs.sh --bootstrap-server localhost:9092 --alter \
  --entity-type topics --entity-name my-topic \
  --add-config cleanup.policy=compact
```

**Log Compaction Visual:**
```
Before: key1=v1, key2=v1, key1=v2, key3=v1, key2=v2
After:  key1=v2, key3=v1, key2=v2  (only latest per key kept)
```

---

## 26. Idempotent Producer

**Hindi:** Idempotent producer ensure karta hai ki network retry ke time duplicate messages na jayein. Kafka 3.0+ mein ye by default enabled hai. Ye exactly-once delivery mein madad karta hai.

**English Model Answer:**
An **Idempotent Producer** ensures exactly-once delivery to a partition, even during retries. Kafka assigns a Producer ID (PID) and sequence number to detect and discard duplicates.

```java
// Enable idempotent producer (default in Kafka 3.0+)
properties.setProperty(ProducerConfig.ENABLE_IDEMPOTENCE_CONFIG, "true");

// Safe producer settings (recommended for production)
properties.setProperty(ProducerConfig.ACKS_CONFIG, "all");
properties.setProperty(ProducerConfig.RETRIES_CONFIG, Integer.toString(Integer.MAX_VALUE));
properties.setProperty(ProducerConfig.MAX_IN_FLIGHT_REQUESTS_PER_CONNECTION, "5");
```

---

## 27. Kafka Security (SSL/SASL)

**Hindi:** Production mein Kafka ko secure karna zaroori hai. SSL/TLS encryption (data in transit), SASL authentication (client verify karna), aur ACLs (authorization - kaun kya kar sakta hai) use hote hain.

**English Model Answer:**
Kafka supports:
- **Encryption (SSL/TLS):** Encrypt data between clients and brokers
- **Authentication (SASL):** Verify client identity (PLAIN, SCRAM, GSSAPI/Kerberos, OAUTHBEARER)
- **Authorization (ACLs):** Control who can read/write to which topics

```java
// SSL Configuration for Producer/Consumer
properties.setProperty("security.protocol", "SSL");
properties.setProperty("ssl.truststore.location", "/path/to/truststore.jks");
properties.setProperty("ssl.truststore.password", "truststore-password");
properties.setProperty("ssl.keystore.location", "/path/to/keystore.jks");
properties.setProperty("ssl.keystore.password", "keystore-password");

// SASL_SSL Configuration
properties.setProperty("security.protocol", "SASL_SSL");
properties.setProperty("sasl.mechanism", "PLAIN");
properties.setProperty("sasl.jaas.config", 
    "org.apache.kafka.common.security.plain.PlainLoginModule required " +
    "username=\"user\" password=\"password\";");
```

---

## 🎯 Quick Revision Table

| # | Topic | One-Liner (Hindi) |
|---|-------|-------------------|
| 1 | Kafka | Distributed streaming platform for real-time data |
| 2 | Topic | Message category jisme data store hota hai |
| 3 | Partition | Topic ka parallel subdivision |
| 4 | Offset | Partition mein message ka unique ID |
| 5 | Producer | Topic mein data bhejne wala |
| 6 | Consumer | Topic se data padhne wala |
| 7 | Consumer Group | Multiple consumers milke partitions share karte hain |
| 8 | Broker | Kafka server jo data store karta hai |
| 9 | Replication | Data ki copies alag brokers pe safety ke liye |
| 10 | Zookeeper/KRaft | Cluster management ke liye |
| 11 | acks | Message delivery confirmation level |
| 12 | Kafka Connect | Bina code likhe data import/export |
| 13 | Kafka Streams | Real-time processing library |
| 14 | Schema Registry | Data format validation & management |
| 15 | Log Compaction | Per key latest value hi rakho |
| 16 | Idempotent | Duplicate messages prevent karo |
| 17 | Security | SSL + SASL + ACL for production |

---

## 📚 Important Commands Cheat Sheet

```bash
# Start Zookeeper
zookeeper-server-start.sh config/zookeeper.properties

# Start Kafka Broker
kafka-server-start.sh config/server.properties

# Create Topic
kafka-topics.sh --bootstrap-server localhost:9092 --create --topic my-topic --partitions 3 --replication-factor 1

# Produce
kafka-console-producer.sh --bootstrap-server localhost:9092 --topic my-topic

# Consume
kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic my-topic --from-beginning

# Consumer Groups
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --describe --group my-group
```

---

> ✅ **Pro Tip:** Interview mein hamesha real-world use case ke saath answer do — e.g., "Kafka is used at LinkedIn for activity tracking, at Netflix for real-time monitoring, at Uber for trip event processing."

---

*Made with ❤️ for quick revision and interview prep*
