# 🔴 Redis — Zero to Advanced Complete Notes
### For 3+ Years Experienced | Hindi-Hinglish Explanation | Interview Ready

---

## 📑 Table of Contents
1. [Redis Kya Hai?](#1-redis-kya-hai)
2. [Installation & Setup](#2-installation--setup)
3. [Basic Commands](#3-basic-commands)
4. [Strings](#4-strings)
5. [Lists](#5-lists)
6. [Sets](#6-sets)
7. [Hashes](#7-hashes)
8. [Sorted Sets](#8-sorted-sets)
9. [Advanced Data Structures](#9-advanced-data-structures)
10. [TTL & Expiry](#10-ttl--expiry)
11. [Persistence RDB & AOF](#11-persistence)
12. [Transactions](#12-transactions)
13. [Pub/Sub](#13-pubsub)
14. [Replication](#14-replication)
15. [Sentinel](#15-sentinel)
16. [Cluster](#16-cluster)
17. [Eviction Policies](#17-eviction-policies)
18. [Pipelining & Performance](#18-pipelining--performance)
19. [Lua Scripting](#19-lua-scripting)
20. [Security & ACL](#20-security--acl)
21. [Redis with Node.js & Python](#21-redis-with-code)
22. [Real World Use Cases](#22-real-world-use-cases)
23. [Monitoring & Debugging](#23-monitoring--debugging)
24. [Redis Modules](#24-redis-modules)
25. [50+ Interview Q&A](#25-interview-qa)

---

## 1. Redis Kya Hai?

### Concept (Hinglish)
Redis ek **in-memory data store** hai — data RAM mein rehta hai isliye ye **bahut fast** hai (microseconds mein response).
Sochlo ek super-fast dabba hai jisme tum kuch bhi rakh sakte ho — text, numbers, lists, maps — aur instantly access kar sakte ho.

**Redis kab use karte hain?**
- Caching (DB queries fast karna)
- Session storage
- Rate limiting
- Leaderboards
- Job queues
- Real-time analytics
- Pub/Sub messaging

**Redis vs MySQL:**
| Feature | Redis | MySQL |
|---------|-------|-------|
| Storage | RAM | Disk |
| Speed | Microseconds | Milliseconds |
| Data Model | Key-Value + rich types | Tables/Rows |
| Persistence | Optional | Always |
| Best For | Cache, Queue, Sessions | Primary Database |

### Interview Answer (English)
> **Q: What is Redis and why is it fast?**
>
> Redis (Remote Dictionary Server) is an open-source, in-memory data structure store used as a database, cache, message broker, and streaming engine. It is fast because: (1) data lives in RAM — no disk I/O, (2) it is single-threaded for command execution using I/O multiplexing — no lock contention, (3) simple data structures with O(1) or O(log N) complexity. It supports rich data types: Strings, Lists, Sets, Sorted Sets, Hashes, Bitmaps, HyperLogLogs, and Streams. It can handle 100K+ ops/sec on a single node.

---

## 2. Installation & Setup

### Hands-on

```bash
# Ubuntu/Debian
sudo apt update && sudo apt install redis-server -y
sudo systemctl start redis-server
sudo systemctl enable redis-server
sudo systemctl status redis-server

# Redis CLI open karo
redis-cli
127.0.0.1:6379> PING
# Output: PONG ✅

# Docker (easiest)
docker run -d --name redis-local -p 6379:6379 redis:latest
docker exec -it redis-local redis-cli

# redis.conf important settings
# /etc/redis/redis.conf
bind 127.0.0.1
requirepass yourStrongPassword
maxmemory 256mb
maxmemory-policy allkeys-lru
appendonly yes
```

---

## 3. Basic Commands

### Concept (Hinglish)
Redis mein har cheez ek **KEY** se identify hoti hai. Key string hoti hai, value koi bhi data type ho sakta hai.
Key naming convention: colon-separated — `user:1000:profile`, `session:abc123`

### Hands-on

```bash
PING                        # PONG
PING "hello"                # hello

SET name "Rahul"
GET name                    # "Rahul"

MSET city Delhi age 28 company Google
MGET city age company

EXISTS name                 # 1 (yes) or 0 (no)
DEL name                    # Delete
DEL key1 key2 key3          # Multiple delete

KEYS *                      # ⚠️ NEVER in production!
KEYS user:*                 # Pattern match
SCAN 0 MATCH user:* COUNT 100  # ✅ Production-safe iteration

DBSIZE                      # Total keys
TYPE name                   # string/list/set/zset/hash/stream
RENAME oldkey newkey
RANDOMKEY
OBJECT ENCODING mykey       # Internal encoding
OBJECT IDLETIME mykey       # Seconds since last access

FLUSHDB                     # Current DB clear ⚠️
FLUSHALL                    # All DBs clear ⚠️

SELECT 1                    # DB 1 pe switch karo (0-15)
MOVE mykey 2                # Key ko DB 2 mein move karo
```

### Interview Q&A
> **Q: Why should you never use KEYS * in production?**
>
> `KEYS *` is O(N) and blocks Redis (single-threaded) while it scans all keys. On a database with millions of keys, this can block Redis for seconds causing timeouts for all other clients. Use `SCAN` instead — it iterates in small batches with a cursor, is non-blocking, and can be filtered with MATCH and COUNT. Always use `SCAN 0 MATCH pattern COUNT 100` in production scripts.

---

## 4. Strings

### Concept (Hinglish)
String sabse basic type — text, numbers, binary data sab store kar sakte ho. Max 512MB per key. Numbers pe atomic increment/decrement bhi hota hai — counters ke liye perfect.

### Hands-on

```bash
SET username "john_doe"
GET username

# Numbers aur atomic increment
SET counter 0
INCR counter                # 1 (atomic!)
INCRBY counter 10           # 11
DECR counter                # 10
DECRBY counter 5            # 5
INCRBYFLOAT price 9.99

# SET with options (ek hi command mein sab!)
SET token "abc123" EX 3600      # Expire in 1 hour
SET token "abc123" PX 60000     # Expire in 60 seconds (ms)
SET token "abc123" NX           # Set only if NOT exists
SET token "abc123" XX           # Set only if EXISTS
SET token "abc123" EX 3600 NX   # Distributed lock pattern!

# Get and modify
GETSET counter 0            # Old value return, new set
GETDEL mykey                # Return and delete (Redis 6.2+)
GETEX mykey EX 60           # Return and set expiry (Redis 6.2+)

# String operations
APPEND name " Kumar"        # Append to string
STRLEN name                 # String length
GETRANGE name 0 4           # Substring (0 to 4)
SETRANGE name 5 "Singh"     # Overwrite at position

MSET k1 v1 k2 v2 k3 v3
MGET k1 k2 k3
MSETNX k1 v1 k2 v2          # All or nothing (atomic)
```

### Interview Q&A
> **Q: How does INCR work atomically and what is it used for?**
>
> `INCR` reads the integer value, increments it by 1, and writes it back as a single atomic operation. Because Redis is single-threaded for command execution, no two commands interleave — making INCR race-condition free without any locks. Use cases: page view counters, rate limiting (INCR + EXPIRE), unique ID generation, inventory tracking. For distributed locks, `SET key value NX EX 30` is the pattern — sets only if key doesn't exist, with automatic TTL to prevent deadlocks.

---

## 5. Lists

### Concept (Hinglish)
List ek ordered sequence hai — doubly linked list. Dono ends (left/right) se push/pop kar sakte ho. Duplicate values allowed. Index-based access bhi milta hai.
**Use case:** Message queue, job queue, activity feed, recent browsing history.

### Hands-on

```bash
RPUSH mylist "a" "b" "c"    # Right push → [a, b, c]
LPUSH mylist "z"             # Left push  → [z, a, b, c]
RPUSHX mylist "d"            # Only push if key exists
LPUSHX mylist "y"

LRANGE mylist 0 -1           # Full list (-1 = last)
LRANGE mylist 0 2            # Index 0 to 2
LLEN mylist                  # Length

LPOP mylist                  # Remove & return from left
RPOP mylist                  # Remove & return from right
LPOP mylist 3                # Pop 3 elements (Redis 6.2+)

LINDEX mylist 0              # Get by index (no remove)
LINDEX mylist -1             # Last element
LSET mylist 1 "B"            # Set value at index
LINSERT mylist BEFORE "b" "X"
LINSERT mylist AFTER "b" "Y"

LREM mylist 2 "a"            # Remove 2 occurrences of "a"
LTRIM mylist 0 99            # Keep only first 100 elements

# Blocking operations (for queues!)
BLPOP mylist 30              # Block 30s waiting for element
BRPOP mylist 30
BLPOP queue1 queue2 0        # Multiple queues, wait forever

# Atomic move between lists
LMOVE source dest LEFT RIGHT
BLMOVE source dest LEFT RIGHT 30   # Blocking version

# --- Reliable Queue Pattern ---
# Producer:
RPUSH jobs:pending "task1" "task2"
# Consumer:
LMOVE jobs:pending jobs:processing LEFT LEFT   # Atomic pop + push
# On complete:
LREM jobs:processing 1 "task1"
# On crash recovery:
LRANGE jobs:processing 0 -1   # Re-queue these
```

### Interview Q&A
> **Q: How would you implement a reliable job queue in Redis?**
>
> Use the "Reliable Queue" pattern. Producer uses `RPUSH jobs:pending task`. Consumer uses `LMOVE jobs:pending jobs:processing LEFT LEFT` — this atomically moves the job from pending to a processing list. On job completion, `LREM jobs:processing 1 task`. If a worker crashes, jobs remain in the processing list and a recovery process can re-queue them with `LMOVE jobs:processing jobs:pending RIGHT LEFT`. Use `BLMOVE` for blocking consumers to avoid polling. This is more reliable than `LPOP` alone because jobs are never lost even during failures.

---

## 6. Sets

### Concept (Hinglish)
Set ek unordered collection hai unique strings ki — duplicates allowed nahi. Math wali set operations milti hain: union, intersection, difference.
**Use case:** Unique visitors, tags, social graph (followers/following), permissions.

### Hands-on

```bash
SADD myset "apple" "banana" "cherry"
SADD myset "apple"          # Duplicate — ignored, returns 0

SMEMBERS myset              # All members (unordered)
SCARD myset                 # Count
SISMEMBER myset "apple"     # 1 or 0
SMISMEMBER myset "apple" "grape"  # Multiple check (Redis 6.2+)

SREM myset "banana"         # Remove
SPOP myset                  # Remove and return random
SPOP myset 3                # Remove 3 random
SRANDMEMBER myset           # Random (no remove)
SRANDMEMBER myset 3         # 3 random (no remove)
SMOVE source dest "apple"   # Atomic move

# Set Math Operations
SADD s1 "a" "b" "c" "d"
SADD s2 "c" "d" "e" "f"

SINTER s1 s2                # Intersection → c, d
SUNION s1 s2                # Union → a,b,c,d,e,f
SDIFF s1 s2                 # Difference (in s1, not s2) → a, b
SDIFF s2 s1                 # → e, f

SINTERSTORE result s1 s2    # Store result in new set
SUNIONSTORE result s1 s2
SDIFFSTORE result s1 s2

# Production scan
SSCAN myset 0 MATCH "a*" COUNT 100
```

### Interview Q&A
> **Q: How would you implement mutual friends using Redis Sets?**
>
> Store each user's friends as a Redis Set: `SADD user:1:friends 2 3 4` and `SADD user:2:friends 1 3 5`. Mutual friends = `SINTER user:1:friends user:2:friends` → returns {3}. "People you may know" = friends of friends minus already-friends: `SUNIONSTORE user:1:fof ...` then `SDIFF fof user:1:friends`. This is O(N) at most and runs in milliseconds even with thousands of friends, compared to expensive SQL JOINs. `SCARD` for follower count, `SISMEMBER` for follow-status check — all O(1).

---

## 7. Hashes

### Concept (Hinglish)
Hash ek field-value pair collection hai ek key ke andar — bilkul JavaScript object ya Python dict ki tarah. Flat structure — no nesting.
**Use case:** User profile, product details, session data, config — koi bhi object.

### Hands-on

```bash
HSET user:1000 name "Rahul" age 28 city "Delhi" email "rahul@example.com"

HGET user:1000 name                  # "Rahul"
HMGET user:1000 name age city        # Multiple fields

HGETALL user:1000                    # All field-value pairs
HKEYS user:1000                      # Only field names
HVALS user:1000                      # Only values
HLEN user:1000                       # Field count

HEXISTS user:1000 email              # 1 or 0
HDEL user:1000 city                  # Delete field

# Numeric operations
HSET product:1 price 100 stock 50
HINCRBY product:1 stock -5           # stock = 45
HINCRBYFLOAT product:1 price 9.99   # price = 109.99

HSETNX user:1000 phone "9999999999" # Set only if field not exists

# Scan hash fields (production safe)
HSCAN user:1000 0 MATCH "na*" COUNT 10
```

### Interview Q&A
> **Q: Hash vs serialized JSON string — when to use which?**
>
> Use **Hashes** when you frequently read/update individual fields. `HSET user:1000 last_login "2024-01-15"` updates one field without touching the rest — efficient in CPU and bandwidth. Use **JSON string** (`SET user:1000 '{"name":"Rahul",...}'`) when you always read the entire object at once, or when you need nested/array structures (Hashes are flat). With JSON, updating one field requires GET → deserialize → modify → serialize → SET — expensive under high write load. For nested JSON with partial updates, use the **RedisJSON** module which supports path-based operations like `JSON.SET user:1000 $.address.city "Mumbai"`.

---

## 8. Sorted Sets

### Concept (Hinglish)
Sorted Set (ZSET) mein har member ka ek **score** (float number) hota hai. Redis automatically elements ko score order mein rakhta hai. Members unique hote hain, scores duplicate ho sakte hain.
**Use case:** Leaderboards, priority queues, rate limiting windows, top-N queries, time-series.

### Hands-on

```bash
ZADD leaderboard 1500 "Alice"
ZADD leaderboard 2000 "Bob"
ZADD leaderboard 1800 "Charlie"

# Add options
ZADD leaderboard NX 900 "Dave"      # Add only (no update)
ZADD leaderboard XX 2500 "Alice"    # Update only (no add)
ZADD leaderboard GT 3000 "Bob"      # Update only if new score > current
ZADD leaderboard LT 100 "Alice"     # Update only if new score < current
ZADD leaderboard CH 3000 "Bob"      # Return changed count

# Range queries
ZRANGE leaderboard 0 -1                    # All (ascending score)
ZRANGE leaderboard 0 -1 WITHSCORES        # With scores
ZRANGE leaderboard 0 -1 REV WITHSCORES    # Descending (Redis 6.2+)
ZREVRANGE leaderboard 0 9 WITHSCORES      # Top 10 (old syntax)

# Score range
ZRANGEBYSCORE leaderboard 1000 2000 WITHSCORES
ZRANGEBYSCORE leaderboard -inf +inf
ZRANGEBYSCORE leaderboard 1000 2000 LIMIT 0 5   # Pagination

# Lex range (same score members)
ZRANGEBYLEX myset "[a" "[z"

# Rank (0-indexed)
ZRANK leaderboard "Alice"           # Ascending rank
ZREVRANK leaderboard "Alice"        # Descending rank (use this for leaderboard!)
ZSCORE leaderboard "Bob"            # Get score
ZCOUNT leaderboard 1000 2000        # Count in score range
ZCARD leaderboard                   # Total members

ZINCRBY leaderboard 500 "Charlie"   # Increment score
ZREM leaderboard "Dave"
ZREMRANGEBYSCORE leaderboard 0 1000 # Remove by score range
ZREMRANGEBYRANK leaderboard 0 2     # Remove by rank range

ZPOPMIN leaderboard                 # Remove and return lowest score
ZPOPMAX leaderboard                 # Remove and return highest score
BZPOPMIN leaderboard 30             # Blocking version (priority queue!)

# Set operations
ZUNIONSTORE out 2 s1 s2
ZINTERSTORE out 2 s1 s2
ZDIFFSTORE out 2 s1 s2

# Production scan
ZSCAN leaderboard 0 MATCH "Ali*" COUNT 100

# --- Real-World: Leaderboard ---
ZADD game:scores 5000 "player:1" 8000 "player:2" 7500 "player:3"
ZREVRANGE game:scores 0 9 WITHSCORES       # Top 10
ZREVRANK game:scores "player:1"            # Player rank
ZINCRBY game:scores 1000 "player:1"        # Add score

# Nearby ranks (player ±5)
# 1. Get rank: ZREVRANK game:scores "player:1" → rank=2
# 2. ZREVRANGE game:scores (rank-5) (rank+5) WITHSCORES
```

### Interview Q&A
> **Q: How does Redis Sorted Set maintain order internally?**
>
> Redis uses a **skip list** combined with a **hash table** internally. The skip list maintains sorted order for range queries in O(log N), while the hash table provides O(1) lookup by member name. For small sets (≤128 members, scores ≤64 bytes), Redis uses a **ziplist** (compact array) which is more memory-efficient. The encoding automatically upgrades to skiplist when thresholds are exceeded. `OBJECT ENCODING myzset` shows `ziplist` or `skiplist`. This dual structure is why both `ZSCORE` (O(1) hash) and `ZRANGE` (O(log N + K) skiplist) are fast.

---

## 9. Advanced Data Structures

### 9a. Bitmaps

```bash
# Bitmaps — bit operations on strings
# Use case: DAU tracking, feature flags, attendance

# User 1001 ne aaj login kiya
SETBIT daily:logins:2024-01-15 1001 1
SETBIT daily:logins:2024-01-15 1002 1
SETBIT daily:logins:2024-01-15 1003 1

GETBIT daily:logins:2024-01-15 1001    # 1
GETBIT daily:logins:2024-01-15 9999    # 0

BITCOUNT daily:logins:2024-01-15       # Total logins today
BITCOUNT daily:logins:2024-01-15 0 10  # Byte range 0-10

# Users logged in BOTH days (AND)
BITOP AND active:both day1 day2
BITCOUNT active:both

# Users logged in on EITHER day (OR)
BITOP OR active:either day1 day2

BITPOS daily:logins:2024-01-15 1       # First user who logged in
BITPOS daily:logins:2024-01-15 0       # First user who did NOT log in
```

### 9b. HyperLogLog

```bash
# Approximate unique count — 12KB memory, 0.81% error
# Use case: Unique visitors, unique IPs, unique search queries

PFADD visitors:2024-01-15 "user:1" "user:2" "user:3" "user:1"
PFCOUNT visitors:2024-01-15          # ~3

PFADD visitors:2024-01-16 "user:3" "user:4" "user:5"
PFMERGE visitors:week visitors:2024-01-15 visitors:2024-01-16
PFCOUNT visitors:week                # ~5
```

### 9c. Streams

```bash
# Streams — persistent append-only log (Redis's Kafka)
# Use case: Event sourcing, activity log, IoT, microservice events

# Add events (auto-generated ID = timestamp-sequence)
XADD events * action "login" user_id "1001" ip "192.168.1.1"
XADD events * action "purchase" user_id "1001" product "redis-book"

# Specific ID
XADD events 1638000000000-0 action "signup" user_id "1002"

# Max length (keep last 1000)
XADD events MAXLEN ~ 1000 * action "logout" user_id "1001"

XLEN events                          # Total messages
XRANGE events - +                    # All messages
XRANGE events - + COUNT 10           # First 10
XREVRANGE events + - COUNT 5         # Last 5
XRANGE events 1638000000000 +        # From timestamp

# Read new messages (like tail -f)
XREAD COUNT 10 STREAMS events 0      # From beginning
XREAD COUNT 10 BLOCK 0 STREAMS events $  # Block, wait for new

# Consumer Groups (multiple consumers, each gets different messages)
XGROUP CREATE events grp1 0          # From beginning
XGROUP CREATE events grp2 $          # Only new messages

XREADGROUP GROUP grp1 consumer1 COUNT 5 STREAMS events >   # > = undelivered
XREADGROUP GROUP grp1 consumer2 COUNT 5 STREAMS events >

XACK events grp1 <message-id>        # Acknowledge processed
XPENDING events grp1 - + 10         # See unacknowledged
XCLAIM events grp1 consumer2 3600000 <msg-id>  # Reassign stale message
```

### Interview Q&A
> **Q: When to use Streams vs Pub/Sub?**
>
> **Pub/Sub** is fire-and-forget — no persistence, no history, offline subscribers miss all messages, no consumer groups. **Streams** persist messages in an append-only log. Consumers can replay from any point in history, multiple consumer groups process the same stream independently (each with their own cursor), and `XACK` provides at-least-once delivery guarantees with `XPENDING` for failure recovery. Use Pub/Sub for ephemeral real-time notifications (live chat, score updates) where losing a message is acceptable. Use Streams for reliable event pipelines, audit logs, microservice event buses, or anywhere Kafka-like semantics are needed but at smaller scale.

---

## 10. TTL & Expiry

### Concept (Hinglish)
Keys ka expiration time set kar sakte ho — stale data automatically delete hota hai. Caching ke liye essential.

### Hands-on

```bash
SET session:abc "user_data"
EXPIRE session:abc 3600         # Expire in 3600 seconds (1 hour)
PEXPIRE session:abc 3600000     # Expire in ms
EXPIREAT session:abc 1735689600 # Unix timestamp pe expire
PEXPIREAT session:abc 1735689600000

TTL session:abc                 # Remaining seconds (-1=no expiry, -2=gone)
PTTL session:abc                # Remaining milliseconds

PERSIST session:abc             # Remove expiry (make permanent)

# SET ke saath directly expiry
SET otp:1001 "456789" EX 300    # OTP 5 min
SET lock:res "token" NX EX 30  # Distributed lock

# Expiry notification (redis.conf)
# notify-keyspace-events "Ex"
# Subscribe karo:
# SUBSCRIBE __keyevent@0__:expired
# Jab bhi koi key expire ho, notification milega
```

### Interview Q&A
> **Q: How does Redis expire keys internally?**
>
> Redis uses two strategies together. **Lazy expiration**: when a key is accessed, Redis checks its TTL and deletes it if expired before returning. **Active expiration**: a background timer fires 10 times/second, randomly samples 20 keys with TTLs — if >25% are expired, it samples again immediately, otherwise waits for next cycle. This means expired keys may linger briefly in memory but are guaranteed to be cleaned up. Memory usage for expired-but-not-yet-deleted keys is a known concern — set `maxmemory` and an eviction policy as a safety net. You can get expiry notifications via keyspace notifications (`notify-keyspace-events "Ex"`) for implementing delayed job triggers.

---

## 11. Persistence

### Concept (Hinglish)
Redis in-memory hai — restart pe data gone! Persistence se data disk pe save hota hai.
- **RDB**: Periodic snapshot (photo khenchna)
- **AOF**: Har write log karo (diary likhna)
- **Hybrid**: Dono saath (best of both)

### Hands-on

```bash
# --- RDB (redis.conf) ---
save 900 1          # 15 min mein 1+ changes
save 300 10         # 5 min mein 10+ changes
save 60 10000       # 1 min mein 10000+ changes
dbfilename dump.rdb
dir /var/lib/redis
rdbcompression yes

BGSAVE              # Manual snapshot (non-blocking ✅)
SAVE                # Manual snapshot (blocking ⚠️)
LASTSAVE            # Last save Unix timestamp

# --- AOF (redis.conf) ---
appendonly yes
appendfilename "appendonly.aof"
appendfsync everysec   # always | everysec | no
                       # always = max safety, slow
                       # everysec = best balance ✅ (max 1 sec loss)
                       # no = fastest, OS decides

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb
BGREWRITEAOF           # Manual AOF compaction

# --- Hybrid (Production Best Practice) ---
aof-use-rdb-preamble yes
# Startup: loads RDB for speed, replays only new AOF entries

# Check persistence status
INFO persistence
```

```
RDB vs AOF:
┌──────────────────┬─────────────────────┬──────────────────────┐
│ Feature          │ RDB                 │ AOF                  │
├──────────────────┼─────────────────────┼──────────────────────┤
│ Data Loss        │ Minutes             │ Max 1 sec            │
│ File Size        │ Compact binary      │ Larger text log      │
│ Restart Speed    │ Fast                │ Slow (log replay)    │
│ Performance      │ Better              │ Slight overhead      │
│ Best For         │ Backups, snapshots  │ Critical data        │
└──────────────────┴─────────────────────┴──────────────────────┘
```

### Interview Q&A
> **Q: Which persistence mode would you use in production and why?**
>
> I'd enable **Hybrid mode** (`aof-use-rdb-preamble yes` with `appendonly yes`). On restart, Redis loads the compact RDB snapshot first (fast), then replays only the AOF entries written after the snapshot (minimal). This gives fast restarts AND at-most-1-second data loss (`appendfsync everysec`). For a pure caching layer where losing data on restart is acceptable, I'd disable persistence entirely (`save ""`, `appendonly no`) for maximum performance. For mission-critical data, I'd use `appendfsync always` — every write is fsynced to disk, safest but slowest.

---

## 12. Transactions

### Concept (Hinglish)
MULTI/EXEC se multiple commands ek atomic block mein execute hote hain — dono execute honge ya (agar DISCARD karo) dono nahi. WATCH se optimistic locking milta hai.

### Hands-on

```bash
# Basic transaction
MULTI
SET balance 1000
INCR visit_count
HSET user:1 last_seen "2024-01-15"
EXEC                    # All 3 execute atomically

# Discard
MULTI
SET foo "bar"
DISCARD                 # Cancel, nothing executes

# WATCH — Optimistic Locking
# Pattern: Read-Modify-Write without race conditions
WATCH account:balance
current = GET account:balance
# ... calculate new balance ...
MULTI
SET account:balance (new_value)
EXEC
# If someone changed account:balance after WATCH, EXEC returns nil
# Application must retry the whole thing

# Error behavior
MULTI
SET k1 "hello"
WRONGCMD               # Syntax error → whole transaction aborted
EXEC                   # Error returned

MULTI
SET k1 "hello"
INCR k1                # Runtime error (k1 is string, not int)
SET k2 "world"
EXEC                   # k1 set succeeds, INCR fails, k2 set succeeds
# ⚠️ Redis does NOT rollback runtime errors!
```

### Interview Q&A
> **Q: Is Redis transaction ACID? Does it support rollback?**
>
> Partially ACID. **Atomicity**: commands execute without interruption but Redis does NOT rollback on runtime errors — partial execution is possible. **Isolation**: yes, commands in a transaction are isolated from other clients. **Consistency and Durability**: depend on app logic and persistence config. Redis explicitly chose not to support rollbacks because they add complexity and most Redis errors are programming mistakes (wrong type), not runtime data errors. For true atomic-with-rollback behavior, use **Lua scripts** — they run atomically on the server and can implement conditional logic and error handling, unlike MULTI/EXEC.

---

## 13. Pub/Sub

### Concept (Hinglish)
Publisher ek channel pe message publish karta hai — saare subscribers ko milta hai. One-to-many broadcast. Fire-and-forget — no persistence.
**Use case:** Real-time notifications, live scores, cache invalidation, chat (non-critical).

### Hands-on

```bash
# Terminal 1 — Subscribe
SUBSCRIBE sports:cricket
SUBSCRIBE sports:cricket sports:football news:tech   # Multiple
PSUBSCRIBE sports:*          # Pattern subscribe
PSUBSCRIBE news:* sports:*   # Multiple patterns

# Terminal 2 — Publish
PUBLISH sports:cricket "India won by 50 runs!"
PUBLISH news:tech "Redis 8.0 released"

# Returns: number of subscribers who received the message

# Introspection
PUBSUB CHANNELS              # All active channels
PUBSUB CHANNELS sports:*     # Pattern filter
PUBSUB NUMSUB sports:cricket # Subscriber count for channel
PUBSUB NUMPAT                # Total pattern subscriptions

# Unsubscribe
UNSUBSCRIBE sports:cricket
UNSUBSCRIBE                  # All channels
PUNSUBSCRIBE sports:*
PUNSUBSCRIBE                 # All patterns

# ⚠️ In subscribe mode, client can ONLY use:
# SUBSCRIBE, UNSUBSCRIBE, PSUBSCRIBE, PUNSUBSCRIBE, PING, RESET, QUIT
```

### Interview Q&A
> **Q: Redis Pub/Sub vs Redis Streams — when to use which?**
>
> **Pub/Sub**: ephemeral, no persistence, subscriber must be online to receive, no consumer groups, no acknowledgment, simple. Good for: live notifications, real-time dashboards, cache invalidation signals. **Streams**: persistent, consumers read from any offset in history, consumer groups allow parallel processing, XACK for delivery confirmation, XPENDING for failure recovery. Good for: event sourcing, microservice event bus, audit logs, anywhere Kafka semantics are needed. Rule of thumb: if losing a message is acceptable → Pub/Sub. If every message must be processed → Streams.

---

## 14. Replication

### Concept (Hinglish)
Master pe writes aate hain, data automatically Replicas pe copy hota hai. Replicas se reads kar sakte ho — read scaling. Async hota hai by default.
**Use case:** Read scaling, data redundancy, disaster recovery.

### Hands-on

```bash
# Replica setup (redis.conf)
replicaof 192.168.1.10 6379
# Ya auth ke saath:
replicaof 192.168.1.10 6379
masterauth "master_password"
replica-read-only yes

# Runtime mein
REPLICAOF 192.168.1.10 6379
REPLICAOF NO ONE              # Promote replica to master

# Status check
INFO replication
# Master shows: role:master, connected_slaves:2
# Replica shows: role:slave, master_host:..., master_link_status:up

# Replication lag
INFO replication | grep offset
# master_repl_offset vs slave_repl_offset → lag in bytes

# Wait for replicas (stronger consistency)
WAIT 2 1000   # Wait until 2 replicas ack, max 1000ms
              # Returns: number of replicas that ack'd

# Config
repl-backlog-size 1mb           # Buffer for partial resync
repl-diskless-sync yes          # Send RDB directly over socket (no disk)
min-replicas-to-write 1         # Master refuses writes if < N replicas
min-replicas-max-lag 10         # Replica must be < 10s behind
```

### Interview Q&A
> **Q: What is replication lag and how do you handle it?**
>
> Replication is asynchronous by default — master sends commands to replicas without waiting for acknowledgment, so replicas may be slightly behind. This causes **replication lag** — stale reads from replica. Monitor via `INFO replication` → `master_repl_offset` vs `slave_repl_offset`. Mitigations: (1) `WAIT N timeout` blocks until N replicas confirm — for critical reads after important writes. (2) Route critical reads to master, non-critical to replica. (3) Use `min-replicas-to-write` and `min-replicas-max-lag` to refuse writes if replicas fall too far behind. (4) Ensure low-latency network between master and replicas.

---

## 15. Sentinel

### Concept (Hinglish)
Sentinel Redis ka watchdog hai. Master down hone pe automatically ek Replica ko Master promote kar deta hai. Multiple Sentinels milke quorum se decision lete hain.
**Use case:** High availability without manual intervention.

### Hands-on

```bash
# sentinel.conf
sentinel monitor mymaster 127.0.0.1 6379 2   # Quorum = 2
sentinel down-after-milliseconds mymaster 5000
sentinel failover-timeout mymaster 60000
sentinel parallel-syncs mymaster 1
sentinel auth-pass mymaster "master_password"

# Start
redis-sentinel /etc/redis/sentinel.conf

# Sentinel CLI (port 26379)
redis-cli -p 26379

SENTINEL masters                              # All monitored masters
SENTINEL master mymaster                      # Specific master info
SENTINEL replicas mymaster                    # Replicas
SENTINEL sentinels mymaster                   # Other sentinels
SENTINEL get-master-addr-by-name mymaster     # Current master IP:port
SENTINEL ckquorum mymaster                    # Check if quorum is reachable
SENTINEL failover mymaster                    # Manual failover (testing)
SENTINEL reset mymaster                       # Reset state
```

### Interview Q&A
> **Q: How does Redis Sentinel automatic failover work step by step?**
>
> (1) Sentinel detects master is unreachable after `down-after-milliseconds` → marks it **SDOWN** (subjectively down). (2) When quorum number of Sentinels agree → marks it **ODOWN** (objectively down). (3) Sentinels elect a **leader Sentinel** (via Raft-like voting). (4) Leader selects best replica: lowest replication lag, highest `slave-priority`, most recent REPLCONF offset. (5) Leader sends `REPLICAOF NO ONE` to promote it. (6) Other replicas are reconfigured to follow the new master. (7) Old master (if it recovers) is demoted to replica. Clients using the Sentinel-aware driver automatically reconnect to new master. Total failover time: typically 10–30 seconds.

---

## 16. Cluster

### Concept (Hinglish)
Cluster mein data **multiple nodes** pe distribute hota hai — horizontal scaling. 16384 hash slots hote hain jo nodes ke beech baante jaate hain. Each master can have replicas.
**Use case:** Datasets too large for one node, very high write throughput.

### Hands-on

```bash
# 6 nodes: 3 master + 3 replica
# redis.conf for each node:
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
appendonly yes
port 7001   # Different port for each

# Start all 6 nodes then:
redis-cli --cluster create \
  127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 \
  127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7006 \
  --cluster-replicas 1

# Connect
# Cluster continued...

redis-cli -c -p 7001    # -c flag = cluster mode (auto redirect)

# Cluster info
CLUSTER INFO
CLUSTER NODES
CLUSTER MYID

# Hash slots
CLUSTER KEYSLOT mykey           # Which slot does this key map to?
CLUSTER COUNTKEYSINSLOT 0       # Keys in slot 0
CLUSTER GETKEYSINSLOT 0 10      # Keys in slot 0 (max 10)

# Resharding (move slots between nodes)
redis-cli --cluster reshard 127.0.0.1:7001

# Add new node
redis-cli --cluster add-node 127.0.0.1:7007 127.0.0.1:7001

# Remove node
redis-cli --cluster del-node 127.0.0.1:7001 <node-id>

# Check cluster health
redis-cli --cluster check 127.0.0.1:7001

# Fix cluster issues
redis-cli --cluster fix 127.0.0.1:7001

# --- Hash Tags (force keys to same slot) ---
SET {user:1000}.name "Rahul"
SET {user:1000}.email "rahul@example.com"
# {} ke andar ka part slot determine karta hai
# Dono keys same slot pe jayenge → MGET/transactions possible

# --- Limitations in Cluster Mode ---
# MSET/MGET only works if all keys are in same slot
# MSET {tag}.k1 v1 {tag}.k2 v2   ✅ (same hash tag)
# MSET k1 v1 k2 v2               ❌ (different slots possible)
# Transactions only within single slot
# SELECT not supported
# KEYS * only scans current node
```

### Interview Q&A
> **Q: How does Redis Cluster distribute data and handle failover?**
>
> Redis Cluster uses **consistent hashing with hash slots** — 16384 slots total. Each key is mapped to a slot via `CRC16(key) % 16384`. Slots are distributed across master nodes (e.g., 3 masters → ~5461 slots each). Clients use a cluster-aware driver that caches the slot-to-node mapping. On wrong-node request, Redis returns a `MOVED` redirect. For **hash tags** `{tag}`, only the tag portion determines the slot — allowing multi-key operations on related keys. **Failover**: each master has replicas. Cluster detects master failure via gossip protocol (`cluster-node-timeout`). A replica calls an election, gets votes from majority of masters, promotes itself. No Sentinel needed — it's built-in.

---

## 17. Eviction Policies

### Concept (Hinglish)
Jab Redis ka memory limit (`maxmemory`) full ho jaaye, toh naye data ke liye kuch purane keys delete karne padte hain. Ye eviction policy decide karti hai **kaun sa key delete hoga**.

### Hands-on

```bash
# redis.conf
maxmemory 512mb
maxmemory-policy allkeys-lru    # Set policy

# Runtime mein change
CONFIG SET maxmemory 512mb
CONFIG SET maxmemory-policy allkeys-lru
CONFIG GET maxmemory-policy

# Memory usage check
INFO memory
MEMORY USAGE mykey              # Bytes used by specific key
MEMORY DOCTOR                   # Memory health report
MEMORY STATS                    # Detailed stats
```

```
Eviction Policies:
┌─────────────────────┬──────────────────────────────────────────────┐
│ Policy              │ Behavior                                     │
├─────────────────────┼──────────────────────────────────────────────┤
│ noeviction          │ Error on new writes when full (default)      │
│ allkeys-lru         │ Evict least recently used from ALL keys ✅   │
│ volatile-lru        │ Evict LRU from keys WITH expiry only         │
│ allkeys-lfu         │ Evict least frequently used from ALL keys    │
│ volatile-lfu        │ Evict LFU from keys WITH expiry only         │
│ allkeys-random      │ Evict random key from ALL keys               │
│ volatile-random     │ Evict random from keys WITH expiry           │
│ volatile-ttl        │ Evict key with nearest expiry first          │
└─────────────────────┴──────────────────────────────────────────────┘

Recommendation:
- Pure cache (all keys expendable)   → allkeys-lru or allkeys-lfu
- Mixed (some keys must persist)     → volatile-lru
- Session store                      → volatile-ttl
- Never use noeviction for cache     → causes errors!
```

### Interview Q&A
> **Q: What is the difference between LRU and LFU eviction?**
>
> **LRU (Least Recently Used)**: evicts the key that hasn't been accessed for the longest time. Good for temporal locality — recently used data is likely to be used again. Redis uses an **approximated LRU** — samples N random keys (default 5, configurable via `maxmemory-samples`) and evicts the least recently used among them. Not exact LRU but memory-efficient. **LFU (Least Frequently Used)**: evicts the key accessed fewest times overall, with a time-decay factor. Better for workloads with hot/cold data patterns — frequently used keys survive even if not accessed recently. Use `allkeys-lfu` for workloads with clear hot-key patterns. Increase `maxmemory-samples` (e.g., 10) for more accurate approximation at slight CPU cost.

---

## 18. Pipelining & Performance

### Concept (Hinglish)
Normal mein har command ek round-trip karta hai client-server ke beech. Pipeline mein saare commands ek saath bhejo, saari responses ek saath aati hain — latency dramatically kam hoti hai.

### Hands-on

```bash
# Without pipeline: 100 commands = 100 round trips
# With pipeline: 100 commands = 1 round trip

# CLI pipeline (unix pipe)
echo -e "SET k1 v1\nSET k2 v2\nSET k3 v3" | redis-cli --pipe

# redis-cli pipeline mode
redis-cli --pipe << EOF
SET user:1 "Alice"
SET user:2 "Bob"
INCR counter
EXPIRE counter 3600
EOF
```

```python
# Python — redis-py pipeline
import redis
r = redis.Redis()

# Without pipeline (slow)
for i in range(1000):
    r.set(f"key:{i}", i)

# With pipeline (fast — 10-100x)
pipe = r.pipeline(transaction=False)  # transaction=False = no MULTI/EXEC
for i in range(1000):
    pipe.set(f"key:{i}", i)
pipe.execute()  # Single round trip

# Pipeline with transactions
pipe = r.pipeline(transaction=True)   # Wraps in MULTI/EXEC
pipe.set("balance", 1000)
pipe.incr("counter")
pipe.execute()
```

```javascript
// Node.js — ioredis pipeline
const Redis = require('ioredis');
const redis = new Redis();

// Pipeline
const pipeline = redis.pipeline();
for (let i = 0; i < 1000; i++) {
  pipeline.set(`key:${i}`, i);
  pipeline.expire(`key:${i}`, 3600);
}
const results = await pipeline.exec();
// results = [[null, 'OK'], [null, 1], ...] — [error, result] pairs
```

```bash
# Performance tuning (redis.conf)
tcp-backlog 511
tcp-keepalive 300
hz 10                       # Background tasks frequency (10-100)
aof-rewrite-incremental-fsync yes
rdb-save-incremental-fsync yes

# Slow log — commands slower than N microseconds
slowlog-log-slower-than 10000   # 10ms
slowlog-max-len 128
SLOWLOG GET 10              # Last 10 slow commands
SLOWLOG LEN                 # Total slow commands
SLOWLOG RESET

# Latency monitoring
CONFIG SET latency-tracking yes
LATENCY LATEST
LATENCY HISTORY event
LATENCY RESET
```

### Interview Q&A
> **Q: How does Redis pipelining work and what are its trade-offs?**
>
> Pipelining buffers multiple commands client-side and sends them in one TCP packet, receiving all responses together. This eliminates per-command round-trip latency (RTT). For 1000 commands over a 1ms RTT network: without pipeline = 1 second, with pipeline = ~1ms. **Trade-offs**: (1) Responses are not available until all commands complete — can't use response of command N as input to command N+1. (2) Large pipelines consume more memory client-side. (3) Unlike transactions (MULTI/EXEC), pipelines are NOT atomic — other client commands can interleave. Best practice: batch 100–1000 commands per pipeline. For atomicity + performance, combine pipeline with MULTI/EXEC, or use Lua scripts.

---

## 19. Lua Scripting

### Concept (Hinglish)
Lua scripts Redis server pe directly execute hote hain — **fully atomic** (no interruption possible). Complex operations ek single atomic step mein kar sakte ho jo MULTI/EXEC se possible nahi.

### Hands-on

```bash
# Simple script
EVAL "return 1" 0

# Script with keys and args
EVAL "return redis.call('SET', KEYS[1], ARGV[1])" 1 mykey "hello"
# 1 = number of KEYS arguments
# mykey = KEYS[1]
# "hello" = ARGV[1]

# GET + SET in one atomic operation
EVAL "
  local val = redis.call('GET', KEYS[1])
  if val then
    return val
  else
    redis.call('SET', KEYS[1], ARGV[1])
    return ARGV[1]
  end
" 1 mykey "default_value"

# Rate limiting script (atomic check-and-increment)
EVAL "
  local key = KEYS[1]
  local limit = tonumber(ARGV[1])
  local window = tonumber(ARGV[2])
  local current = redis.call('INCR', key)
  if current == 1 then
    redis.call('EXPIRE', key, window)
  end
  if current > limit then
    return 0
  end
  return 1
" 1 rate:user:1001 100 60
# Returns 1 = allowed, 0 = rate limited

# Load script (cache it on server)
SCRIPT LOAD "return redis.call('GET', KEYS[1])"
# Returns SHA1 hash

# Execute cached script (faster — no parsing)
EVALSHA <sha1> 1 mykey

# Check if script exists
SCRIPT EXISTS <sha1>

# Clear all cached scripts
SCRIPT FLUSH

# Error handling
EVAL "
  local ok, err = pcall(function()
    return redis.call('INCR', KEYS[1])
  end)
  if ok then
    return 1
  else
    return redis.error_reply(err)
  end
" 1 mykey
```

### Interview Q&A
> **Q: Why use Lua scripts over MULTI/EXEC transactions?**
>
> Lua scripts run atomically on the Redis server — no other command can execute while a script runs. Unlike MULTI/EXEC, scripts can: (1) **use conditional logic** — read a value and branch based on it (MULTI/EXEC queues blindly), (2) **handle errors and rollback** using `pcall`, (3) **reduce round trips** by doing all computation server-side, (4) **be cached** on the server with `SCRIPT LOAD` + `EVALSHA` — only SHA is sent, not full script. The downside: scripts must complete quickly (Redis is blocked) and should not have side effects that survive errors. Use `redis.pcall` instead of `redis.call` to catch and handle errors gracefully.

---

## 20. Security & ACL

### Concept (Hinglish)
Redis by default koi security nahi karta — production mein secure karna zaroori hai. ACL (Access Control Lists) se different users ko alag-alag permissions de sakte ho.

### Hands-on

```bash
# redis.conf — Basic security
bind 127.0.0.1 192.168.1.10   # Only specific IPs
protected-mode yes              # Block external access if no auth
requirepass "StrongP@ssw0rd!"   # Single password (legacy)
rename-command FLUSHALL ""      # Disable dangerous command
rename-command CONFIG ""        # Disable CONFIG command
rename-command DEBUG ""

# Authenticate
AUTH password
AUTH username password          # ACL users

# --- ACL (Redis 6.0+) ---
# List all users
ACL LIST
ACL WHOAMI                      # Current user

# Create user
ACL SETUSER alice on >password123 ~cached:* &* +get +set +del
# on = enabled
# >password123 = password
# ~cached:* = can only access keys starting with "cached:"
# &* = all pub/sub channels
# +get +set +del = allowed commands only

# Read-only user
ACL SETUSER readonly on >readpass ~* &* +@read -@write

# Admin user
ACL SETUSER admin on >adminpass ~* &* +@all

# Command categories
# +@all, +@read, +@write, +@string, +@hash, +@list, +@set, +@sortedset
# +@dangerous (FLUSHALL, DEBUG etc), +@admin

ACL GETUSER alice               # User details
ACL DELUSER alice               # Delete user
ACL SAVE                        # Save to aclfile
ACL LOG                         # Failed auth attempts

# acl file (redis.conf)
aclfile /etc/redis/users.acl

# TLS/SSL (redis.conf)
tls-port 6380
tls-cert-file /path/to/cert.pem
tls-key-file /path/to/key.pem
tls-ca-cert-file /path/to/ca.pem
```

### Interview Q&A
> **Q: How do you secure a Redis instance in production?**
>
> Multi-layer approach: (1) **Network**: bind to specific IPs only, never expose to public internet, use firewall rules, put Redis in private subnet/VPC. (2) **Authentication**: use ACL with strong passwords per user, grant minimal required permissions (`~session:*` instead of `~*`). (3) **Disable dangerous commands**: `rename-command FLUSHALL ""`, `rename-command CONFIG ""`. (4) **Encryption**: enable TLS for in-transit encryption, use Redis Enterprise or stunnel for older versions. (5) **Protected mode**: enabled by default — blocks external connections if no auth configured. (6) **Audit**: `ACL LOG` for failed attempts, `MONITOR` for command audit (careful in production — high overhead). (7) **Keyspace isolation**: use separate Redis instances or ACL key patterns per application.

---

## 21. Redis with Code

### Node.js (ioredis)

```javascript
const Redis = require('ioredis');

// Basic connection
const redis = new Redis({
  host: '127.0.0.1',
  port: 6379,
  password: 'yourpassword',
  db: 0,
  retryStrategy: (times) => Math.min(times * 50, 2000),
  maxRetriesPerRequest: 3,
});

// Cluster
const cluster = new Redis.Cluster([
  { host: '127.0.0.1', port: 7001 },
  { host: '127.0.0.1', port: 7002 },
]);

// Sentinel
const sentinel = new Redis({
  sentinels: [
    { host: '127.0.0.1', port: 26379 },
    { host: '127.0.0.1', port: 26380 },
  ],
  name: 'mymaster',
  password: 'masterpassword',
});

// Basic operations
await redis.set('name', 'Rahul', 'EX', 3600);
const name = await redis.get('name');

await redis.hset('user:1000', { name: 'Rahul', age: 28, city: 'Delhi' });
const user = await redis.hgetall('user:1000');

await redis.zadd('leaderboard', 1500, 'Alice', 2000, 'Bob');
const top = await redis.zrevrange('leaderboard', 0, 9, 'WITHSCORES');

// Pipeline
const pipe = redis.pipeline();
pipe.set('k1', 'v1');
pipe.set('k2', 'v2');
pipe.get('k1');
const results = await pipe.exec();

// Pub/Sub
const sub = new Redis();
const pub = new Redis();
await sub.subscribe('notifications');
sub.on('message', (channel, message) => {
  console.log(`${channel}: ${message}`);
});
await pub.publish('notifications', 'Hello!');

// Stream
await redis.xadd('events', '*', 'action', 'login', 'user', '1001');
const messages = await redis.xrange('events', '-', '+');

// Scan (production safe)
const stream = redis.scanStream({ match: 'user:*', count: 100 });
stream.on('data', (keys) => { console.log(keys); });
stream.on('end', () => { console.log('Done'); });
```

### Python (redis-py)

```python
import redis
import json
from contextlib import contextmanager

# Connection
r = redis.Redis(
    host='127.0.0.1',
    port=6379,
    password='yourpassword',
    db=0,
    decode_responses=True,    # Auto decode bytes to str
    socket_timeout=5,
    retry_on_timeout=True,
)

# Connection pool (for web apps)
pool = redis.ConnectionPool(
    host='127.0.0.1', port=6379,
    password='yourpassword',
    max_connections=50,
    decode_responses=True
)
r = redis.Redis(connection_pool=pool)

# Sentinel
from redis.sentinel import Sentinel
sentinel = Sentinel(
    [('127.0.0.1', 26379), ('127.0.0.1', 26380)],
    socket_timeout=0.1
)
master = sentinel.master_for('mymaster', socket_timeout=0.1)
slave = sentinel.slave_for('mymaster', socket_timeout=0.1)

# Basic ops
r.set('name', 'Rahul', ex=3600)
name = r.get('name')

r.hset('user:1000', mapping={'name': 'Rahul', 'age': 28})
user = r.hgetall('user:1000')

# Pipeline
pipe = r.pipeline(transaction=False)
for i in range(1000):
    pipe.set(f'key:{i}', i)
    pipe.expire(f'key:{i}', 3600)
pipe.execute()

# Cache decorator pattern
def cache(key, ttl=300):
    def decorator(func):
        def wrapper(*args, **kwargs):
            cached = r.get(key)
            if cached:
                return json.loads(cached)
            result = func(*args, **kwargs)
            r.setex(key, ttl, json.dumps(result))
            return result
        return wrapper
    return decorator

@cache('expensive:query', ttl=600)
def get_stats():
    # DB query here
    return {'users': 10000, 'orders': 50000}

# Distributed lock
@contextmanager
def redis_lock(lock_name, timeout=30):
    lock = r.set(f'lock:{lock_name}', '1', nx=True, ex=timeout)
    try:
        if not lock:
            raise Exception(f'Could not acquire lock: {lock_name}')
        yield
    finally:
        r.delete(f'lock:{lock_name}')

with redis_lock('process:payments', timeout=60):
    # Critical section
    pass

# Scan safely
cursor = 0
while True:
    cursor, keys = r.scan(cursor, match='user:*', count=100)
    for key in keys:
        print(key)
    if cursor == 0:
        break
```

---

## 22. Real World Use Cases

### 22a. Caching (Cache-Aside Pattern)

```python
def get_user(user_id):
    cache_key = f'user:{user_id}'
    
    # 1. Cache check karo
    cached = r.get(cache_key)
    if cached:
        return json.loads(cached)
    
    # 2. DB se fetch karo
    user = db.query('SELECT * FROM users WHERE id = ?', user_id)
    
    # 3. Cache mein store karo (15 min TTL)
    r.setex(cache_key, 900, json.dumps(user))
    return user

def update_user(user_id, data):
    db.update('UPDATE users SET ...', user_id, data)
    # Cache invalidate karo
    r.delete(f'user:{user_id}')
```

### 22b. Session Store

```python
import secrets

def create_session(user_id):
    session_id = secrets.token_urlsafe(32)
    session_data = {
        'user_id': user_id,
        'created_at': time.time(),
        'ip': request.remote_addr
    }
    r.setex(f'session:{session_id}', 86400, json.dumps(session_data))  # 24hr
    return session_id

def get_session(session_id):
    data = r.get(f'session:{session_id}')
    if data:
        r.expire(f'session:{session_id}', 86400)  # Sliding expiry
        return json.loads(data)
    return None

def delete_session(session_id):
    r.delete(f'session:{session_id}')
```

### 22c. Rate Limiting

```python
def rate_limit(user_id, limit=100, window=60):
    key = f'rate:{user_id}:{int(time.time() // window)}'
    pipe = r.pipeline()
    pipe.incr(key)
    pipe.expire(key, window)
    count, _ = pipe.execute()
    return count <= limit

# Sliding window rate limiter (more accurate)
def sliding_rate_limit(user_id, limit=100, window=60):
    now = time.time()
    key = f'rate:sliding:{user_id}'
    pipe = r.pipeline()
    pipe.zremrangebyscore(key, 0, now - window)          # Remove old
    pipe.zadd(key, {str(now): now})                       # Add current
    pipe.zcard(key)                                       # Count
    pipe.expire(key, window)
    _, _, count, _ = pipe.execute()
    return count <= limit
```

### 22d. Distributed Lock

```python
# Redlock-style lock (single instance simple version)
def acquire_lock(resource, ttl=30):
    token = secrets.token_urlsafe()
    acquired = r.set(f'lock:{resource}', token, nx=True, ex=ttl)
    return token if acquired else None

def release_lock(resource, token):
    # Atomic check-and-delete using Lua
    lua_script = """
    if redis.call('GET', KEYS[1]) == ARGV[1] then
        return redis.call('DEL', KEYS[1])
    else
        return 0
    end
    """
    return r.eval(lua_script, 1, f'lock:{resource}', token)

token = acquire_lock('payment:process')
if token:
    try:
        process_payment()
    finally:
        release_lock('payment:process', token)
```

### 22e. Leaderboard

```python
def update_score(game_id, player_id, score):
    r.zincrby(f'leaderboard:{game_id}', score, player_id)

def get_top_players(game_id, n=10):
    return r.zrevrange(f'leaderboard:{game_id}', 0, n-1, withscores=True)

def get_player_rank(game_id, player_id):
    rank = r.zrevrank(f'leaderboard:{game_id}', player_id)
    score = r.zscore(f'leaderboard:{game_id}', player_id)
    return {'rank': rank + 1 if rank is not None else None, 'score': score}

def get_nearby_players(game_id, player_id, spread=5):
    rank = r.zrevrank(f'leaderboard:{game_id}', player_id)
    start = max(0, rank - spread)
    end = rank + spread
    return r.zrevrange(f'leaderboard:{game_id}', start, end, withscores=True)
```

### 22f. OTP / Temporary Tokens

```python
import random

def generate_otp(phone):
    otp = str(random.randint(100000, 999999))
    attempts_key = f'otp:attempts:{phone}'
    
    # Limit OTP generation (max 3 per hour)
    attempts = r.incr(attempts_key)
    r.expire(attempts_key, 3600)
    if attempts > 3:
        raise Exception('Too many OTP requests')
    
    r.setex(f'otp:{phone}', 300, otp)  # 5 min expiry
    return otp

def verify_otp(phone, entered_otp):
    stored = r.get(f'otp:{phone}')
    if stored and stored == entered_otp:
        r.delete(f'otp:{phone}')
        return True
    return False
```

---

## 23. Monitoring & Debugging

### Hands-on

```bash
# --- INFO command (most important!) ---
INFO                        # All sections
INFO server                 # Redis version, OS, uptime
INFO clients                # Connected clients, blocked clients
INFO memory                 # Memory usage, peak, fragmentation
INFO stats                  # Commands processed, hits, misses
INFO replication            # Master/replica status, lag
INFO cpu                    # CPU usage
INFO keyspace               # DB key counts, expiry stats

# Key metrics from INFO:
# used_memory_human          → Current memory
# mem_fragmentation_ratio    → >1.5 = fragmentation issue, <1 = swap!
# connected_clients          → Active connections
# instantaneous_ops_per_sec  → Current ops/sec
# keyspace_hits/misses       → Cache hit rate = hits/(hits+misses)
# evicted_keys               → Keys evicted due to maxmemory
# expired_keys               → Keys expired via TTL
# rdb_last_bgsave_status     → Last RDB save OK/err
# master_link_status         → up/down (replica only)

# --- MONITOR (real-time command stream) ---
MONITOR                     # ⚠️ High overhead! Only for debugging
# Capture and filter:
redis-cli monitor | grep "user:"

# --- Slow Log ---
CONFIG SET slowlog-log-slower-than 1000   # 1ms threshold
SLOWLOG GET 25              # Last 25 slow commands
SLOWLOG LEN
SLOWLOG RESET

# --- Latency ---
CONFIG SET latency-tracking yes
LATENCY LATEST              # Latest latency per event
LATENCY HISTORY             # History
LATENCY RESET

# --- Memory analysis ---
MEMORY USAGE mykey                  # Bytes for specific key
MEMORY DOCTOR                       # Diagnostic report
MEMORY MALLOC-STATS                 # Allocator stats

# --- Client connections ---
CLIENT LIST                         # All connected clients
CLIENT INFO                         # Current client info
CLIENT SETNAME "my-app-worker-1"    # Name your connection
CLIENT KILL ID <id>                 # Kill specific client
CLIENT PAUSE 5000                   # Pause all clients 5 sec (maintenance)

# --- DEBUG ---
DEBUG SLEEP 2               # Force Redis to sleep 2s (testing)
DEBUG JMAP                  # Memory map
DEBUG RELOAD                # Reload dataset (testing)

# --- Key analysis tools ---
# redis-cli --bigkeys          Find largest keys per type
redis-cli --bigkeys

# redis-cli --hotkeys          Find most accessed keys (LFU required)
redis-cli --hotkeys

# redis-cli --memkeys          Memory usage per key
redis-cli --memkeys

# RDB analysis (offline)
pip install rdbtools
rdb --command memory dump.rdb > memory.csv
rdb --command justkeys dump.rdb | head -100
```

### Interview Q&A
> **Q: How do you diagnose high memory usage in Redis?**
>
> Step-by-step: (1) `INFO memory` — check `used_memory_human`, `mem_fragmentation_ratio` (>1.5 means fragmentation; restart or `MEMORY PURGE` to defragment). (2) `redis-cli --bigkeys` — identify top memory consumers per data type. (3) `MEMORY USAGE key` on suspicious keys. (4) `INFO keyspace` — check total key counts. (5) Check `evicted_keys` in `INFO stats` — if high, `maxmemory` is too low. (6) Check `INFO clients` for `blocked_clients` and `client_recent_max_input_buffer`. (7) Enable keyspace notifications to track key patterns. (8) Use `rdbtools` for offline RDB analysis to get a full memory breakdown by key prefix.

---

## 24. Redis Modules

### 24a. RedisJSON

```bash
# Install: docker run -p 6379:6379 redislabs/rejson
# Or use Redis Stack

# Set JSON
JSON.SET user:1000 $ '{"name":"Rahul","age":28,"address":{"city":"Delhi","zip":"110001"},"tags":["developer","redis"]}'

# Get entire document
JSON.GET user:1000

# Get specific path
JSON.GET user:1000 $.name
JSON.GET user:1000 $.address.city

# Update specific field (no full re-write!)
JSON.SET user:1000 $.age 29
JSON.SET user:1000 $.address.city "Mumbai"

# Array operations
JSON.ARRAPPEND user:1000 $.tags '"expert"'
JSON.ARRLEN user:1000 $.tags
JSON.ARRPOP user:1000 $.tags

# Numeric operations
JSON.NUMINCRBY user:1000 $.age 1

# Type check
JSON.TYPE user:1000 $.name    # string
JSON.TYPE user:1000 $.tags    # array

# Delete field
JSON.DEL user:1000 $.address.zip

# Merge (Redis 7.4+)
JSON.MERGE user:1000 $ '{"phone":"9999999999"}'
```

### 24b. RediSearch

```bash
# Full-text search on Redis data
# Install: Redis Stack

# Create index on Hash
FT.CREATE idx:users ON HASH PREFIX 1 user:
  SCHEMA name TEXT WEIGHT 5.0
         age NUMERIC SORTABLE
         city TAG SORTABLE
         email TEXT NOSTEM

# Create index on JSON
FT.CREATE idx:products ON JSON PREFIX 1 product:
  SCHEMA $.name AS name TEXT
         $.price AS price NUMERIC SORTABLE
         $.category AS category TAG

# Index karne ke baad data add karo
HSET user:1 name "Rahul Kumar" age 28 city "Delhi" email "rahul@example.com"
HSET user:2 name "Priya Singh" age 25 city "Mumbai" email "priya@example.com"

# Search
FT.SEARCH idx:users "Rahul"
FT.SEARCH idx:users "@city:{Delhi}"
FT.SEARCH idx:users "@age:[25 30]"           # Numeric range
FT.SEARCH idx:users "rahul @city:{Delhi}"    # Combined
FT.SEARCH idx:users "*" SORTBY age ASC       # Sort
FT.SEARCH idx:users "priya" LIMIT 0 10       # Pagination

# Aggregation
FT.AGGREGATE idx:users "*"
  GROUPBY 1 @city
  REDUCE COUNT 0 AS user_count
  SORTBY 2 @user_count DESC

# Index info
FT.INFO idx:users
FT.DROPINDEX idx:users
```

### 24c. RedisBloom

```bash
# Bloom Filter — probabilistic data structure
# "Is this item in the set?" — no false negatives, small false positive rate
# Use case: Check if email/username already exists without DB query

BF.RESERVE myfilter 0.01 1000000   # 1% error rate, 1M capacity
BF.ADD myfilter "user@example.com"
BF.EXISTS myfilter "user@example.com"  # 1 (definitely in set or false positive)
BF.EXISTS myfilter "new@example.com"   # 0 (definitely NOT in set)
BF.MADD myfilter "a@a.com" "b@b.com"
BF.MEXISTS myfilter "a@a.com" "x@x.com"

# Count-Min Sketch — approximate frequency count
CMS.INITBYDIM cms 2000 5           # width, depth
CMS.INCRBY cms "item:laptop" 10
CMS.QUERY cms "item:laptop"        # Approximate count

# Top-K — approximate top K heavy hitters
TOPK.RESERVE topk 10 50 5 0.9
TOPK.ADD topk "item:phone" "item:laptop" "item:phone"
TOPK.LIST topk
TOPK.QUERY topk "item:phone"
```

---

## 25. Interview Q&A

### Core Concepts

**Q1: What is Redis and how is it different from Memcached?**
> Redis supports rich data structures (Lists, Sets, Sorted Sets, Hashes, Streams), persistence (RDB/AOF), replication, clustering, pub/sub, and Lua scripting. Memcached is simpler — only strings, no persistence, no replication built-in, but slightly lower memory overhead per key. Redis is the clear choice for most use cases today. Redis is also single-threaded for commands (uses I/O multiplexing) while Memcached is multi-threaded.

**Q2: Is Redis single-threaded? How does it handle concurrency?**
> Redis command execution is single-threaded (one command at a time, no locks needed). But since Redis 4.0, background operations (AOF rewrite, RDB save, lazy freeing) run in separate threads. Redis 6.0 added threaded I/O for network read/write — multiple threads handle socket I/O, but command execution remains single-threaded. This single-threaded model eliminates race conditions and context switching overhead, enabling 100K+ ops/sec on a single core.

**Q3: What happens when Redis runs out of memory?**
> Depends on `maxmemory-policy`: with `noeviction` (default), Redis returns errors on new write commands. With eviction policies (lru, lfu, ttl, random), Redis evicts keys according to the policy. Always set `maxmemory` and an appropriate `maxmemory-policy` in production. Monitor `evicted_keys` in `INFO stats` — high evictions indicate maxmemory is too low or data model needs optimization.

**Q4: How does Redis handle data persistence and what is the risk of data loss?**
> RDB: periodic snapshots — risk = loss of data since last snapshot (minutes). AOF with `everysec`: max 1 second loss. AOF with `always`: no loss but slowest. Hybrid mode (recommended): RDB + AOF. `no-appendfsync-on-rewrite yes` avoids AOF fsync during RDB/AOF rewrite (performance trade-off). In pure cache scenarios, disable persistence for max performance.

### Architecture & Scaling

**Q5: How would you scale Redis for 10 million users?**
> Vertical scaling first (more RAM). Then horizontal: (1) Redis Cluster — shard data across 6+ nodes (3 master + 3 replica). (2) Read replicas — route reads to replicas, writes to master. (3) Separate Redis instances by use case (cache cluster, session cluster, queue cluster). (4) Use connection pooling aggressively. (5) Optimize data structures — use Hashes for small objects (ziplist encoding), set appropriate `hash-max-ziplist-entries`. (6) Enable key expiry to keep dataset size manageable.

**Q6: How do you handle a Redis hotkey problem?**
> A hotkey is a single key receiving disproportionate traffic (e.g., a viral post's like count). Solutions: (1) **Local cache** in application layer — cache value in-process for 100ms, reducing Redis calls. (2) **Key sharding** — split into multiple keys: `hotkey:shard:0`, `hotkey:shard:1` ... `hotkey:shard:N`, write to random shard, read and aggregate. (3) **Redis Cluster** with read replicas — reads distributed across replicas. (4) **Client-side caching** (Redis 6+) — server tracks what each client has cached, invalidates on change. Detect hotkeys with `redis-cli --hotkeys` (requires LFU eviction).

**Q7: What is the N+1 problem in Redis and how to avoid it?**
> Making N separate Redis calls in a loop instead of batching. Solution: (1) **MGET/MSET** for string keys. (2) **Pipelining** — batch N commands into 1 round trip. (3) **Lua scripts** — do all processing server-side. (4) **HMGET** for Hash fields. Example: fetching 100 user profiles — don't loop 100 GET calls; use pipeline or MGET.

### Data Modeling

**Q8: How would you model a Twitter-like timeline in Redis?**
> Each user has a timeline as a Sorted Set: `ZADD timeline:user:1001 <timestamp> <tweet_id>`. On new tweet from followed user, fan-out: add tweet_id to all followers' timelines (push model). `ZREVRANGE timeline:user:1001 0 19 WITHSCORES` = latest 20 tweets. Trim to last 1000: `ZREMRANGEBYRANK timeline:user:1001 0 -1001`. For users with huge follower counts (celebrities), use pull model — merge celebrity + follower timelines at read time.

**Q9: How would you implement autocomplete/typeahead in Redis?**
> Store all possible completions in a Sorted Set with score 0 (lex ordering). Use `ZRANGEBYLEX` with prefix matching: `ZADD completions 0 "apple" 0 "application" 0 "apply"`. Search: `ZRANGEBYLEX completions "[app" "[app\xff" LIMIT 0 10`. Returns all entries starting with "app". For weighted (popular) suggestions, use actual scores and combine with `ZRANGEBYSCORE`.

**Q10: How do you handle cache stampede / thundering herd?**
> Cache stampede: when popular cached key expires, all requests simultaneously hit the DB. Solutions: (1) **Lock + recompute**: first request acquires a distributed lock (`SET lock:key token NX EX 5`), recomputes, releases lock; other requests wait or serve stale data. (2) **Probabilistic early expiration**: before key expires, with probability P proportional to closeness to expiry, one request preemptively refreshes. (3) **Stale-while-revalidate**: serve stale data while async refresh happens. (4) **Jitter on TTL**: `SETEX key (base_ttl + random(0, 60)) value` — spread out expirations.

### Operational

**Q11: How do you do a zero-downtime Redis migration?**
> (1) Set up new Redis instance. (2) Configure old as master, new as replica — data syncs. (3) Monitor replication lag: `INFO replication`. (4) Once lag = 0, switch application to write to new instance. (5) `REPLICAOF NO ONE` on new instance (promote to master). (6) Decommission old. For cluster migration: use `redis-cli --cluster` to add new nodes, reshard slots, remove old nodes. For cross-version: use DUMP/RESTORE or Redis's built-in migration tools.

**Q12: What is the OBJECT ENCODING command and why does it matter?**
> Redis uses different internal encodings based on data size/type for memory efficiency. `OBJECT ENCODING key` shows current encoding. Examples: small Lists use `listpack` (compact), large use `quicklist`. Small Hashes use `listpack`, large use `hashtable`. Small Sorted Sets use `listpack`, large use `skiplist`. Small integers use `int` encoding (shared object pool for 0-9999). This matters for memory optimization — keep objects small to stay in compact encodings. Tune via `hash-max-listpack-entries 128`, `zset-max-listpack-entries 128` etc.

**Q13: How would you implement a distributed lock correctly in Redis?**
> Single instance: `SET lock:resource unique_token NX EX 30`. Release with Lua (atomic check-then-delete): `if redis.call('GET',KEYS[1])==ARGV[1] then return redis.call('DEL',KEYS[1]) else return 0 end`. The unique_token prevents releasing another client's lock. The EX prevents deadlock if client crashes. For multi-node safety, use the **Redlock algorithm**: acquire lock on N/2+1 independent Redis nodes within TTL/2 time; valid only if majority succeeded and elapsed time < TTL. Use battle-tested libraries (Redlock-py, redlock-node) rather than implementing from scratch.

**Q14: Explain Redis keyspace notifications.**
> Redis can publish events to Pub/Sub channels when certain operations happen: key expiry, key set, key delete, etc. Configure: `CONFIG SET notify-keyspace-events "KEA"` (K=keyspace, E=keyevent, A=all events). Subscribe to: `__keyevent@0__:expired` to get notified when any key expires in DB 0. Use case: delayed job execution — set a key with TTL, subscribe to expiry events, trigger job when key expires. Caveat: notifications are best-effort (fire-and-forget), delivery not guaranteed if Redis restarts.

**Q15: What are Redis memory optimization techniques?**
> (1) Use Hashes for small objects — under `hash-max-listpack-entries` threshold, stored as compact listpack. Store user:1000:* fields as `HSET user:1000 name x age y` instead of separate string keys. (2) Use integer keys and values where possible — Redis interns integers 0-9999 as shared objects. (3) Set appropriate TTLs — don't let stale data accumulate. (4) Enable compression: `rdbcompression yes`. (5) Use `OBJECT FREQ` (LFU) to find rarely used keys. (6) Avoid storing large blobs — store references (IDs) and fetch from S3/DB. (7) `MEMORY PURGE` — return freed memory to OS. (8) Tune `maxmemory-samples` for eviction accuracy vs CPU.

---

## Quick Reference Cheatsheet

```
DATA TYPES & KEY COMMANDS:
PING                           Test connection
SET k v [EX s] [NX|XX]        Set string
GET/MGET/GETDEL/GETEX          Get variants
INCR/INCRBY/INCRBYFLOAT        Atomic increment
APPEND/STRLEN/GETRANGE          String ops
EXPIRE/TTL/PERSIST/PEXPIRE      TTL management
TYPE/EXISTS/DEL/RENAME/SCAN     Key management

LISTS:
RPUSH/LPUSH/RPUSHX/LPUSHX      Add elements
LRANGE/LLEN/LINDEX             Read
LPOP/RPOP/BLPOP/BRPOP          Remove
LMOVE/BLMOVE                   Atomic move
LINSERT/LSET/LREM/LTRIM        Modify

SETS:
SADD/SREM/SMOVE                Add/Remove
SMEMBERS/SCARD/SISMEMBER       Read
SPOP/SRANDMEMBER               Random
SINTER/SUNION/SDIFF            Set math

HASHES:
HSET/HDEL/HSETNX               Modify
HGET/HMGET/HGETALL/HKEYS/HVALS Read
HINCRBY/HINCRBYFLOAT           Numeric
HEXISTS/HLEN                   Meta

SORTED SETS:
ZADD [NX|XX|GT|LT] [CH]       Add/Update
ZRANGE/ZREVRANGE/ZRANGEBYSCORE Read
ZRANK/ZREVRANK/ZSCORE          Position
ZINCRBY/ZREM                   Modify
ZPOPMIN/ZPOPMAX/BZPOPMIN       Remove & return
ZUNIONSTORE/ZINTERSTORE        Set ops

STREAMS:
XADD/XLEN/XRANGE/XREVRANGE    Core ops
XREAD/XREADGROUP               Consumer
XGROUP CREATE/SETID/DELGROUP   Groups
XACK/XPENDING/XCLAIM           Delivery

SERVER:
INFO [section]                 Server stats
CONFIG GET/SET/REWRITE         Config
MONITOR                        Command stream
SLOWLOG GET/RESET              Slow queries
DEBUG SLEEP/RELOAD             Testing
MEMORY USAGE/DOCTOR/STATS      Memory
CLIENT LIST/KILL/SETNAME       Connections
BGSAVE/BGREWRITEAOF            Persistence
CLUSTER INFO/NODES             Cluster
SENTINEL masters/failover      Sentinel
ACL LIST/SETUSER/WHOAMI        Security
```

---

*Notes prepared for 3+ years experienced developers. All commands tested on Redis 7.x.*
*Refer: https://redis.io/docs | https://redis.io/commands*
