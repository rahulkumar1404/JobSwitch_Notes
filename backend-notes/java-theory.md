# ☕ Java Complete Notes — Zero to Advance
### 3 Years Experience Level | Hindi-Hinglish Explanations | Interview Q&A | Hands-on Code

> **Kaise use karein yeh notes:**
> - Theory → Hinglish mein samjho
> - Code → English mein likha hai (industry standard)
> - Interview Q&A → Har section ke end mein
> - Hands-on → Real scenarios with code
> - `💡 Tip` = Important interview point
> - `⚠️ Gotcha` = Common mistake / trick question

---

## 📚 Table of Contents

1. [Java Fundamentals & JVM Architecture](#1-java-fundamentals--jvm-architecture)
2. [Data Types, Variables & Operators](#2-data-types-variables--operators)
3. [Control Flow](#3-control-flow)
4. [Object-Oriented Programming (OOP)](#4-object-oriented-programming-oop)
5. [String & String Pool](#5-string--string-pool)
6. [Arrays & Varargs](#6-arrays--varargs)
7. [Exception Handling](#7-exception-handling)
8. [Collections Framework](#8-collections-framework)
9. [Generics](#9-generics)
10. [Java 8+ Features (Lambdas, Streams, Optional)](#10-java-8-features)
11. [Multithreading & Concurrency](#11-multithreading--concurrency)
12. [Java Memory Model & Garbage Collection](#12-java-memory-model--garbage-collection)
13. [Design Patterns](#13-design-patterns)
14. [Java I/O & NIO](#14-java-io--nio)
15. [JDBC & Database Connectivity](#15-jdbc--database-connectivity)
16. [Java Modules (JPMS) & New Features (9–21)](#16-java-modules--new-features-9-21)
17. [Common Interview Coding Problems](#17-common-interview-coding-problems)

---

## 1. Java Fundamentals & JVM Architecture

### 🔤 Java kya hai?

Java ek **platform-independent**, **object-oriented**, **strongly-typed** language hai. Iska main principle hai — **"Write Once, Run Anywhere (WORA)"**.

```
Source Code (.java)
      ↓  javac (compiler)
Bytecode (.class)
      ↓  JVM (Java Virtual Machine)
Platform-specific Machine Code
```

### 🏗️ JVM Architecture — Andar kya hota hai?

```
┌─────────────────────────────────────────────────────┐
│                     JVM                              │
│  ┌──────────────┐  ┌──────────────────────────────┐ │
│  │ Class Loader │  │      Runtime Data Areas       │ │
│  │  Subsystem   │  │  ┌──────────┐ ┌────────────┐ │ │
│  │ - Bootstrap  │  │  │  Method  │ │    Heap    │ │ │
│  │ - Extension  │  │  │  Area    │ │  (Objects) │ │ │
│  │ - Application│  │  └──────────┘ └────────────┘ │ │
│  └──────────────┘  │  ┌──────────┐ ┌────────────┐ │ │
│                    │  │  Stack   │ │  PC Reg.   │ │ │
│  ┌──────────────┐  │  │(per thrd)│ │            │ │ │
│  │  Execution   │  │  └──────────┘ └────────────┘ │ │
│  │   Engine     │  │  ┌──────────────────────────┐ │ │
│  │ - Interpreter│  │  │   Native Method Stack    │ │ │
│  │ - JIT        │  │  └──────────────────────────┘ │ │
│  │ - GC         │  └──────────────────────────────┘ │
│  └──────────────┘                                    │
└─────────────────────────────────────────────────────┘
```

**JVM ke main parts:**

| Component | Kya karta hai |
|-----------|---------------|
| **Class Loader** | `.class` files ko load karta hai memory mein |
| **Method Area** | Class metadata, static variables store hoti hain |
| **Heap** | Objects aur instance variables yahan rehte hain |
| **Stack** | Har thread ka apna stack — local variables, method calls |
| **PC Register** | Current executing instruction ka address |
| **JIT Compiler** | Hot bytecode ko native code mein convert karta hai (fast execution) |

### JDK vs JRE vs JVM

```
┌──────────────────────────────────────┐
│  JDK (Java Development Kit)          │
│  ┌────────────────────────────────┐  │
│  │  JRE (Java Runtime Environment)│  │
│  │  ┌──────────────────────────┐  │  │
│  │  │  JVM                     │  │  │
│  │  └──────────────────────────┘  │  │
│  │  + Libraries (rt.jar, etc.)    │  │
│  └────────────────────────────────┘  │
│  + javac, javadoc, jar, jdb, etc.    │
└──────────────────────────────────────┘
```

- **JVM** — Sirf execute karta hai
- **JRE** — JVM + Libraries (sirf run karne ke liye)
- **JDK** — JRE + Development tools (develop + run dono)

### Class Loading Process

```java
// Class loading 3 steps mein hota hai:
// 1. Loading → .class file read karo
// 2. Linking → Verify → Prepare (default values) → Resolve (symbolic refs)
// 3. Initialization → static blocks & static variable initialization

public class ClassLoadingDemo {
    static int x = 10; // Step 3 mein initialize hoga

    static {
        System.out.println("Static block runs during initialization");
        // x = 10 already set ho gaya before this
    }

    public static void main(String[] args) {
        System.out.println("x = " + x);
    }
}
```

💡 **Tip:** Class loading **lazy** hoti hai — jab first time use ho tab.

---

### 🎯 Interview Q&A — Section 1

**Q1: JVM kya hai aur kaise kaam karta hai?**
> JVM (Java Virtual Machine) ek abstract machine hai jo Java bytecode ko execute karta hai. Yeh platform-specific hota hai (Windows JVM alag, Linux JVM alag), isliye Java platform-independent hai. JVM Class Loader se class load karta hai, phir Execution Engine (Interpreter + JIT) se execute karta hai.

**Q2: JIT Compiler kya hai?**
> JIT (Just-In-Time) Compiler frequently executed code (hot code) ko bytecode se native machine code mein compile karta hai runtime pe. Yeh performance improve karta hai kyunki native code directly run hota hai bina interpretation ke.

**Q3: Stack vs Heap difference?**
> - **Stack:** Thread-specific, stores local variables & method call frames, LIFO, fast, auto memory management
> - **Heap:** Shared across all threads, stores objects, GC manage karta hai, slow access compared to stack

**Q4: Java platform independent kyun hai?**
> Kyunki `.java` → `.class` (bytecode) compile hota hai, aur yeh bytecode har platform ke JVM pe run ho sakta hai. JVM platform-specific hai, bytecode nahi.

**Q5: Class loader kya karta hai? Types?**
> Class loader `.class` files ko JVM memory mein load karta hai. 3 types:
> 1. **Bootstrap ClassLoader** — core Java classes (java.lang.*)
> 2. **Extension ClassLoader** — Java extensions (javax.*)
> 3. **Application ClassLoader** — application classes (our code)
> 
> Parent delegation model follow karta hai — pehle parent ko delegate karo.

---

## 2. Data Types, Variables & Operators

### Primitive Data Types

```java
public class PrimitiveTypes {
    public static void main(String[] args) {
        // Integer types
        byte b = 127;          // 1 byte, range: -128 to 127
        short s = 32767;       // 2 bytes
        int i = 2_147_483_647; // 4 bytes (underscore for readability - Java 7+)
        long l = 9_223_372_036_854_775_807L; // 8 bytes, suffix L required

        // Floating point
        float f = 3.14f;       // 4 bytes, suffix f required
        double d = 3.14159265; // 8 bytes (default for decimals)

        // Other
        char c = 'A';          // 2 bytes (Unicode, 0 to 65535)
        boolean bool = true;   // JVM-dependent, typically 1 bit/1 byte

        // ⚠️ GOTCHA: Integer overflow
        int maxVal = Integer.MAX_VALUE;
        System.out.println(maxVal + 1); // Prints -2147483648 (overflow!)

        // ⚠️ GOTCHA: Float precision
        System.out.println(0.1 + 0.2); // 0.30000000000000004 (use BigDecimal for money!)
    }
}
```

### Type Casting

```java
public class TypeCasting {
    public static void main(String[] args) {
        // Widening (implicit) — Automatic, no data loss
        int x = 100;
        long y = x;    // int → long, automatic
        double d = x;  // int → double, automatic
        // byte → short → int → long → float → double

        // Narrowing (explicit) — Manual, possible data loss
        double pi = 3.14159;
        int piInt = (int) pi; // Explicit cast, truncates decimal → 3
        System.out.println(piInt); // 3

        // ⚠️ GOTCHA: char to int and back
        char ch = 'A';
        int ascii = ch;  // 65 (widening)
        char back = (char) (ascii + 1); // 'B'

        // ⚠️ GOTCHA: Integer.parseInt vs Integer.valueOf
        int parsed = Integer.parseInt("42");     // returns primitive int
        Integer boxed = Integer.valueOf("42");   // returns Integer object (cached -128 to 127)
    }
}
```

### Autoboxing & Unboxing

```java
public class AutoboxingDemo {
    public static void main(String[] args) {
        // Autoboxing: primitive → Wrapper
        Integer a = 5;        // int → Integer (autoboxing)
        List<Integer> list = new ArrayList<>();
        list.add(10);         // int → Integer autoboxing here

        // Unboxing: Wrapper → primitive
        int val = a;          // Integer → int (unboxing)

        // ⚠️ GOTCHA: Integer cache (-128 to 127)
        Integer x = 127;
        Integer y = 127;
        System.out.println(x == y);   // true (same cached object)

        Integer p = 128;
        Integer q = 128;
        System.out.println(p == q);   // false (different objects!)
        System.out.println(p.equals(q)); // true (compare value)

        // ⚠️ GOTCHA: NullPointerException with unboxing
        Integer nullInt = null;
        // int result = nullInt; // Throws NullPointerException!
    }
}
```

### Operators

```java
public class OperatorsDemo {
    public static void main(String[] args) {
        // Arithmetic
        System.out.println(10 / 3);    // 3 (integer division)
        System.out.println(10.0 / 3);  // 3.3333...
        System.out.println(10 % 3);    // 1 (modulo)

        // Bitwise — Interview favorites!
        int a = 5;   // 0101
        int b = 3;   // 0011
        System.out.println(a & b);   // 1  (AND: 0001)
        System.out.println(a | b);   // 7  (OR:  0111)
        System.out.println(a ^ b);   // 6  (XOR: 0110)
        System.out.println(~a);      // -6 (NOT: flip all bits)
        System.out.println(a << 1);  // 10 (left shift = multiply by 2)
        System.out.println(a >> 1);  // 2  (right shift = divide by 2)
        System.out.println(-1 >>> 1);// max int (unsigned right shift)

        // Short-circuit operators
        boolean result = (null != null) && (null.equals("x")); // No NPE! (short-circuit)
        System.out.println(result); // false

        // Ternary
        int max = (a > b) ? a : b; // 5

        // instanceof (Java 16+ pattern matching)
        Object obj = "Hello";
        if (obj instanceof String str) { // Pattern matching - Java 16+
            System.out.println(str.toUpperCase());
        }
    }
}
```

---

### 🎯 Interview Q&A — Section 2

**Q1: `==` vs `.equals()` difference kya hai?**
> - `==` reference comparison karta hai (kya same object hai memory mein?)
> - `.equals()` content/value comparison karta hai
> - Primitives ke liye `==` value compare karta hai
> - Objects ke liye always use `.equals()` for value comparison

**Q2: Why is `String` not a primitive in Java?**
> String ek class hai (java.lang.String), primitive nahi. Isliye String ke objects hote hain, methods available hain (`.length()`, `.charAt()` etc.), aur String pool feature kaam karta hai.

**Q3: Integer overflow detect kaise karein?**
> Java 8+ mein `Math.addExact(a, b)` use karo jo `ArithmeticException` throw karta hai overflow pe. Ya `long` use karo.

**Q4: `float` vs `double` kab use karein?**
> - `double` prefer karo (more precision, default decimal type)
> - `float` sirf tab jab memory critical ho (graphics, large arrays)
> - Money calculations mein `BigDecimal` use karo

**Q5: What is the default value of local variables?**
> Local variables ka **koi default value nahi** hota — compile time error aata hai uninitialized local variable use karne par. Instance variables ke defaults: `int=0`, `boolean=false`, `Object=null`, `char='\u0000'`.

---

## 3. Control Flow

### Loops & Enhanced Features

```java
import java.util.*;

public class ControlFlowDemo {

    public static void main(String[] args) {

        // Enhanced for loop (for-each)
        List<String> names = List.of("Alice", "Bob", "Charlie");
        for (String name : names) {
            System.out.println(name);
        }

        // Labeled break/continue — tricky interview topic!
        outer:
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (j == 1) continue outer; // Skip inner, go to next outer iteration
                if (i == 2) break outer;    // Break out of BOTH loops
                System.out.println(i + "," + j);
            }
        }
        // Output: 0,0   1,0

        // Switch expression (Java 14+)
        int day = 3;
        String dayName = switch (day) {
            case 1 -> "Monday";
            case 2 -> "Tuesday";
            case 3 -> "Wednesday";
            default -> "Unknown";
        };

        // Switch with multiple labels (Java 14+)
        String type = switch (day) {
            case 1, 2, 3, 4, 5 -> "Weekday";
            case 6, 7 -> "Weekend";
            default -> throw new IllegalArgumentException("Invalid: " + day);
        };

        // Switch with yield (Java 13+)
        int result = switch (day) {
            case 1, 2 -> 10;
            default -> {
                int val = day * 2;
                yield val; // yield replaces return in switch expressions
            }
        };
    }

    // ⚠️ GOTCHA: switch fall-through
    static void switchFallthrough(int x) {
        switch (x) {
            case 1:
                System.out.println("One");
                // No break! Falls through to case 2
            case 2:
                System.out.println("Two");
                break;
            case 3:
                System.out.println("Three");
        }
        // If x=1: prints "One" AND "Two"
    }
}
```

---

### 🎯 Interview Q&A — Section 3

**Q1: `break` vs `continue` vs `return`?**
> - `break` — loop ya switch se bahar niklo
> - `continue` — current iteration skip karo, next pe jao
> - `return` — method se bahar niklo (value return karo)

**Q2: Switch mein String use kar sakte hain?**
> Haan! Java 7 se String switch mein use ho sakta hai. Internally `.hashCode()` + `.equals()` use hota hai.

**Q3: Infinite loop kab useful hai?**
> Server applications, event loops, daemon threads mein. `while(true)` ya `for(;;)` dono same hain.

---

## 4. Object-Oriented Programming (OOP)

### Classes & Objects

```java
// Well-designed class with all Java best practices
public class BankAccount {
    // Instance variables (state)
    private final String accountNumber; // final = immutable after construction
    private String ownerName;
    private double balance;
    private static int totalAccounts = 0; // Class-level variable

    // Constructor
    public BankAccount(String accountNumber, String ownerName, double initialBalance) {
        this.accountNumber = accountNumber;
        this.ownerName = ownerName;
        this.balance = initialBalance;
        totalAccounts++;
    }

    // Copy constructor
    public BankAccount(BankAccount other) {
        this.accountNumber = other.accountNumber + "_COPY";
        this.ownerName = other.ownerName;
        this.balance = other.balance;
    }

    // Methods
    public void deposit(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
        this.balance += amount;
    }

    public void withdraw(double amount) {
        if (amount <= 0) throw new IllegalArgumentException("Amount must be positive");
        if (amount > balance) throw new IllegalStateException("Insufficient funds");
        this.balance -= amount;
    }

    // Getters
    public String getAccountNumber() { return accountNumber; }
    public double getBalance() { return balance; }
    public static int getTotalAccounts() { return totalAccounts; }

    // toString, equals, hashCode — always override together!
    @Override
    public String toString() {
        return String.format("BankAccount{account='%s', owner='%s', balance=%.2f}",
                accountNumber, ownerName, balance);
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof BankAccount)) return false;
        BankAccount that = (BankAccount) o;
        return Objects.equals(accountNumber, that.accountNumber);
    }

    @Override
    public int hashCode() {
        return Objects.hash(accountNumber);
    }
}
```

### Inheritance

```java
// Abstract base class
public abstract class Animal {
    protected String name;
    protected int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Abstract method — subclass MUST implement
    public abstract String makeSound();

    // Concrete method — can be overridden
    public String getInfo() {
        return name + " (age: " + age + ")";
    }

    // final method — cannot be overridden
    public final String getSpecies() {
        return this.getClass().getSimpleName();
    }
}

public class Dog extends Animal {
    private String breed;

    public Dog(String name, int age, String breed) {
        super(name, age); // Must call super constructor
        this.breed = breed;
    }

    @Override
    public String makeSound() {
        return "Woof!";
    }

    @Override
    public String getInfo() {
        return super.getInfo() + ", Breed: " + breed; // Use super
    }
}

public class Cat extends Animal {
    @Override
    public String makeSound() {
        return "Meow!";
    }

    public Cat(String name, int age) {
        super(name, age);
    }
}

// Polymorphism in action
public class AnimalDemo {
    public static void main(String[] args) {
        Animal[] animals = {
            new Dog("Rex", 3, "German Shepherd"),
            new Cat("Whiskers", 5),
            new Dog("Buddy", 2, "Labrador")
        };

        for (Animal a : animals) {
            System.out.println(a.getInfo() + " says: " + a.makeSound());
            // Runtime polymorphism — correct makeSound() called automatically
        }

        // Downcasting
        Animal a = new Dog("Rex", 3, "Poodle");
        if (a instanceof Dog dog) { // Java 16 pattern matching
            System.out.println("Breed: " + dog.breed);
        }
    }
}
```

### Interfaces

```java
// Interface — Pure contract
public interface Flyable {
    // Constants (implicitly public static final)
    double MAX_ALTITUDE = 10000.0;

    // Abstract method
    void fly();

    // Default method (Java 8+) — implementation with default behavior
    default String getFlightStatus() {
        return "Flying at standard altitude";
    }

    // Static method (Java 8+)
    static boolean isAboveMaxAltitude(double altitude) {
        return altitude > MAX_ALTITUDE;
    }

    // Private method (Java 9+) — helper for default methods
    private void logFlight() {
        System.out.println("Logging flight...");
    }
}

public interface Swimmable {
    void swim();

    default String getSwimStatus() {
        return "Swimming";
    }
}

// Multiple interface implementation (Java's answer to multiple inheritance)
public class Duck extends Animal implements Flyable, Swimmable {
    public Duck(String name, int age) {
        super(name, age);
    }

    @Override
    public String makeSound() { return "Quack!"; }

    @Override
    public void fly() { System.out.println(name + " is flying!"); }

    @Override
    public void swim() { System.out.println(name + " is swimming!"); }

    // ⚠️ GOTCHA: Diamond problem — if both interfaces have same default method,
    // you MUST override in implementing class
}
```

### Encapsulation, Abstraction, Polymorphism

```java
// Encapsulation — Data hiding
public class Employee {
    private String name;
    private double salary;

    // Validation in setter = Encapsulation benefit
    public void setSalary(double salary) {
        if (salary < 0) throw new IllegalArgumentException("Salary cannot be negative");
        this.salary = salary;
    }

    public double getSalary() { return salary; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
}

// Polymorphism types:
// 1. Compile-time (Method Overloading)
public class Calculator {
    public int add(int a, int b) { return a + b; }
    public double add(double a, double b) { return a + b; }         // Overloaded
    public int add(int a, int b, int c) { return a + b + c; }      // Overloaded

    // ⚠️ GOTCHA: Return type alone is NOT valid overloading
    // public double add(int a, int b) { return a + b; } // Compile error!
}

// 2. Runtime (Method Overriding)
// Already shown above with makeSound()
```

### Access Modifiers

```java
/*
 * Access Modifier Summary:
 *
 * Modifier      | Same Class | Same Package | Subclass | Everywhere
 * --------------|------------|--------------|----------|----------
 * public        |     ✓      |      ✓       |    ✓     |    ✓
 * protected     |     ✓      |      ✓       |    ✓     |    ✗
 * default(none) |     ✓      |      ✓       |    ✗     |    ✗
 * private       |     ✓      |      ✗       |    ✗     |    ✗
 */

public class AccessDemo {
    public String pub = "public";
    protected String prot = "protected";
    String def = "default (package-private)";
    private String priv = "private";
}
```

### Static, Final, Abstract Keywords

```java
public class KeywordsDemo {

    // Static — class level, not instance level
    static int counter = 0;

    static {
        // Static initializer block — runs once when class is loaded
        System.out.println("Class loaded!");
        counter = 10;
    }

    // final variable — must be initialized once, never changed
    final int id;
    {
        // Instance initializer block
        id = ++counter; // Every new object gets unique id
    }

    // final method — cannot be overridden
    public final void display() {
        System.out.println("ID: " + id);
    }

    // static inner class
    static class Config {
        static final String VERSION = "1.0";
    }

    // Blank final — must be assigned in constructor
    final String name;
    public KeywordsDemo(String name) {
        this.name = name; // Assigned here
    }
}

// final class — cannot be extended (e.g., String, Integer)
final class ImmutablePoint {
    private final int x;
    private final int y;

    public ImmutablePoint(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }
    // No setters — immutable!
}
```

### Inner Classes

```java
public class OuterClass {
    private int outerField = 10;

    // 1. Non-static inner class (member inner class)
    class InnerClass {
        void display() {
            System.out.println("Outer field: " + outerField); // Can access outer
        }
    }

    // 2. Static nested class
    static class StaticNested {
        void display() {
            // Cannot access outerField directly — no reference to outer instance
            System.out.println("Static nested class");
        }
    }

    void method() {
        int localVar = 20; // must be effectively final for use in local class

        // 3. Local inner class
        class LocalClass {
            void display() {
                System.out.println("Local: " + localVar); // localVar must be effectively final
            }
        }
        new LocalClass().display();

        // 4. Anonymous inner class
        Runnable r = new Runnable() {
            @Override
            public void run() {
                System.out.println("Anonymous class running, outer: " + outerField);
            }
        };
        r.run();
    }

    public static void main(String[] args) {
        OuterClass outer = new OuterClass();

        // Creating inner class instance
        OuterClass.InnerClass inner = outer.new InnerClass();
        inner.display();

        // Creating static nested class instance
        OuterClass.StaticNested nested = new OuterClass.StaticNested();
        nested.display();
    }
}
```

---

### 🎯 Interview Q&A — Section 4

**Q1: Overloading vs Overriding?**
> - **Overloading** — Same class, same method name, different parameters. Compile-time polymorphism. Return type can differ. Static methods can be overloaded.
> - **Overriding** — Subclass redefines parent's method. Runtime polymorphism. Same signature. `@Override` annotation use karo. static, final, private methods override nahi ho sakte.

**Q2: Abstract class vs Interface?**

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Multiple inheritance | No | Yes |
| Constructor | Yes | No |
| State (fields) | Yes | Only constants |
| Access modifiers | Any | public/default |
| Default methods | Yes (Java 8+) | Yes (Java 8+) |
| `when to use` | IS-A, shared code | CAN-DO behavior |

**Q3: `super` keyword ke uses?**
> 1. `super()` — Parent constructor call (first statement in constructor)
> 2. `super.method()` — Parent's overridden method call
> 3. `super.field` — Parent's hidden field access

**Q4: Can we override static methods?**
> Nahi! Static methods **method hiding** karte hain, override nahi. Runtime ke time pe object ka actual type decide nahi hota static ke liye — compile time pe decide hota hai.

**Q5: What is a marker interface?**
> Koi method nahi hota — sirf marker/tag ka kaam karta hai. Examples: `Serializable`, `Cloneable`, `Remote`. Java 5 se annotations better alternative hain, lekin legacy code mein dikhte hain.

**Q6: Constructor chaining kya hai?**
> `this()` same class ke doosre constructor ko call karta hai. `super()` parent class ke constructor ko. Dono first statement hona chahiye. Circular calls compile error deta hai.

**Q7: Composition vs Inheritance?**
> **"Favor composition over inheritance"** — GoF principle.
> - Inheritance = IS-A relationship (Dog IS-A Animal)
> - Composition = HAS-A relationship (Car HAS-A Engine)
> - Composition more flexible, avoids tight coupling, supports runtime change

**Q8: Can abstract class have constructor?**
> Haan! Abstract class ka constructor hota hai, lekin directly instantiate nahi kar sakte. Subclass constructor `super()` se call karta hai.

---

## 5. String & String Pool

### String Internals

```java
public class StringDemo {
    public static void main(String[] args) {
        // String Pool (String Interning)
        String s1 = "Hello";          // Goes to String Pool
        String s2 = "Hello";          // Same pool reference
        String s3 = new String("Hello"); // New object on Heap

        System.out.println(s1 == s2);       // true (same pool reference)
        System.out.println(s1 == s3);       // false (different objects)
        System.out.println(s1.equals(s3));  // true (same content)

        // intern() — force string to pool
        String s4 = s3.intern();
        System.out.println(s1 == s4);  // true (now from pool)

        // ⚠️ String Immutability
        String str = "Hello";
        str.concat(" World"); // Creates NEW string, doesn't change str!
        System.out.println(str); // Still "Hello"

        str = str.concat(" World"); // Now str points to new string
        System.out.println(str); // "Hello World"
    }
}
```

### String Methods — Commonly Used

```java
public class StringMethods {
    public static void main(String[] args) {
        String s = "  Hello, World!  ";

        // Basic operations
        System.out.println(s.length());           // 19
        System.out.println(s.trim());             // "Hello, World!"
        System.out.println(s.strip());            // "Hello, World!" (Java 11, handles Unicode whitespace)
        System.out.println(s.toUpperCase());      // "  HELLO, WORLD!  "
        System.out.println(s.toLowerCase());      // "  hello, world!  "
        System.out.println(s.charAt(2));          // 'H'
        System.out.println(s.indexOf("World"));  // 9
        System.out.println(s.contains("Hello")); // true
        System.out.println(s.startsWith("  H")); // true
        System.out.println(s.endsWith("!  "));   // true

        // Substring
        System.out.println(s.substring(2, 7));   // "Hello"
        System.out.println(s.substring(2));      // "Hello, World!  "

        // Replace
        System.out.println(s.replace("Hello", "Hi")); // "  Hi, World!  "
        System.out.println("aabbcc".replaceAll("[abc]", "X")); // "XXXXXX" (regex)

        // Split
        String csv = "a,b,c,d";
        String[] parts = csv.split(",");          // ["a", "b", "c", "d"]
        String[] limited = csv.split(",", 2);     // ["a", "b,c,d"]

        // Join (Java 8+)
        String joined = String.join("-", "a", "b", "c"); // "a-b-c"
        String fromList = String.join(", ", List.of("x", "y", "z")); // "x, y, z"

        // Java 11+ methods
        System.out.println("".isEmpty());         // true
        System.out.println("  ".isBlank());       // true (isEmpty() returns false for spaces)
        System.out.println("Hello\nWorld".lines().count()); // 2
        System.out.println("ha".repeat(3));       // "hahaha"

        // String.format / formatted (Java 15+)
        String formatted = String.format("Name: %s, Age: %d, Score: %.2f", "Alice", 25, 98.5);
        // Java 15+:
        String formatted2 = "Name: %s, Age: %d".formatted("Alice", 25);

        // char[] to String and back
        char[] chars = {'H', 'i'};
        String fromChars = new String(chars); // "Hi"
        char[] backToChars = "Hi".toCharArray();
    }
}
```

### StringBuilder vs StringBuffer

```java
public class StringBuilderDemo {
    public static void main(String[] args) {
        // ⚠️ String concatenation in loop — BAD (creates many objects)
        String bad = "";
        for (int i = 0; i < 1000; i++) {
            bad += i; // Creates new String object every iteration!
        }

        // StringBuilder — GOOD (mutable, not thread-safe, faster)
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 1000; i++) {
            sb.append(i);
        }
        String result = sb.toString();

        // StringBuilder methods
        StringBuilder builder = new StringBuilder("Hello");
        builder.append(" World");       // "Hello World"
        builder.insert(5, ",");         // "Hello, World"
        builder.delete(5, 6);           // "Hello World"
        builder.reverse();              // "dlroW olleH"
        builder.replace(0, 4, "Hi");    // "Hi W olleH" (wait, let's be accurate)

        // StringBuilder capacity
        StringBuilder sb2 = new StringBuilder(100); // Initial capacity 100
        System.out.println(sb2.capacity()); // 100
        System.out.println(sb2.length());   // 0

        // StringBuffer — thread-safe (synchronized), use when multiple threads
        StringBuffer sbuf = new StringBuffer("Thread safe");
        // All methods are synchronized — slower than StringBuilder
    }
}

/*
 * String vs StringBuilder vs StringBuffer:
 *
 * Feature        | String      | StringBuilder | StringBuffer
 * ---------------|-------------|---------------|-------------
 * Mutability     | Immutable   | Mutable       | Mutable
 * Thread Safety  | Yes         | No            | Yes
 * Performance    | Slow (++)   | Fastest       | Slow (sync)
 * Use When       | Fixed text  | Single thread | Multi thread
 */
```

### Text Blocks (Java 13+)

```java
public class TextBlockDemo {
    public static void main(String[] args) {
        // Old way — ugly
        String json = "{\n  \"name\": \"Alice\",\n  \"age\": 25\n}";

        // Text Block (Java 13+ preview, 15+ stable)
        String jsonBlock = """
                {
                  "name": "Alice",
                  "age": 25
                }
                """;

        String html = """
                <html>
                    <body>
                        <p>Hello</p>
                    </body>
                </html>
                """;

        // Indentation is managed by the closing """
        // \  at end of line = line continuation (no newline)
        String longLine = """
                This is a very long line \
                that continues here.
                """;
    }
}
```

---

### 🎯 Interview Q&A — Section 5

**Q1: String immutable kyun hai Java mein?**
> 1. **Security** — String as HashMap key, class loading, network connections safely use hoti hai
> 2. **String Pool** — Same string multiple references share kar sakti hain
> 3. **HashCode caching** — Hash ek baar calculate, cache ho jata hai
> 4. **Thread Safety** — Immutable objects inherently thread-safe

**Q2: `String s = new String("hello")` — kitne objects create honge?**
> **2 objects** (possibly): Ek String Pool mein (agar "hello" already nahi hai), ek Heap pe `new` ke through. Agar pool mein already hai to sirf 1 heap pe.

**Q3: `+` operator String pe kaise kaam karta hai?**
> Compile time pe `StringBuilder` use karta hai internally. `"a" + "b" + "c"` becomes `new StringBuilder().append("a").append("b").append("c").toString()`. Lekin loop mein yeh inefficient hai — explicitly `StringBuilder` use karo.

**Q4: `equals()` vs `equalsIgnoreCase()` vs `compareTo()`?**
> - `equals()` — exact match, case-sensitive
> - `equalsIgnoreCase()` — case-insensitive match
> - `compareTo()` — lexicographic comparison, returns -/0/+ (used for sorting)

**Q5: String reverse karo without reverse() method?**
```java
String original = "Hello";
StringBuilder sb = new StringBuilder(original);
// Manual approach:
char[] chars = original.toCharArray();
int left = 0, right = chars.length - 1;
while (left < right) {
    char temp = chars[left];
    chars[left] = chars[right];
    chars[right] = temp;
    left++; right--;
}
String reversed = new String(chars); // "olleH"
```

---

## 6. Arrays & Varargs

```java
import java.util.Arrays;

public class ArraysDemo {
    public static void main(String[] args) {
        // Declaration & initialization
        int[] arr = new int[5];              // Default: 0s
        int[] arr2 = {1, 2, 3, 4, 5};       // Inline init
        int[] arr3 = new int[]{1, 2, 3};     // Another way

        // 2D array
        int[][] matrix = new int[3][3];
        int[][] matrix2 = {{1,2,3}, {4,5,6}, {7,8,9}};

        // Jagged array (rows of different length)
        int[][] jagged = new int[3][];
        jagged[0] = new int[]{1};
        jagged[1] = new int[]{2, 3};
        jagged[2] = new int[]{4, 5, 6};

        // Arrays utility methods
        Arrays.sort(arr2);                              // In-place sort
        System.out.println(Arrays.toString(arr2));     // [1, 2, 3, 4, 5]
        System.out.println(Arrays.binarySearch(arr2, 3)); // 2 (index)
        int[] copy = Arrays.copyOf(arr2, 3);           // [1, 2, 3]
        int[] rangeCopy = Arrays.copyOfRange(arr2, 1, 4); // [2, 3, 4]
        Arrays.fill(arr, 7);                            // [7, 7, 7, 7, 7]

        // ⚠️ Array copy — shallow vs deep
        int[][] src = {{1,2}, {3,4}};
        int[][] shallow = Arrays.copyOf(src, src.length);
        shallow[0][0] = 99; // Changes src too! (shallow copy)

        // Deep copy
        int[][] deep = new int[src.length][];
        for (int i = 0; i < src.length; i++) {
            deep[i] = Arrays.copyOf(src[i], src[i].length);
        }

        // Array sort with comparator (Integer array)
        Integer[] nums = {3, 1, 4, 1, 5, 9};
        Arrays.sort(nums, (a, b) -> b - a); // Descending
    }

    // Varargs — variable arguments
    public static int sum(int... numbers) { // Can pass 0 or more ints
        int total = 0;
        for (int n : numbers) total += n;
        return total;
    }

    // Calling:
    // sum()           → 0
    // sum(1, 2, 3)    → 6
    // sum(new int[]{1,2,3}) → 6 (array works too)

    // ⚠️ Varargs rules:
    // - Only one varargs parameter per method
    // - Must be the last parameter
    public static void print(String prefix, int... numbers) { /* OK */ }
    // public static void bad(int... a, int... b) { } // Compile error!
}
```

---

### 🎯 Interview Q&A — Section 6

**Q1: Array vs ArrayList difference?**

| | Array | ArrayList |
|--|-------|-----------|
| Size | Fixed | Dynamic |
| Type | Primitive + Object | Object only |
| Performance | Faster | Slightly slower |
| Methods | length | size(), add(), remove() |

**Q2: Array pass-by-value ya pass-by-reference?**
> Java always **pass-by-value**. Lekin for objects/arrays, the value is the **reference** (memory address). So array ke elements modify ho sakte hain inside method, lekin reference reassign karne se original array nahi badhega.

---

## 7. Exception Handling

### Exception Hierarchy

```
Throwable
├── Error (Don't catch these — JVM problems)
│   ├── OutOfMemoryError
│   ├── StackOverflowError
│   └── VirtualMachineError
└── Exception
    ├── Checked Exceptions (must handle/declare)
    │   ├── IOException
    │   ├── SQLException
    │   ├── ClassNotFoundException
    │   └── FileNotFoundException
    └── RuntimeException (Unchecked — optional to handle)
        ├── NullPointerException
        ├── ArrayIndexOutOfBoundsException
        ├── ClassCastException
        ├── ArithmeticException
        ├── NumberFormatException
        └── IllegalArgumentException
```

### Exception Handling — Complete

```java
import java.io.*;

public class ExceptionHandlingDemo {

    // Checked exception — caller must handle or declare
    public static String readFile(String path) throws IOException {
        // try-with-resources (Java 7+) — auto closes resources
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            return br.readLine();
        }
        // br.close() is called automatically, even if exception occurs
    }

    // Multiple resources
    public static void copyFile(String src, String dest) throws IOException {
        try (InputStream in = new FileInputStream(src);
             OutputStream out = new FileOutputStream(dest)) {
            in.transferTo(out); // Java 9+
        }
    }

    public static void main(String[] args) {
        // Multi-catch (Java 7+)
        try {
            String content = readFile("test.txt");
            int num = Integer.parseInt(content);
        } catch (FileNotFoundException e) {
            System.err.println("File not found: " + e.getMessage());
        } catch (NumberFormatException | IOException e) { // Multi-catch
            System.err.println("Parse or IO error: " + e.getMessage());
        } finally {
            System.out.println("Finally block — always executes");
            // ⚠️ GOTCHA: finally runs even with return in try/catch
            // ⚠️ GOTCHA: finally doesn't run only if System.exit() called
        }

        // Exception chaining — preserve original cause
        try {
            try {
                int[] arr = new int[5];
                arr[10] = 1; // ArrayIndexOutOfBoundsException
            } catch (ArrayIndexOutOfBoundsException e) {
                throw new RuntimeException("Array operation failed", e); // Wrap with cause
            }
        } catch (RuntimeException e) {
            System.out.println("Cause: " + e.getCause()); // Original exception
        }
    }

    // Custom exception
    public static class InsufficientFundsException extends Exception {
        private final double amount;

        public InsufficientFundsException(double amount) {
            super("Insufficient funds. Needed: " + amount);
            this.amount = amount;
        }

        public double getAmount() { return amount; }
    }

    // Custom unchecked exception
    public static class InvalidAgeException extends RuntimeException {
        public InvalidAgeException(String message) {
            super(message);
        }
    }
}
```

### Exception Best Practices

```java
public class ExceptionBestPractices {

    // ✅ DO: Specific exception types catch karo
    public void good() {
        try {
            riskyOperation();
        } catch (SpecificException e) { // Specific
            handleSpecific(e);
        }
    }

    // ❌ DON'T: Catch generic Exception (swallows unexpected errors)
    public void bad() {
        try {
            riskyOperation();
        } catch (Exception e) { // Too broad
            e.printStackTrace(); // Don't do this in production!
        }
    }

    // ❌ DON'T: Empty catch blocks — silent failure!
    public void terrible() {
        try {
            riskyOperation();
        } catch (Exception e) {
            // Do nothing — this is horrible!
        }
    }

    // ✅ DO: Log and rethrow if you can't handle
    public void correct() throws Exception {
        try {
            riskyOperation();
        } catch (Exception e) {
            logger.error("Operation failed", e);
            throw e; // Rethrow
        }
    }

    // ✅ DO: Use finally or try-with-resources for cleanup
    // ✅ DO: Don't use exceptions for flow control
    // ✅ DO: Never throw from finally block
    // ✅ DO: Custom exceptions should have message and cause

    private void riskyOperation() throws SpecificException {}
    private void handleSpecific(SpecificException e) {}
    private static final java.util.logging.Logger logger =
        java.util.logging.Logger.getLogger(ExceptionBestPractices.class.getName());

    static class SpecificException extends Exception {
        SpecificException(String msg) { super(msg); }
    }
}
```

---

### 🎯 Interview Q&A — Section 7

**Q1: Checked vs Unchecked exceptions?**
> - **Checked** — Compile time pe check hota hai. Method ko `throws` declare karna padta hai ya `try-catch` use karna. Example: `IOException`, `SQLException`
> - **Unchecked (RuntimeException)** — Compile time check nahi. Programming errors. Example: `NPE`, `ClassCastException`
> - **Error** — JVM level problems, recover nahi kar sakte. Example: `OutOfMemoryError`

**Q2: `finally` block kab nahi chalega?**
> `System.exit()` call hone par, ya JVM crash hone par.

**Q3: Exception swallowing kya hai?**
> Empty catch block ya exception ko catch karke kuch nahi karna. Production mein bahut bura practice — bugs debug karna impossible ho jata hai.

**Q4: Can we have try without catch?**
> Haan — `try-finally` valid hai (no catch). Java 7 se `try-with-resources` bhi catch ke bina valid hai.

**Q5: `throw` vs `throws`?**
> - `throw` — Actual exception object throw karta hai (`throw new IOException()`)
> - `throws` — Method signature mein declare karta hai ki yeh method yeh exception throw kar sakta hai

**Q6: StackOverflowError kab aata hai?**
> Infinite recursion se. Stack pe frames stack hote rehte hain — jab stack full ho jata hai to `StackOverflowError`.

---

## 8. Collections Framework

### Collections Hierarchy

```
Collection (Interface)
├── List (Interface) — Ordered, duplicates allowed
│   ├── ArrayList
│   ├── LinkedList
│   └── Vector (synchronized, legacy)
│       └── Stack
├── Set (Interface) — No duplicates
│   ├── HashSet (unordered)
│   ├── LinkedHashSet (insertion order)
│   └── TreeSet (sorted)
└── Queue (Interface)
    ├── LinkedList
    ├── PriorityQueue
    └── Deque
        ├── ArrayDeque
        └── LinkedList

Map (Interface) — Key-Value pairs
├── HashMap (unordered)
├── LinkedHashMap (insertion/access order)
├── TreeMap (sorted by key)
├── Hashtable (synchronized, legacy)
└── ConcurrentHashMap (thread-safe)
```

### ArrayList — Deep Dive

```java
import java.util.*;
import java.util.stream.Collectors;

public class ArrayListDemo {
    public static void main(String[] args) {
        // Creation
        List<String> list = new ArrayList<>();              // Default capacity 10
        List<String> list2 = new ArrayList<>(100);         // Initial capacity 100
        List<String> list3 = new ArrayList<>(List.of("a", "b", "c")); // From collection

        // ⚠️ GOTCHA: List.of() is IMMUTABLE, Arrays.asList() is FIXED SIZE
        List<String> immutable = List.of("a", "b");     // No add/remove
        List<String> fixedSize = Arrays.asList("a", "b"); // No add/remove, but can set()
        List<String> mutable = new ArrayList<>(List.of("a", "b")); // Truly mutable

        // CRUD operations
        list.add("Hello");
        list.add(0, "First");        // Insert at index
        list.addAll(List.of("x","y","z"));
        list.set(0, "Updated");      // Replace
        list.remove(0);              // Remove by index
        list.remove("Hello");        // Remove by value (first occurrence)
        String val = list.get(1);
        int idx = list.indexOf("x"); // -1 if not found
        boolean contains = list.contains("x");
        int size = list.size();

        // Iteration methods
        // 1. for-each (simplest)
        for (String s : list) System.out.println(s);

        // 2. Iterator (safe remove during iteration)
        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            String s = it.next();
            if (s.startsWith("x")) it.remove(); // Safe removal
        }

        // 3. ListIterator (bidirectional)
        ListIterator<String> lit = list.listIterator(list.size()); // Start from end
        while (lit.hasPrevious()) {
            System.out.println(lit.previous()); // Traverse backwards
        }

        // Sorting
        List<Integer> nums = new ArrayList<>(Arrays.asList(3,1,4,1,5,9,2,6));
        Collections.sort(nums);                        // Natural order
        nums.sort(Comparator.reverseOrder());          // Reverse
        nums.sort(Comparator.comparingInt(Math::abs)); // Custom

        // Sublist (backed by original list)
        List<Integer> sub = nums.subList(0, 3); // [first 3 elements]
        sub.clear(); // This modifies the original list too!

        // Converting
        Object[] array = list.toArray();
        String[] strArr = list.toArray(String[]::new); // Java 11 style

        // ⚠️ ConcurrentModificationException
        List<String> names = new ArrayList<>(Arrays.asList("a","b","c"));
        // WRONG:
        // for (String s : names) { if(s.equals("b")) names.remove(s); } // CME!
        // CORRECT:
        names.removeIf(s -> s.equals("b")); // Java 8+
    }
}
```

### HashMap — Deep Dive

```java
public class HashMapDemo {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();

        // CRUD
        map.put("Alice", 25);
        map.put("Bob", 30);
        map.put("Alice", 26);       // Overwrites previous value
        map.putIfAbsent("Charlie", 35); // Only puts if key absent

        int age = map.get("Alice");           // 26
        int defaultAge = map.getOrDefault("Dave", 0); // 0 (not found)

        map.remove("Bob");                     // Remove by key
        boolean removed = map.remove("Alice", 99); // Remove only if value matches (false, value is 26)

        // Java 8+ methods
        map.computeIfAbsent("Dave", k -> k.length()); // Compute if absent
        map.computeIfPresent("Charlie", (k, v) -> v + 1); // Update if present
        map.compute("Alice", (k, v) -> v == null ? 1 : v + 1); // Always compute

        map.merge("Bob", 1, Integer::sum); // Merge — adds 1 if key absent, sums if present

        // Iteration
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + " → " + entry.getValue());
        }
        map.forEach((k, v) -> System.out.println(k + " → " + v)); // Lambda

        // Views
        Set<String> keys = map.keySet();
        Collection<Integer> values = map.values();
        Set<Map.Entry<String,Integer>> entries = map.entrySet();
    }
}
```

### HashMap Internals — Interview Favorite!

```java
/*
 * HashMap kaise kaam karta hai internally?
 *
 * 1. Array of Node<K,V>[] (default size 16) — buckets
 * 2. Key ka hashCode() calculate karo
 * 3. Bucket index: index = (n-1) & hash
 * 4. Same bucket pe collision → LinkedList (Java 7)
 *    Java 8+: 8 elements ke baad LinkedList → Red-Black Tree (O(log n))
 *
 * Load Factor = 0.75 (default)
 * When size > capacity * loadFactor → rehash (double size)
 *
 * Performance:
 * - Average: O(1) for get/put
 * - Worst (all same hash): O(n) Java 7, O(log n) Java 8+
 *
 * HashMap is NOT thread-safe!
 * Use ConcurrentHashMap or Collections.synchronizedMap() for thread safety.
 */

// Word frequency counter — common HashMap use case
public class WordFrequency {
    public static Map<String, Integer> countWords(String text) {
        Map<String, Integer> freq = new HashMap<>();
        for (String word : text.split("\\s+")) {
            freq.merge(word.toLowerCase(), 1, Integer::sum);
            // Equivalent to:
            // freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        return freq;
    }
}
```

### LinkedHashMap, TreeMap, HashSet, TreeSet

```java
public class OtherCollections {
    public static void main(String[] args) {
        // LinkedHashMap — maintains insertion order
        Map<String, Integer> linked = new LinkedHashMap<>();
        linked.put("C", 3); linked.put("A", 1); linked.put("B", 2);
        System.out.println(linked); // {C=3, A=1, B=2} — insertion order maintained

        // Access-order LinkedHashMap (LRU Cache base)
        Map<String, Integer> lru = new LinkedHashMap<>(16, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<String,Integer> eldest) {
                return size() > 3; // Max 3 entries
            }
        };

        // TreeMap — sorted by key (natural order or Comparator)
        Map<String, Integer> tree = new TreeMap<>();
        tree.put("C", 3); tree.put("A", 1); tree.put("B", 2);
        System.out.println(tree); // {A=1, B=2, C=3} — sorted!

        // TreeMap specific methods
        TreeMap<Integer, String> tmap = new TreeMap<>();
        tmap.put(1, "one"); tmap.put(3, "three"); tmap.put(5, "five");
        System.out.println(tmap.floorKey(4));  // 3 (largest key ≤ 4)
        System.out.println(tmap.ceilingKey(2)); // 3 (smallest key ≥ 2)
        System.out.println(tmap.headMap(4));   // {1=one, 3=three}
        System.out.println(tmap.tailMap(3));   // {3=three, 5=five}

        // HashSet
        Set<String> set = new HashSet<>();
        set.add("Alice"); set.add("Bob"); set.add("Alice"); // Duplicate ignored
        System.out.println(set.size()); // 2

        // TreeSet — sorted set
        Set<Integer> sortedSet = new TreeSet<>(Set.of(3,1,4,1,5,9,2,6));
        System.out.println(sortedSet); // [1, 2, 3, 4, 5, 6, 9]

        // PriorityQueue — min heap by default
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        pq.offer(5); pq.offer(1); pq.offer(3);
        System.out.println(pq.poll()); // 1 (smallest)
        System.out.println(pq.poll()); // 3

        // Max heap
        PriorityQueue<Integer> maxPQ = new PriorityQueue<>(Comparator.reverseOrder());
        maxPQ.offer(5); maxPQ.offer(1); maxPQ.offer(3);
        System.out.println(maxPQ.poll()); // 5 (largest)

        // ArrayDeque — better than Stack and LinkedList for stack/queue use
        Deque<String> deque = new ArrayDeque<>();
        deque.push("first");       // Stack push (add to front)
        deque.push("second");
        System.out.println(deque.pop()); // "second" (LIFO)
        deque.offer("last");       // Queue add (add to back)
        System.out.println(deque.poll()); // "first" (FIFO)
    }
}
```

### Collections Utility Class

```java
public class CollectionsUtility {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(Arrays.asList(3,1,4,1,5,9,2,6));

        Collections.sort(list);               // Sort
        Collections.reverse(list);            // Reverse
        Collections.shuffle(list);            // Random shuffle
        Collections.shuffle(list, new Random(42)); // Seeded shuffle

        System.out.println(Collections.min(list)); // Minimum
        System.out.println(Collections.max(list)); // Maximum
        System.out.println(Collections.frequency(list, 1)); // Count of 1s

        Collections.fill(list, 0);            // Fill all with 0
        Collections.swap(list, 0, 1);         // Swap elements
        Collections.nCopies(5, "hello");      // [hello,hello,hello,hello,hello]

        // Unmodifiable wrappers
        List<String> unmod = Collections.unmodifiableList(new ArrayList<>(List.of("a","b")));
        // unmod.add("c"); // Throws UnsupportedOperationException

        // Synchronized wrappers
        List<String> sync = Collections.synchronizedList(new ArrayList<>());
        // Thread-safe, but still need external sync for iteration

        // Singleton collections
        List<String> single = Collections.singletonList("only"); // Immutable, 1 element
        Set<String> emptySet = Collections.emptySet();
    }
}
```

---

### 🎯 Interview Q&A — Section 8

**Q1: ArrayList vs LinkedList kab use karein?**
> - **ArrayList:** Random access frequent ho (O(1) get), add/remove mostly at end. Memory efficient.
> - **LinkedList:** Frequent insert/delete at beginning or middle (O(1) after pointer found). Queue, Deque implement karna ho.

**Q2: HashMap null key aur null value support karta hai?**
> Haan! HashMap ek null key aur multiple null values support karta hai. TreeMap null key support nahi karta (NullPointerException). Hashtable null key/value support nahi karta.

**Q3: HashMap thread-safe kaise banate hain?**
> 1. `ConcurrentHashMap` — best option, segment-level locking
> 2. `Collections.synchronizedMap(map)` — wrap karo, slow
> 3. `Hashtable` — legacy, avoid karo

**Q4: Iterator fail-fast vs fail-safe?**
> - **Fail-fast** — Iteration ke dauraan modification detect kare to `ConcurrentModificationException` throw karta hai. ArrayList, HashMap iterators.
> - **Fail-safe** — Copy pe iterate karta hai, modification allow. ConcurrentHashMap, CopyOnWriteArrayList.

**Q5: Comparator vs Comparable?**
> - **Comparable** — Natural ordering. Class khud implement karta hai `compareTo()`. Ek hi ordering possible.
> - **Comparator** — Custom ordering. External class/lambda. Multiple different orderings possible. `Collections.sort(list, comparator)`.

**Q6: HashSet duplicate kaise detect karta hai?**
> 1. `hashCode()` calculate karo
> 2. Same hash bucket mein koi existing element hai?
> 3. Agar haan, `equals()` se compare karo
> 4. `equals()` true return kare to duplicate — nahi add karta
> 
> Isliye custom objects ko HashSet mein use karte time `hashCode()` AND `equals()` dono override karne chahiye.

---

## 9. Generics

```java
import java.util.function.Function;

// Generic class
public class Box<T> {
    private T content;

    public void set(T content) { this.content = content; }
    public T get() { return content; }

    // Generic method inside generic class
    public <R> R transform(Function<T, R> func) {
        return func.apply(content);
    }
}

// Multiple type parameters
public class Pair<A, B> {
    private A first;
    private B second;

    public Pair(A first, B second) {
        this.first = first;
        this.second = second;
    }

    public A getFirst() { return first; }
    public B getSecond() { return second; }

    @Override
    public String toString() { return "(" + first + ", " + second + ")"; }
}

// Bounded type parameters
public class NumberBox<T extends Number> { // T must be Number or subclass
    private T value;
    public NumberBox(T value) { this.value = value; }
    public double doubleValue() { return value.doubleValue(); } // Can call Number methods
}

// Generic method
public class GenericMethods {
    // Generic method
    public static <T extends Comparable<T>> T max(T a, T b) {
        return a.compareTo(b) >= 0 ? a : b;
    }

    // Wildcard — Unbounded (?)
    public static void printList(List<?> list) { // Accepts List of any type
        for (Object o : list) System.out.println(o);
    }

    // Upper bounded wildcard — extends
    public static double sumList(List<? extends Number> list) { // Number or subclass
        return list.stream().mapToDouble(Number::doubleValue).sum();
    }

    // Lower bounded wildcard — super
    public static void addNumbers(List<? super Integer> list) { // Integer or superclass
        list.add(1); // Can safely add Integer
        list.add(2);
    }

    /*
     * PECS Principle — Producer Extends, Consumer Super
     * - Producer (reading from): use ? extends T
     * - Consumer (writing to):   use ? super T
     */
}

/*
 * Type Erasure — Generics at Runtime
 * Compile time pe type info use hoti hai, runtime pe erase ho jati hai
 * List<String> aur List<Integer> runtime pe sirf List hain
 *
 * Consequences:
 * - new T() nahi kar sakte (no generic array creation)
 * - instanceof T check nahi kar sakte
 * - static generic fields nahi ho sakte
 */
public class TypeErasureDemo {
    // ⚠️ These won't work:
    // public <T> T create() { return new T(); } // Error!
    // public <T> T[] createArray(int n) { return new T[n]; } // Error!

    // Workaround:
    public <T> T create(Class<T> clazz) throws Exception {
        return clazz.getDeclaredConstructor().newInstance();
    }
}
```

---

### 🎯 Interview Q&A — Section 9

**Q1: Generics kya hain aur kyun use karte hain?**
> Generics compile-time type safety provide karte hain. `List<String>` mein accidentally Integer add karne se compile error aata hai. Type casting ki zaroorat nahi. Code reusable banta hai.

**Q2: Type Erasure kya hai?**
> Java generics compile-time feature hai. Compiled bytecode mein type information erase ho jati hai. `List<String>` → `List` (raw type) bytecode mein. Runtime reflection se generic type info access limited hai.

**Q3: `List<Object>` aur `List<?>` difference?**
> - `List<Object>` — Sirf Object type list. `List<String>` ko assign nahi kar sakte.
> - `List<?>` — Kisi bhi type ki list accept karta hai (wildcard). But add nahi kar sakte null ke alawa.

---

## 10. Java 8+ Features

### Lambda Expressions

```java
import java.util.*;
import java.util.function.*;

public class LambdaDemo {
    @FunctionalInterface // Only one abstract method
    interface MathOperation {
        int operate(int a, int b);
        // Can have default and static methods too
        default void printDescription() {
            System.out.println("Math operation");
        }
    }

    public static void main(String[] args) {
        // Lambda = Anonymous function implementation of FunctionalInterface
        MathOperation add = (a, b) -> a + b;
        MathOperation multiply = (a, b) -> {
            int result = a * b;
            return result;
        };

        System.out.println(add.operate(5, 3));      // 8
        System.out.println(multiply.operate(5, 3)); // 15

        // Built-in Functional Interfaces (java.util.function)
        // Predicate<T> — takes T, returns boolean
        Predicate<String> isEmpty = String::isEmpty;
        Predicate<String> isNotEmpty = isEmpty.negate();
        Predicate<String> isLong = s -> s.length() > 5;
        Predicate<String> combined = isNotEmpty.and(isLong);

        // Function<T, R> — takes T, returns R
        Function<String, Integer> length = String::length;
        Function<String, String> upper = String::toUpperCase;
        Function<String, Integer> upperLength = upper.andThen(length); // Compose

        // BiFunction<T, U, R> — takes T and U, returns R
        BiFunction<String, Integer, String> repeat = (s, n) -> s.repeat(n);

        // Supplier<T> — takes nothing, returns T
        Supplier<List<String>> listFactory = ArrayList::new;
        List<String> newList = listFactory.get();

        // Consumer<T> — takes T, returns nothing
        Consumer<String> printer = System.out::println;
        Consumer<String> upperPrinter = printer.compose(String::toUpperCase);

        // UnaryOperator<T> — Function<T,T>
        UnaryOperator<String> trim = String::trim;

        // BinaryOperator<T> — BiFunction<T,T,T>
        BinaryOperator<Integer> max = Integer::max;

        // Method references
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        names.forEach(System.out::println);         // Instance method of arbitrary object
        names.sort(String::compareToIgnoreCase);    // Static method ref
        names.stream().map(String::new).count();    // Constructor ref
    }
}
```

### Streams API

```java
import java.util.stream.*;

public class StreamsDemo {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

        // Intermediate operations (lazy — don't execute until terminal)
        // Terminal operations (eager — trigger execution)

        // filter → map → collect
        List<Integer> evenSquares = numbers.stream()
            .filter(n -> n % 2 == 0)           // [2, 4, 6, 8, 10]
            .map(n -> n * n)                   // [4, 16, 36, 64, 100]
            .collect(Collectors.toList());

        // reduce — aggregate
        int sum = numbers.stream()
            .reduce(0, Integer::sum);          // 55
        Optional<Integer> max = numbers.stream()
            .reduce(Integer::max);             // Optional[10]

        // Collectors
        Map<Boolean, List<Integer>> partitioned = numbers.stream()
            .collect(Collectors.partitioningBy(n -> n % 2 == 0));
        // {false=[1,3,5,7,9], true=[2,4,6,8,10]}

        Map<Integer, List<Integer>> grouped = numbers.stream()
            .collect(Collectors.groupingBy(n -> n % 3));
        // {0=[3,6,9], 1=[1,4,7,10], 2=[2,5,8]}

        Map<Integer, Long> countByRemainder = numbers.stream()
            .collect(Collectors.groupingBy(n -> n % 3, Collectors.counting()));

        String joined = Stream.of("a", "b", "c")
            .collect(Collectors.joining(", ", "[", "]")); // "[a, b, c]"

        // flatMap — flatten nested collections
        List<List<Integer>> nested = Arrays.asList(
            Arrays.asList(1, 2, 3),
            Arrays.asList(4, 5, 6)
        );
        List<Integer> flat = nested.stream()
            .flatMap(Collection::stream)
            .collect(Collectors.toList()); // [1,2,3,4,5,6]

        // distinct, sorted, limit, skip
        List<Integer> processed = numbers.stream()
            .distinct()
            .sorted(Comparator.reverseOrder())
            .skip(2)
            .limit(5)
            .collect(Collectors.toList());

        // peek — debug intermediate steps (don't use in production for side effects)
        numbers.stream()
            .filter(n -> n > 5)
            .peek(n -> System.out.println("After filter: " + n))
            .map(n -> n * 2)
            .forEach(System.out::println);

        // Numeric streams — avoid boxing overhead
        IntStream.range(1, 6).sum();              // 15
        IntStream.rangeClosed(1, 5).sum();        // 15
        IntStream.of(1, 2, 3).average();          // OptionalDouble[2.0]

        // Statistics
        IntSummaryStatistics stats = numbers.stream()
            .mapToInt(Integer::intValue)
            .summaryStatistics();
        System.out.println("Min: " + stats.getMin() + ", Max: " + stats.getMax()
                + ", Avg: " + stats.getAverage() + ", Sum: " + stats.getSum());

        // Parallel streams — be careful!
        long count = numbers.parallelStream()
            .filter(n -> n > 5)
            .count();
        // ⚠️ Not always faster! Good for CPU-intensive tasks with large data.
        // ⚠️ Not suitable for ordered operations or when order matters.

        // Stream.generate and Stream.iterate
        Stream.generate(Math::random).limit(5).forEach(System.out::println);
        Stream.iterate(0, n -> n + 2).limit(5).forEach(System.out::println); // 0,2,4,6,8
        // Java 9+:
        Stream.iterate(0, n -> n < 10, n -> n + 2).forEach(System.out::println);
    }
}
```

### Optional

```java
public class OptionalDemo {
    public static void main(String[] args) {
        // Creating Optional
        Optional<String> empty = Optional.empty();
        Optional<String> present = Optional.of("Hello");       // Throws NPE if null!
        Optional<String> nullable = Optional.ofNullable(null); // Safe for null

        // Checking and getting
        System.out.println(present.isPresent()); // true
        System.out.println(empty.isEmpty());      // true (Java 11+)
        System.out.println(present.get());        // "Hello" — ⚠️ throws if empty

        // Safe alternatives to get()
        String val1 = empty.orElse("default");          // "default"
        String val2 = empty.orElseGet(() -> "computed"); // Lazy evaluation
        String val3 = present.orElseThrow();             // Throws if empty (Java 10+)
        String val4 = empty.orElseThrow(
            () -> new RuntimeException("Not found")
        );

        // Transformations
        Optional<Integer> length = present.map(String::length); // Optional[5]
        Optional<String> upper = present.filter(s -> s.length() > 3).map(String::toUpperCase);

        // flatMap — when mapping returns Optional
        Optional<Optional<String>> nested = present.map(s -> Optional.of(s.toUpperCase()));
        Optional<String> flat = present.flatMap(s -> Optional.of(s.toUpperCase())); // Better

        // ifPresent, ifPresentOrElse
        present.ifPresent(s -> System.out.println("Value: " + s));
        empty.ifPresentOrElse(                        // Java 9+
            s -> System.out.println("Found: " + s),
            () -> System.out.println("Not found")
        );

        // or() — Java 9+
        Optional<String> result = empty.or(() -> Optional.of("fallback"));

        // ⚠️ Don'ts with Optional:
        // 1. Optional as field type (use null instead)
        // 2. Optional as method parameter
        // 3. Optional.get() without isPresent() check
        // 4. Optional for primitive types (use OptionalInt, OptionalLong, OptionalDouble)
    }

    // ✅ Good use: Method return type to indicate possible absence
    public Optional<User> findUserById(int id) {
        // return Optional.empty() or Optional.of(user)
        return Optional.empty();
    }

    record User(int id, String name) {} // Java 16+ record
}
```

### Java 9-21 Modern Features

```java
// Java 9: Modules, private interface methods (shown above)

// Java 10: var — Local Variable Type Inference
public class VarDemo {
    public static void main(String[] args) {
        var list = new ArrayList<String>(); // Inferred as ArrayList<String>
        var map = new HashMap<String, Integer>(); // Inferred
        var str = "Hello"; // Inferred as String

        for (var entry : map.entrySet()) { // Works in for-each
            System.out.println(entry.getKey());
        }

        // ⚠️ var limitations:
        // var x;           // No initializer
        // var x = null;    // Can't infer from null
        // var[] arr;       // No var for arrays
        // var is not a keyword — can still be used as variable name (but don't!)
    }
}

// Java 14: Records — Immutable data classes
public record Point(int x, int y) {
    // Compact canonical constructor (validation)
    Point {
        if (x < 0 || y < 0) throw new IllegalArgumentException("Negative coordinates");
    }

    // Custom method
    public double distanceFromOrigin() {
        return Math.sqrt(x * x + y * y);
    }
    // Automatically generates: constructor, getters (x(), y()), equals(), hashCode(), toString()
}

// Java 17: Sealed Classes — restrict subclasses
public sealed class Shape permits Circle, Rectangle, Triangle {
    public abstract double area();
}

public final class Circle extends Shape {
    private final double radius;
    Circle(double radius) { this.radius = radius; }
    @Override
    public double area() { return Math.PI * radius * radius; }
}

public final class Rectangle extends Shape {
    private final double width, height;
    Rectangle(double w, double h) { this.width = w; this.height = h; }
    @Override
    public double area() { return width * height; }
}

// Java 21: Pattern Matching for switch
public class PatternMatchingDemo {
    public static String describe(Object obj) {
        return switch (obj) {
            case Integer i when i < 0 -> "negative int: " + i;
            case Integer i -> "positive int: " + i;
            case String s when s.isEmpty() -> "empty string";
            case String s -> "string: " + s;
            case null -> "null";
            default -> "other: " + obj;
        };
    }

    // Java 21: Virtual threads (Project Loom)
    public static void virtualThreadDemo() throws Exception {
        Thread vt = Thread.ofVirtual().start(() -> {
            System.out.println("Running on virtual thread: " + Thread.currentThread());
        });
        vt.join();

        // ExecutorService with virtual threads
        try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
            executor.submit(() -> System.out.println("Task 1"));
            executor.submit(() -> System.out.println("Task 2"));
        }
    }
}
```

---

### 🎯 Interview Q&A — Section 10

**Q1: Lambda vs Anonymous class difference?**
> - Lambda: Shorter, `this` refers to enclosing class
> - Anonymous class: Verbose, `this` refers to anonymous class itself
> - Lambda only for functional interfaces (1 abstract method)
> - Anonymous class for multiple methods, extending classes

**Q2: Stream aur Collection difference?**
> - **Collection:** Data store karta hai, multiple traversal possible, data modify kar sakte hain
> - **Stream:** Data process karta hai, single traversal, no modification, lazy evaluation, pipeline

**Q3: `map()` vs `flatMap()` in Streams?**
> - `map(f)` — Each element ko transform karta hai. `List<List<T>>` → `Stream<List<T>>`
> - `flatMap(f)` — Each element transform karke flatten karta hai. `List<List<T>>` → `Stream<T>`

**Q4: Parallel streams kab avoid karein?**
> - Small datasets (overhead > benefit)
> - Ordered operations where order matters
> - I/O operations
> - Shared mutable state (thread safety issues)
> - Side effects wali operations

**Q5: Optional ka purpose kya hai?**
> `NullPointerException` avoid karna aur clearly express karna ki method "no value" return kar sakta hai. Better than returning null. Chain of method calls safe karta hai.

**Q6: `var` keyword kya hai? Disadvantages?**
> Local variable type inference — compiler type infer karta hai. Disadvantages: code readability reduce ho sakti hai complex types ke saath, IDE support required, lambda return type mein use nahi kar sakte.

---

## 11. Multithreading & Concurrency

### Thread Creation

```java
public class ThreadCreation {

    // Way 1: Extend Thread
    static class MyThread extends Thread {
        @Override
        public void run() {
            System.out.println("Thread running: " + getName());
        }
    }

    // Way 2: Implement Runnable (preferred)
    static class MyRunnable implements Runnable {
        @Override
        public void run() {
            System.out.println("Runnable running: " + Thread.currentThread().getName());
        }
    }

    // Way 3: Callable (returns value, throws exception)
    static class MyCallable implements Callable<String> {
        @Override
        public String call() throws Exception {
            Thread.sleep(1000);
            return "Callable result from " + Thread.currentThread().getName();
        }
    }

    public static void main(String[] args) throws Exception {
        // Thread lifecycle: NEW → RUNNABLE → (BLOCKED/WAITING/TIMED_WAITING) → TERMINATED

        // Way 1
        MyThread t1 = new MyThread();
        t1.start(); // start() creates new thread, run() executes in it
        // t1.run(); // ⚠️ This runs in CURRENT thread, not new one!

        // Way 2
        Thread t2 = new Thread(new MyRunnable());
        t2.start();

        // Lambda (since Runnable is functional interface)
        Thread t3 = new Thread(() -> System.out.println("Lambda thread"));
        t3.setName("LambdaThread");
        t3.setDaemon(true); // Daemon thread — JVM exits when only daemon threads left
        t3.setPriority(Thread.MAX_PRIORITY); // 1-10, default 5
        t3.start();

        // Thread methods
        t1.join();      // Wait for t1 to complete
        t1.join(2000);  // Wait max 2 seconds

        Thread.sleep(100);  // Current thread sleep 100ms
        Thread.yield();     // Hint to scheduler to switch threads

        // ExecutorService — better than raw threads
        ExecutorService executor = Executors.newFixedThreadPool(4);
        executor.execute(() -> System.out.println("Task 1")); // Runnable

        Future<String> future = executor.submit(new MyCallable());
        System.out.println("Doing other work...");
        String result = future.get(); // Blocks until done
        System.out.println(result);

        executor.shutdown(); // Graceful shutdown (finish pending tasks)
        executor.awaitTermination(5, TimeUnit.SECONDS);
        // executor.shutdownNow(); // Interrupt all tasks
    }
}
```

### Synchronization & Locks

```java
import java.util.concurrent.locks.*;
import java.util.concurrent.atomic.*;

public class SynchronizationDemo {

    // Shared mutable state — Problem!
    private int counter = 0;

    // Solution 1: synchronized method
    public synchronized void increment() {
        counter++;
    }

    // Solution 2: synchronized block (more granular)
    private final Object lock = new Object();
    public void incrementBlock() {
        synchronized (lock) {
            counter++; // Only this critical section is synchronized
        }
    }

    // Solution 3: ReentrantLock (more features than synchronized)
    private final ReentrantLock reentrantLock = new ReentrantLock();
    public void incrementWithLock() {
        reentrantLock.lock();
        try {
            counter++;
        } finally {
            reentrantLock.unlock(); // Always unlock in finally!
        }
    }

    // Solution 4: AtomicInteger (lock-free, best for simple counters)
    private final AtomicInteger atomicCounter = new AtomicInteger(0);
    public void atomicIncrement() {
        atomicCounter.incrementAndGet(); // Atomic, thread-safe, no locking overhead
    }

    // Solution 5: volatile (visibility, not atomicity)
    private volatile boolean running = true; // Ensures write visible to all threads
    public void stopThread() { running = false; }
    // ⚠️ volatile does NOT make compound operations atomic (i++ is not atomic!)

    // ReadWriteLock — multiple readers, one writer
    private final ReadWriteLock rwLock = new ReentrantReadWriteLock();
    private String sharedData = "";

    public String read() {
        rwLock.readLock().lock();
        try {
            return sharedData; // Multiple threads can read simultaneously
        } finally {
            rwLock.readLock().unlock();
        }
    }

    public void write(String data) {
        rwLock.writeLock().lock();
        try {
            sharedData = data; // Exclusive access for writing
        } finally {
            rwLock.writeLock().unlock();
        }
    }
}
```

### Producer-Consumer Pattern

```java
import java.util.concurrent.*;

public class ProducerConsumer {

    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<Integer> queue = new ArrayBlockingQueue<>(10); // Capacity 10

        // Producer
        Thread producer = new Thread(() -> {
            try {
                for (int i = 0; i < 20; i++) {
                    queue.put(i); // Blocks if queue is full
                    System.out.println("Produced: " + i);
                    Thread.sleep(50);
                }
                queue.put(-1); // Poison pill — signal end
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        // Consumer
        Thread consumer = new Thread(() -> {
            try {
                while (true) {
                    int item = queue.take(); // Blocks if queue is empty
                    if (item == -1) break; // Poison pill
                    System.out.println("Consumed: " + item);
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        });

        producer.start();
        consumer.start();
        producer.join();
        consumer.join();
    }
}
```

### CompletableFuture (Java 8+)

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureDemo {
    public static void main(String[] args) throws Exception {
        // Async computation
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            // Runs in ForkJoinPool.commonPool()
            simulateNetworkCall();
            return "Data from network";
        });

        // Chain transformations
        CompletableFuture<Integer> processed = future
            .thenApply(data -> data.length())              // Transform result
            .thenApply(len -> len * 2);                    // Chain

        // Side effects (no return)
        future.thenAccept(data -> System.out.println("Got: " + data));

        // Run after completion
        future.thenRun(() -> System.out.println("Completed!"));

        // Combine two futures
        CompletableFuture<String> f1 = CompletableFuture.supplyAsync(() -> "Hello");
        CompletableFuture<String> f2 = CompletableFuture.supplyAsync(() -> " World");
        CompletableFuture<String> combined = f1.thenCombine(f2, (a, b) -> a + b);
        System.out.println(combined.get()); // "Hello World"

        // Run multiple in parallel, wait for ALL
        CompletableFuture<Void> all = CompletableFuture.allOf(f1, f2);
        all.join(); // Wait for both

        // Wait for ANY one to complete
        CompletableFuture<Object> any = CompletableFuture.anyOf(f1, f2);
        System.out.println(any.get());

        // Exception handling
        CompletableFuture<String> safe = future
            .exceptionally(ex -> "Default value on error: " + ex.getMessage())
            .handle((result, ex) -> ex != null ? "Error: " + ex.getMessage() : result);

        System.out.println(processed.get(2, TimeUnit.SECONDS));
    }

    private static void simulateNetworkCall() {
        try { Thread.sleep(500); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
    }
}
```

### Thread-Safe Data Structures

```java
import java.util.concurrent.*;

public class ConcurrentCollections {
    public static void main(String[] args) {
        // ConcurrentHashMap — thread-safe HashMap (segment locking)
        ConcurrentHashMap<String, Integer> chm = new ConcurrentHashMap<>();
        chm.put("key", 1);
        chm.computeIfAbsent("key2", k -> 2);
        // Supports atomic compound operations

        // CopyOnWriteArrayList — reads fast, writes create copy
        // Good when reads >> writes
        CopyOnWriteArrayList<String> cowList = new CopyOnWriteArrayList<>();
        cowList.add("item");
        // Iteration always safe, no ConcurrentModificationException

        // BlockingQueue implementations
        BlockingQueue<String> abq = new ArrayBlockingQueue<>(100); // Bounded
        BlockingQueue<String> lbq = new LinkedBlockingQueue<>();    // Unbounded (careful!)
        BlockingQueue<String> pq = new PriorityBlockingQueue<>();   // Priority-based

        // CountDownLatch — wait for N events
        CountDownLatch latch = new CountDownLatch(3);
        for (int i = 0; i < 3; i++) {
            new Thread(() -> {
                doWork();
                latch.countDown(); // Decrement count
            }).start();
        }
        try { latch.await(); } catch (InterruptedException e) {} // Wait until count = 0
        System.out.println("All 3 tasks completed!");

        // CyclicBarrier — N threads wait for each other at barrier
        CyclicBarrier barrier = new CyclicBarrier(3, () -> System.out.println("All arrived!"));

        // Semaphore — control access to limited resources
        Semaphore semaphore = new Semaphore(3); // 3 permits (like 3 db connections)
        try {
            semaphore.acquire(); // Take a permit (blocks if 0)
            doWork();
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        } finally {
            semaphore.release(); // Return permit
        }
    }

    private static void doWork() {
        try { Thread.sleep(100); } catch (InterruptedException e) { Thread.currentThread().interrupt(); }
    }
}
```

---

### 🎯 Interview Q&A — Section 11

**Q1: Thread kaise create karte hain? Best practice kya hai?**
> `Runnable` implement karna prefer karo (vs `Thread` extend). Aur better hai `ExecutorService` use karna raw threads ke baje — resource management, thread pooling automatic.

**Q2: `synchronized` vs `ReentrantLock`?**
> - `synchronized` — simpler, automatic unlock, no try-lock, no interrupt
> - `ReentrantLock` — tryLock(), lockInterruptibly(), fairness support, multiple conditions. When in doubt, `synchronized` use karo.

**Q3: `volatile` kab use karein?**
> Jab ek thread variable write kare aur doosra sirf read kare. Visibility guarantee deta hai. Compound operations (i++) ke liye atomic nahi — use `AtomicInteger`.

**Q4: Deadlock kya hai? Kaise avoid karein?**
> Jab 2 threads ek doosre ke lock ka wait karein — koi aage nahi badhta.
> Avoidance: Lock ordering (always same order), try-lock with timeout, lock hierarchy, avoid nested locks.

**Q5: `ThreadLocal` kya hai?**
> Har thread ka apna variable copy. Thread-safe without synchronization. Use case: database connections per thread, user session info in web requests.

**Q6: `wait()` vs `sleep()`?**
> - `wait()` — Object pe call, lock release karta hai, notify() se wakeup
> - `sleep()` — Thread pe call, lock hold karta hai, time ke baad wakeup automatically

**Q7: ExecutorService types?**
> - `newFixedThreadPool(n)` — n threads fixed pool
> - `newCachedThreadPool()` — unlimited threads, idle ones terminate
> - `newSingleThreadExecutor()` — single thread, tasks sequentially
> - `newScheduledThreadPool(n)` — scheduled/periodic tasks
> - `newVirtualThreadPerTaskExecutor()` — Java 21, virtual threads

---

## 12. Java Memory Model & Garbage Collection

### Memory Areas

```java
/*
 * JVM Memory Layout:
 *
 * ┌─────────────────────────────────────────────────────────┐
 * │  Heap (Shared across threads)                           │
 * │  ┌────────────────────────────┬──────────────────────┐  │
 * │  │       Young Generation     │   Old Generation     │  │
 * │  │  ┌──────┬──────┬────────┐  │   (Tenured)          │  │
 * │  │  │ Eden │  S0  │  S1    │  │                      │  │
 * │  │  └──────┴──────┴────────┘  │   Long-lived objects │  │
 * │  │  New objects created here  │                      │  │
 * │  └────────────────────────────┴──────────────────────┘  │
 * │  ┌──────────────────────────────────────────────────┐   │
 * │  │  Metaspace (Java 8+, was PermGen before)         │   │
 * │  │  Class metadata, static variables, method code   │   │
 * │  └──────────────────────────────────────────────────┘   │
 * └─────────────────────────────────────────────────────────┘
 *
 * Per-Thread Memory:
 * ┌───────────┐  ┌────────────┐  ┌─────────────────────┐
 * │  Stack    │  │ PC Register│  │ Native Method Stack │
 * │ Frames    │  │            │  │                     │
 * └───────────┘  └────────────┘  └─────────────────────┘
 */

// GC Tuning JVM flags (reference):
// -Xms512m           → Initial heap size
// -Xmx2g             → Max heap size
// -Xss256k           → Stack size per thread
// -XX:+UseG1GC       → Use G1 garbage collector
// -XX:MaxMetaspaceSize=256m
// -XX:+PrintGCDetails
```

### Garbage Collection

```java
/*
 * GC Process:
 *
 * Minor GC (Young Gen):
 * 1. New objects → Eden
 * 2. Eden full → Minor GC
 * 3. Live objects → S0 (survivor space)
 * 4. Next GC: S0 → S1, new Eden → S0
 * 5. After N cycles (default 15) → Old Gen
 *
 * Major GC / Full GC (Old Gen):
 * 1. Old Gen full → Full GC
 * 2. All live objects marked, unreachable collected
 * 3. Stop-the-world event (application pauses!)
 *
 * GC Algorithms:
 * - Serial GC: Single-threaded, small apps
 * - Parallel GC: Multi-threaded, throughput focused (default Java 8)
 * - CMS (Concurrent Mark Sweep): Low pause time (deprecated)
 * - G1 GC: Default Java 9+, predictable pause times, large heaps
 * - ZGC (Java 15+): Ultra-low latency (<10ms pauses)
 * - Shenandoah: Similar to ZGC
 */

public class GCDemo {
    // Object becomes eligible for GC when no references point to it

    public static void main(String[] args) {
        Object obj = new Object(); // obj holds reference
        obj = null;                // obj eligible for GC

        // ⚠️ System.gc() is just a HINT — JVM may ignore it!
        System.gc(); // Don't rely on this in production!

        // Finalize — deprecated, don't use!
        // Use java.lang.ref for weak/soft/phantom references

        // Reference types:
        // Strong: obj = new Object() — never GC'd while referenced
        // Soft: SoftReference<> — GC'd when memory low (good for caches)
        // Weak: WeakReference<> — GC'd at next GC cycle
        // Phantom: PhantomReference<> — after finalization, before reclaim

        java.lang.ref.WeakReference<byte[]> weakRef =
            new java.lang.ref.WeakReference<>(new byte[1024]);
        System.out.println(weakRef.get()); // May return null after GC
    }
}
```

### Memory Leaks

```java
/*
 * Common Memory Leak Patterns in Java:
 *
 * 1. Static Collections holding objects
 */
public class MemoryLeakExamples {
    private static final List<Object> STATIC_LIST = new ArrayList<>(); // Never GC'd!

    public void badMethod() {
        STATIC_LIST.add(new byte[1024 * 1024]); // 1MB added every call!
    }

    // 2. Unclosed resources
    public void resourceLeak() throws Exception {
        // ❌ Bad
        Connection conn = getConnection();
        // If exception occurs, conn never closed!

        // ✅ Good — try-with-resources
        try (Connection c = getConnection()) {
            // auto-closed
        }
    }

    // 3. ThreadLocal not removed
    private static ThreadLocal<Object> threadLocal = new ThreadLocal<>();
    public void threadLocalLeak() {
        threadLocal.set(new BigObject());
        // If thread reused (thread pool), BigObject stays!
        // Always call threadLocal.remove() when done
    }

    // 4. Inner class holding outer class reference
    class BadInnerClass { // holds reference to OuterClass.this
        void process() { /* uses outer class */ }
    }
    // Use static inner class if outer reference not needed

    private Connection getConnection() { return null; }
    static class BigObject {}
    interface Connection extends AutoCloseable {}
}
```

---

### 🎯 Interview Q&A — Section 12

**Q1: Java mein memory leak possible hai?**
> Haan! GC hone ke bawajood leaks possible hain — jab objects accidentally reference mein rahein. Static collections, unclosed resources, ThreadLocal without remove, event listeners not deregistered.

**Q2: OutOfMemoryError kab aata hai? Types?**
> - `Java heap space` — Heap full, new objects create nahi ho sakte
> - `GC overhead limit exceeded` — 98% time GC, 2% work (infinite GC loop)
> - `Metaspace` — Class metadata space full (too many classes/classloaders)
> - `Unable to create native thread` — OS thread limit exceeded

**Q3: G1 GC kya hai aur kyun better hai?**
> G1 (Garbage-First) heap ko equal-size regions mein divide karta hai. Predictable pause times provide karta hai. Large heaps (6GB+) ke liye designed. Concurrent marking, parallel compaction. Java 9+ default.

**Q4: `finalize()` use karna chahiye?**
> Nahi! Deprecated Java 9 mein. Unpredictable execution time, performance overhead. Use `try-with-resources` ya `Cleaner` API (Java 9+) instead.

---

## 13. Design Patterns

### Creational Patterns

```java
// 1. Singleton Pattern — One instance only
public class Singleton {
    // Bill Pugh Singleton — thread-safe, lazy initialization
    private Singleton() {}

    private static class Holder {
        private static final Singleton INSTANCE = new Singleton();
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}

// Thread-safe with double-checked locking
public class SingletonDCL {
    private volatile static SingletonDCL instance; // volatile required!
    private SingletonDCL() {}

    public static SingletonDCL getInstance() {
        if (instance == null) {                      // First check (no sync)
            synchronized (SingletonDCL.class) {
                if (instance == null) {              // Second check (with sync)
                    instance = new SingletonDCL();
                }
            }
        }
        return instance;
    }
}

// Enum Singleton — Best approach (Effective Java recommended)
public enum EnumSingleton {
    INSTANCE;
    public void doSomething() { /* ... */ }
}

// 2. Factory Pattern
public interface Animal { String speak(); }
public class Dog implements Animal { public String speak() { return "Woof"; } }
public class Cat implements Animal { public String speak() { return "Meow"; } }

public class AnimalFactory {
    public static Animal create(String type) {
        return switch (type.toLowerCase()) {
            case "dog" -> new Dog();
            case "cat" -> new Cat();
            default -> throw new IllegalArgumentException("Unknown: " + type);
        };
    }
}

// 3. Builder Pattern — Complex object construction
public class Pizza {
    private final String size;
    private final String crust;
    private final List<String> toppings;

    private Pizza(Builder builder) {
        this.size = builder.size;
        this.crust = builder.crust;
        this.toppings = List.copyOf(builder.toppings);
    }

    public static class Builder {
        private String size = "MEDIUM"; // Default
        private String crust = "THIN";
        private List<String> toppings = new ArrayList<>();

        public Builder size(String size) { this.size = size; return this; }
        public Builder crust(String crust) { this.crust = crust; return this; }
        public Builder topping(String topping) { this.toppings.add(topping); return this; }
        public Pizza build() { return new Pizza(this); }
    }

    public static void main(String[] args) {
        Pizza pizza = new Pizza.Builder()
            .size("LARGE")
            .crust("THICK")
            .topping("Cheese")
            .topping("Mushroom")
            .build();
    }
}
```

### Structural Patterns

```java
// Decorator Pattern — Add functionality without changing class
public interface Coffee {
    String getDescription();
    double getCost();
}

public class SimpleCoffee implements Coffee {
    public String getDescription() { return "Coffee"; }
    public double getCost() { return 1.0; }
}

public abstract class CoffeeDecorator implements Coffee {
    protected Coffee decoratedCoffee;
    public CoffeeDecorator(Coffee c) { this.decoratedCoffee = c; }
    public String getDescription() { return decoratedCoffee.getDescription(); }
    public double getCost() { return decoratedCoffee.getCost(); }
}

public class MilkDecorator extends CoffeeDecorator {
    public MilkDecorator(Coffee c) { super(c); }
    public String getDescription() { return super.getDescription() + ", Milk"; }
    public double getCost() { return super.getCost() + 0.5; }
}

// Usage:
// Coffee c = new MilkDecorator(new SugarDecorator(new SimpleCoffee()));

// Proxy Pattern — Control access
public interface Database {
    void query(String sql);
}

public class RealDatabase implements Database {
    public void query(String sql) { System.out.println("Executing: " + sql); }
}

public class DatabaseProxy implements Database {
    private RealDatabase db;
    private boolean hasAccess;

    public DatabaseProxy(boolean hasAccess) { this.hasAccess = hasAccess; }

    public void query(String sql) {
        if (!hasAccess) throw new SecurityException("Access denied!");
        if (db == null) db = new RealDatabase(); // Lazy initialization
        System.out.println("Logging query: " + sql);
        db.query(sql);
    }
}
```

### Behavioral Patterns

```java
// Observer Pattern (Event-based)
public interface Observer {
    void update(String event);
}

public class EventManager {
    private List<Observer> observers = new ArrayList<>();

    public void subscribe(Observer o) { observers.add(o); }
    public void unsubscribe(Observer o) { observers.remove(o); }
    public void notifyAll(String event) {
        observers.forEach(o -> o.update(event));
    }
}

// Strategy Pattern
public interface SortStrategy {
    void sort(int[] arr);
}

public class BubbleSortStrategy implements SortStrategy {
    public void sort(int[] arr) { /* bubble sort */ }
}

public class QuickSortStrategy implements SortStrategy {
    public void sort(int[] arr) { /* quick sort */ }
}

public class Sorter {
    private SortStrategy strategy;
    public Sorter(SortStrategy strategy) { this.strategy = strategy; }
    public void setStrategy(SortStrategy strategy) { this.strategy = strategy; }
    public void sort(int[] arr) { strategy.sort(arr); }
}

// Template Method Pattern
public abstract class DataProcessor {
    // Template method — defines algorithm skeleton
    public final void process() {
        readData();    // Step 1
        processData(); // Step 2 — subclass implements
        writeData();   // Step 3
    }

    private void readData() { System.out.println("Reading data"); }
    protected abstract void processData(); // Must implement
    private void writeData() { System.out.println("Writing data"); }
}
```

---

### 🎯 Interview Q&A — Section 13

**Q1: Singleton pattern ke problems kya hain?**
> Global state, difficult to test (mock nahi kar sakte easily), multithreading issues (if not implemented correctly), reflection se break ho sakta hai.

**Q2: When to use Factory vs Builder?**
> - **Factory** — Object creation logic hide karna, type decide at runtime
> - **Builder** — Complex objects with many optional parameters construct karna

**Q3: Difference between Decorator and Proxy?**
> - **Decorator** — Add behavior/functionality. Client knows it's decorating.
> - **Proxy** — Control access. Often transparent to client. Used for lazy loading, security, logging.

---

## 14. Java I/O & NIO

```java
import java.io.*;
import java.nio.*;
import java.nio.file.*;
import java.nio.channels.*;

public class IODemo {

    public static void main(String[] args) throws IOException {
        // Files utility (Java 7+ NIO.2) — Modern way
        Path path = Paths.get("example.txt");

        // Write
        Files.writeString(path, "Hello\nWorld\n"); // Java 11+
        Files.write(path, List.of("Line1", "Line2"), StandardCharsets.UTF_8);

        // Read
        String content = Files.readString(path);  // Java 11+
        List<String> lines = Files.readAllLines(path);
        byte[] bytes = Files.readAllBytes(path);

        // Stream lines lazily (large files)
        try (Stream<String> lineStream = Files.lines(path)) {
            lineStream.filter(l -> l.startsWith("H")).forEach(System.out::println);
        }

        // Path operations
        Path dir = Paths.get("mydir");
        Files.createDirectories(dir);               // Create directory + parents
        Files.copy(path, dir.resolve("copy.txt"));  // Copy file
        Files.move(path, dir.resolve("moved.txt")); // Move/rename
        Files.delete(path);                          // Delete (throws if not exists)
        Files.deleteIfExists(path);                  // Delete if exists

        // Walk directory tree
        try (Stream<Path> walk = Files.walk(Paths.get("."))) {
            walk.filter(Files::isRegularFile)
                .filter(p -> p.toString().endsWith(".java"))
                .forEach(System.out::println);
        }

        // File attributes
        BasicFileAttributes attrs = Files.readAttributes(path, BasicFileAttributes.class);
        System.out.println(attrs.size());
        System.out.println(attrs.lastModifiedTime());
        System.out.println(attrs.isDirectory());
    }

    // Traditional BufferedReader/Writer (still useful for streams)
    public static void traditionalIO() throws IOException {
        // Reading with BufferedReader
        try (BufferedReader reader = new BufferedReader(
                new InputStreamReader(new FileInputStream("input.txt"), StandardCharsets.UTF_8))) {
            String line;
            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }
        }

        // Writing with BufferedWriter
        try (BufferedWriter writer = new BufferedWriter(
                new OutputStreamWriter(new FileOutputStream("output.txt"), StandardCharsets.UTF_8))) {
            writer.write("Hello");
            writer.newLine();
            writer.write("World");
        }

        // Serialization
        // Object must implement Serializable
    }

    // Serialization
    static class Person implements Serializable {
        private static final long serialVersionUID = 1L; // Always define!
        private String name;
        private int age;
        private transient String password; // transient = not serialized

        Person(String name, int age, String password) {
            this.name = name; this.age = age; this.password = password;
        }
    }

    public static void serializeDemo() throws Exception {
        Person p = new Person("Alice", 25, "secret");

        // Serialize (object → bytes)
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream("person.ser"))) {
            oos.writeObject(p);
        }

        // Deserialize (bytes → object)
        try (ObjectInputStream ois = new ObjectInputStream(
                new FileInputStream("person.ser"))) {
            Person loaded = (Person) ois.readObject();
            System.out.println(loaded.name); // "Alice"
            System.out.println(loaded.password); // null (transient)
        }
    }
}
```

---

### 🎯 Interview Q&A — Section 14

**Q1: `transient` keyword kya hai?**
> Serialization ke waqt `transient` fields serialize nahi hote. Sensitive data (password), cache, derived fields ke liye use karo.

**Q2: `serialVersionUID` kyun zaroori hai?**
> Class version control ke liye. Agar class modify ki aur serialVersionUID match nahi kiya, to `InvalidClassException` aata hai deserialization mein. Always explicitly define karo.

**Q3: NIO vs IO difference?**
> - **IO** — Blocking, stream-based, one thread per connection
> - **NIO** — Non-blocking, buffer-based, channels+selectors, one thread handles multiple connections. Netty, etc. use NIO.

---

## 15. JDBC & Database Connectivity

```java
import java.sql.*;
import javax.sql.DataSource;
import com.zaxxer.hikari.HikariConfig;
import com.zaxxer.hikari.HikariDataSource;

public class JDBCDemo {

    // ❌ Old way — load driver manually (not needed Java 6+)
    // Class.forName("com.mysql.cj.jdbc.Driver");

    // ✅ JDBC URL format:
    // MySQL:      jdbc:mysql://localhost:3306/dbname
    // PostgreSQL: jdbc:postgresql://localhost:5432/dbname
    // H2 Memory:  jdbc:h2:mem:testdb

    public static void basicJDBC() throws SQLException {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String user = "root";
        String pass = "password";

        // ⚠️ Always use try-with-resources!
        try (Connection conn = DriverManager.getConnection(url, user, pass)) {
            // Statement — for no parameters
            try (Statement stmt = conn.createStatement()) {
                ResultSet rs = stmt.executeQuery("SELECT * FROM users");
                while (rs.next()) {
                    System.out.println(rs.getInt("id") + " " + rs.getString("name"));
                }
            }

            // PreparedStatement — for parameters (prevents SQL injection!)
            String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
            try (PreparedStatement ps = conn.prepareStatement(sql,
                    Statement.RETURN_GENERATED_KEYS)) {
                ps.setString(1, "Alice");
                ps.setString(2, "alice@example.com");
                int rowsAffected = ps.executeUpdate();

                // Get generated keys
                try (ResultSet keys = ps.getGeneratedKeys()) {
                    if (keys.next()) {
                        System.out.println("Generated ID: " + keys.getInt(1));
                    }
                }
            }

            // Batch operations — multiple inserts efficiently
            String batchSql = "INSERT INTO users (name, email) VALUES (?, ?)";
            try (PreparedStatement ps = conn.prepareStatement(batchSql)) {
                conn.setAutoCommit(false); // Manual transaction
                try {
                    for (int i = 0; i < 100; i++) {
                        ps.setString(1, "User" + i);
                        ps.setString(2, "user" + i + "@test.com");
                        ps.addBatch();
                        if (i % 20 == 0) ps.executeBatch(); // Execute every 20
                    }
                    ps.executeBatch();
                    conn.commit(); // Commit transaction
                } catch (SQLException e) {
                    conn.rollback(); // Rollback on error!
                    throw e;
                } finally {
                    conn.setAutoCommit(true);
                }
            }

            // Transactions
            conn.setAutoCommit(false);
            try {
                // Transfer money between accounts
                updateBalance(conn, 1, -100);
                updateBalance(conn, 2, +100);
                conn.commit();
            } catch (Exception e) {
                conn.rollback();
                throw e;
            }
        }
    }

    // Connection Pool with HikariCP (Production standard)
    public static DataSource createDataSource() {
        HikariConfig config = new HikariConfig();
        config.setJdbcUrl("jdbc:mysql://localhost:3306/mydb");
        config.setUsername("root");
        config.setPassword("password");
        config.setMaximumPoolSize(20);          // Max connections in pool
        config.setMinimumIdle(5);               // Min idle connections
        config.setConnectionTimeout(30000);     // 30 sec timeout
        config.setIdleTimeout(600000);          // 10 min idle before remove
        config.setMaxLifetime(1800000);         // 30 min max connection life
        return new HikariDataSource(config);
    }

    private static void updateBalance(Connection conn, int accountId, int amount) throws SQLException {
        String sql = "UPDATE accounts SET balance = balance + ? WHERE id = ?";
        try (PreparedStatement ps = conn.prepareStatement(sql)) {
            ps.setInt(1, amount);
            ps.setInt(2, accountId);
            ps.executeUpdate();
        }
    }
}
```

---

### 🎯 Interview Q&A — Section 15

**Q1: Statement vs PreparedStatement?**
> - `Statement` — Dynamic SQL, no parameters, SQL injection risk, no precompilation
> - `PreparedStatement` — Parameterized queries, SQL injection safe, precompiled (faster for repeated execution), type-safe parameter setting

**Q2: Connection pool kyun use karte hain?**
> Database connection create karna expensive operation hai (network, auth, resources). Pool mein pre-created connections ready rehte hain — reuse hote hain. HikariCP production standard hai.

**Q3: ACID kya hai?**
> - **Atomicity** — Transaction ya poora succeed ya poora fail
> - **Consistency** — Database valid state mein rahe
> - **Isolation** — Concurrent transactions interfere nahi karein
> - **Durability** — Committed changes persist karain

---

## 16. Java Modules & New Features (9-21)

```java
// Module System (Java 9) — module-info.java
/*
module com.myapp.core {
    requires java.sql;          // Depends on java.sql module
    requires com.google.guava;  // External dependency
    exports com.myapp.api;      // Public API
    exports com.myapp.util to com.myapp.web; // Qualified export
    opens com.myapp.model to com.fasterxml.jackson.databind; // Reflection access
}
*/

// Java 10: var (covered earlier)

// Java 11: String methods, Files.readString/writeString

// Java 12: Switch expressions (preview)

// Java 13: Text blocks (preview)

// Java 14: Records (preview), Pattern matching instanceof (preview)

// Java 15: Sealed classes (preview), Text blocks stable

// Java 16: Records stable, Pattern matching instanceof stable

// Java 17: Sealed classes stable, Pattern matching switch (preview)
// Strong encapsulation of JDK internals

// Java 21: Virtual threads (stable), Sequenced Collections, Pattern matching switch stable

// Sequenced Collections (Java 21)
public class SequencedDemo {
    public static void main(String[] args) {
        // New interface SequencedCollection, SequencedSet, SequencedMap
        List<String> list = new ArrayList<>(List.of("a", "b", "c"));
        System.out.println(list.getFirst()); // "a" (Java 21+)
        System.out.println(list.getLast());  // "c"
        list.addFirst("z");                  // ["z","a","b","c"]
        list.removeLast();                   // ["z","a","b"]

        SequencedMap<String, Integer> map = new LinkedHashMap<>();
        map.put("a", 1); map.put("b", 2);
        System.out.println(map.firstEntry()); // a=1
        System.out.println(map.lastEntry());  // b=2
    }
}
```

---

## 17. Common Interview Coding Problems

### String Problems

```java
public class StringProblems {

    // 1. Check if string is palindrome
    public static boolean isPalindrome(String s) {
        s = s.toLowerCase().replaceAll("[^a-z0-9]", "");
        int left = 0, right = s.length() - 1;
        while (left < right) {
            if (s.charAt(left++) != s.charAt(right--)) return false;
        }
        return true;
    }

    // 2. Check anagram
    public static boolean isAnagram(String s1, String s2) {
        if (s1.length() != s2.length()) return false;
        int[] count = new int[26];
        for (char c : s1.toLowerCase().toCharArray()) count[c - 'a']++;
        for (char c : s2.toLowerCase().toCharArray()) count[c - 'a']--;
        for (int c : count) if (c != 0) return false;
        return true;
    }

    // 3. Find all permutations
    public static List<String> permutations(String s) {
        List<String> result = new ArrayList<>();
        permHelper("", s, result);
        return result;
    }

    private static void permHelper(String prefix, String remaining, List<String> result) {
        if (remaining.isEmpty()) { result.add(prefix); return; }
        for (int i = 0; i < remaining.length(); i++) {
            permHelper(prefix + remaining.charAt(i),
                    remaining.substring(0, i) + remaining.substring(i + 1), result);
        }
    }

    // 4. Longest common substring
    public static int longestCommonSubstring(String s1, String s2) {
        int[][] dp = new int[s1.length() + 1][s2.length() + 1];
        int max = 0;
        for (int i = 1; i <= s1.length(); i++) {
            for (int j = 1; j <= s2.length(); j++) {
                if (s1.charAt(i-1) == s2.charAt(j-1)) {
                    dp[i][j] = dp[i-1][j-1] + 1;
                    max = Math.max(max, dp[i][j]);
                }
            }
        }
        return max;
    }

    // 5. First non-repeating character
    public static char firstNonRepeating(String s) {
        Map<Character, Integer> freq = new LinkedHashMap<>(); // Maintains insertion order
        for (char c : s.toCharArray()) freq.merge(c, 1, Integer::sum);
        for (Map.Entry<Character, Integer> e : freq.entrySet()) {
            if (e.getValue() == 1) return e.getKey();
        }
        return '\0';
    }
}
```

### Array/Collection Problems

```java
public class ArrayProblems {

    // 1. Two Sum
    public static int[] twoSum(int[] nums, int target) {
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (map.containsKey(complement)) {
                return new int[]{map.get(complement), i};
            }
            map.put(nums[i], i);
        }
        return new int[]{};
    }

    // 2. Find duplicate in array
    public static int findDuplicate(int[] nums) {
        // Floyd's cycle detection
        int slow = nums[0], fast = nums[0];
        do {
            slow = nums[slow];
            fast = nums[nums[fast]];
        } while (slow != fast);
        slow = nums[0];
        while (slow != fast) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }

    // 3. Rotate array by k positions
    public static void rotate(int[] nums, int k) {
        k %= nums.length;
        reverse(nums, 0, nums.length - 1);
        reverse(nums, 0, k - 1);
        reverse(nums, k, nums.length - 1);
    }

    private static void reverse(int[] nums, int start, int end) {
        while (start < end) {
            int temp = nums[start]; nums[start] = nums[end]; nums[end] = temp;
            start++; end--;
        }
    }

    // 4. Top K frequent elements
    public static int[] topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> freq = new HashMap<>();
        for (int n : nums) freq.merge(n, 1, Integer::sum);

        PriorityQueue<Integer> pq = new PriorityQueue<>(
            (a, b) -> freq.get(a) - freq.get(b)); // Min heap by frequency

        for (int key : freq.keySet()) {
            pq.offer(key);
            if (pq.size() > k) pq.poll(); // Remove least frequent
        }

        return pq.stream().mapToInt(Integer::intValue).toArray();
    }

    // 5. Fibonacci with memoization
    private static Map<Integer, Long> memo = new HashMap<>();
    public static long fibonacci(int n) {
        if (n <= 1) return n;
        if (memo.containsKey(n)) return memo.get(n);
        long result = fibonacci(n - 1) + fibonacci(n - 2);
        memo.put(n, result);
        return result;
    }

    // 6. Binary Search
    public static int binarySearch(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2; // Prevent overflow!
            if (nums[mid] == target) return mid;
            else if (nums[mid] < target) left = mid + 1;
            else right = mid - 1;
        }
        return -1;
    }
}
```

### OOP Problems

```java
// LRU Cache Implementation
import java.util.*;

public class LRUCache {
    private final int capacity;
    private final LinkedHashMap<Integer, Integer> cache;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.cache = new LinkedHashMap<>(capacity, 0.75f, true) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<Integer, Integer> eldest) {
                return size() > capacity;
            }
        };
    }

    public int get(int key) {
        return cache.getOrDefault(key, -1);
    }

    public void put(int key, int value) {
        cache.put(key, value);
    }
}

// Design Stack with getMin() in O(1)
public class MinStack {
    private final Deque<Integer> stack = new ArrayDeque<>();
    private final Deque<Integer> minStack = new ArrayDeque<>();

    public void push(int val) {
        stack.push(val);
        minStack.push(minStack.isEmpty() ? val : Math.min(val, minStack.peek()));
    }

    public void pop() {
        stack.pop();
        minStack.pop();
    }

    public int top() { return stack.peek(); }
    public int getMin() { return minStack.peek(); }
}
```

### Java 8 Stream Problems

```java
public class StreamProblems {

    record Employee(String name, String dept, double salary) {}

    static List<Employee> employees = List.of(
        new Employee("Alice", "IT", 90000),
        new Employee("Bob", "IT", 85000),
        new Employee("Charlie", "HR", 70000),
        new Employee("Dave", "HR", 75000),
        new Employee("Eve", "IT", 95000)
    );

    public static void main(String[] args) {
        // 1. Highest paid employee per department
        Map<String, Optional<Employee>> highestPaid = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::dept,
                Collectors.maxBy(Comparator.comparingDouble(Employee::salary))
            ));

        // 2. Average salary per department
        Map<String, Double> avgSalary = employees.stream()
            .collect(Collectors.groupingBy(
                Employee::dept,
                Collectors.averagingDouble(Employee::salary)
            ));

        // 3. Count employees per department
        Map<String, Long> countByDept = employees.stream()
            .collect(Collectors.groupingBy(Employee::dept, Collectors.counting()));

        // 4. All unique departments sorted
        List<String> depts = employees.stream()
            .map(Employee::dept)
            .distinct()
            .sorted()
            .collect(Collectors.toList());

        // 5. Top 3 highest paid employees
        List<Employee> top3 = employees.stream()
            .sorted(Comparator.comparingDouble(Employee::salary).reversed())
            .limit(3)
            .collect(Collectors.toList());

        // 6. Second highest salary
        OptionalDouble secondHighest = employees.stream()
            .mapToDouble(Employee::salary)
            .distinct()
            .sorted()
            .skip(employees.stream().mapToDouble(Employee::salary).distinct().count() - 2)
            .findFirst();

        // 7. Employee names sorted by salary
        List<String> namesBySalary = employees.stream()
            .sorted(Comparator.comparingDouble(Employee::salary).reversed())
            .map(Employee::name)
            .collect(Collectors.toList());

        System.out.println("Highest paid by dept: " + highestPaid);
        System.out.println("Avg salary: " + avgSalary);
        System.out.println("Count: " + countByDept);
        System.out.println("Departments: " + depts);
        System.out.println("Top 3: " + top3);
    }
}
```

---

## 🔥 Top 50 Java Interview Q&A (Quick Reference)

```
Q01: JVM kya hai? → Java Virtual Machine, bytecode execute karta hai, platform-specific
Q02: JDK vs JRE vs JVM? → JDK ⊃ JRE ⊃ JVM. JDK for dev, JRE for run, JVM for execute
Q03: Java platform independent kyun? → Bytecode → JVM → native code. JVM platform-specific, bytecode nahi.
Q04: OOP ke 4 pillars? → Encapsulation, Inheritance, Polymorphism, Abstraction
Q05: == vs equals()? → == reference, equals() content
Q06: String immutable kyun? → Security, pool sharing, hashcode caching, thread safety
Q07: String vs StringBuilder vs StringBuffer? → Immutable vs mutable-unsafe vs mutable-safe
Q08: Checked vs Unchecked exceptions? → Checked = compile time check, Unchecked = runtime
Q09: Final vs Finally vs Finalize? → Final=keyword, Finally=block, Finalize=deprecated GC method
Q10: Abstract class vs Interface? → Abstract=partial impl+state, Interface=contract+multiple inherit
Q11: Overloading vs Overriding? → Overload=compile time+same class, Override=runtime+inheritance
Q12: Static vs Non-static? → Static=class level, non-static=object level
Q13: ArrayList vs LinkedList? → ArrayList=fast random access, LinkedList=fast insert/delete
Q14: HashMap vs TreeMap vs LinkedHashMap? → Unordered vs sorted vs insertion-order
Q15: HashMap internal working? → Array+LinkedList/Tree, hashCode+equals, load factor, rehash
Q16: fail-fast vs fail-safe iterator? → fail-fast throws CME, fail-safe copies data
Q17: Comparable vs Comparator? → Comparable=natural order inside class, Comparator=external custom
Q18: Generics kya hain? → Compile-time type safety, reusable code
Q19: Lambda expression kya hai? → Anonymous function implementing @FunctionalInterface
Q20: Stream vs Collection? → Stream=process data (lazy), Collection=store data
Q21: Optional kya hai? → NPE avoid karne ka wrapper, return type for possibly-absent values
Q22: Thread lifecycle states? → NEW → RUNNABLE → BLOCKED/WAITING/TIMED_WAITING → TERMINATED
Q23: synchronized vs ReentrantLock? → synchronized simpler, ReentrantLock more features
Q24: volatile kya karta hai? → Visibility guarantee across threads, not atomicity
Q25: Deadlock kya hai? → 2 threads circularly waiting for each other's locks
Q26: Heap vs Stack? → Heap=objects(shared), Stack=frames(per thread)
Q27: Garbage Collection kaise kaam karta hai? → Young Gen(Minor GC) → Old Gen(Major GC)
Q28: Memory leak Java mein? → Static collections, unclosed resources, ThreadLocal without remove
Q29: Singleton design pattern? → One instance, Bill Pugh/Enum preferred
Q30: Design Pattern types? → Creational, Structural, Behavioral
Q31: Serialization kya hai? → Object → bytes (persist/transfer), implements Serializable
Q32: transient keyword? → Serialization se exclude karo
Q33: try-with-resources kya hai? → AutoCloseable resources auto-close, Java 7+
Q34: var keyword? → Local type inference, Java 10+, not a keyword
Q35: Record kya hai? → Immutable data class, Java 16+, auto-generates boilerplate
Q36: Sealed class kya hai? → Restricted inheritance, Java 17+
Q37: Virtual threads kya hain? → Lightweight threads, Java 21, Project Loom
Q38: ConcurrentHashMap vs HashMap? → ConcurrentHashMap thread-safe, HashMap nahi
Q39: CompletableFuture kya hai? → Async programming, Java 8+, chain operations
Q40: Functional Interface kya hai? → Exactly 1 abstract method, @FunctionalInterface
Q41: Method reference types? → Static::method, object::method, Class::method, Class::new
Q42: flatMap vs map? → map transforms, flatMap transforms + flattens
Q43: GroupingBy kya hai? → Stream collector for grouping elements by classifier
Q44: Connection pool kyun? → DB connection expensive, pool mein reuse karo
Q45: PreparedStatement vs Statement? → PreparedStatement=safe+fast, Statement=SQL injection risk
Q46: ACID properties? → Atomicity, Consistency, Isolation, Durability
Q47: Inner class types? → Member, Static nested, Local, Anonymous
Q48: Covariant return type? → Override mein return type ko narrow kar sakte hain (subclass)
Q49: String pool kya hai? → Heap ka special area jahan string literals cached hote hain
Q50: Java 8 se 21 key features? → Lambda, Streams, Optional, var, Records, Sealed, Virtual Threads
```

---

## 📝 Hands-on Mini Projects / Practice Problems

### 1. Bank System (OOP Practice)
```java
// Implement:
// - Abstract Account (savings/current)
// - Transaction history
// - Interest calculation (template method pattern)
// - Thread-safe operations (synchronized/Lock)
```

### 2. Custom HashMap
```java
// Implement MyHashMap<K,V> with:
// - put(), get(), remove(), size()
// - Separate chaining for collision resolution
// - Resize when load factor > 0.75
```

### 3. Thread-safe Queue
```java
// Implement MyBlockingQueue<T> with:
// - put() (blocks when full)
// - take() (blocks when empty)
// - Using wait()/notifyAll() or Condition
```

### 4. Stream Operations on Employees
```java
// Given List<Employee(name, dept, salary, age)>:
// - Dept-wise average salary
// - Highest paid per dept
// - Employees age > 30 sorted by salary desc
// - Total salary of IT dept
```

### 5. Design Patterns Combo
```java
// Logger: Singleton + Strategy (console/file/database output)
// Pizza: Builder + Decorator (toppings)
// Notification: Observer (email/SMS/push)
```

---

## 🎯 Final Tips for 3-Year Experience Interview

1. **Theory + Code dono aana chahiye** — Sirf definition nahi, implementation bhi
2. **Collections internals** — HashMap, ConcurrentHashMap, ArrayList internals must know
3. **Java 8 features** — Lambda, Streams, Optional — live coding expected
4. **Multithreading scenarios** — Deadlock, thread pool, Producer-Consumer
5. **Design Patterns** — Singleton, Factory, Builder, Observer common hai
6. **Exception handling best practices** — try-with-resources, custom exceptions
7. **Memory management** — GC basics, memory leaks common interview topic
8. **Code quality** — Clean code, SOLID principles, immutability
9. **Performance** — String pool, StringBuilder, parallel streams trade-offs
10. **Modern Java** — Records, Sealed classes, var, switch expressions show karo

---

*Notes compiled from: Effective Java (Joshua Bloch), Java Concurrency in Practice, Oracle Java Docs, Baeldung, JavaPoint, DZone, LeetCode Java patterns*

*Last Updated: June 2026 | Java 21 Compatible*
