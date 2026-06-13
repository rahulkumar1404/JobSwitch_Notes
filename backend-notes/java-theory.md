# Complete Java Notes: Zero to Advanced  
### Hindi/Hinglish Explanation + English Model Answers + Hands-on + Interview Q&A

> Target: Java learners, freshers, and developers with around 3 years of experience.  
> Java Version Recommended: Java 17 or Java 21 LTS

---

## Table of Contents

1. [Java Introduction](#1-java-introduction)
2. [Java Setup](#2-java-setup)
3. [First Java Program](#3-first-java-program)
4. [Variables and Data Types](#4-variables-and-data-types)
5. [Operators](#5-operators)
6. [Control Flow](#6-control-flow)
7. [Arrays](#7-arrays)
8. [Strings](#8-strings)
9. [Methods](#9-methods)
10. [Object-Oriented Programming](#10-object-oriented-programming)
11. [Constructors](#11-constructors)
12. [Inheritance](#12-inheritance)
13. [Polymorphism](#13-polymorphism)
14. [Abstraction](#14-abstraction)
15. [Encapsulation](#15-encapsulation)
16. [Interfaces](#16-interfaces)
17. [Exception Handling](#17-exception-handling)
18. [Collections Framework](#18-collections-framework)
19. [Generics](#19-generics)
20. [Java 8 Features](#20-java-8-features)
21. [Streams API](#21-streams-api)
22. [Multithreading](#22-multithreading)
23. [Concurrency Utilities](#23-concurrency-utilities)
24. [File Handling and I/O](#24-file-handling-and-io)
25. [JVM, JRE, JDK](#25-jvm-jre-jdk)
26. [Memory Management](#26-memory-management)
27. [Garbage Collection](#27-garbage-collection)
28. [Immutability](#28-immutability)
29. [Comparable and Comparator](#29-comparable-and-comparator)
30. [Annotations](#30-annotations)
31. [Reflection](#31-reflection)
32. [JDBC Basics](#32-jdbc-basics)
33. [Unit Testing with JUnit](#33-unit-testing-with-junit)
34. [Design Patterns](#34-design-patterns)
35. [SOLID Principles](#35-solid-principles)
36. [Common Coding Problems](#36-common-coding-problems)
37. [Hands-on Projects](#37-hands-on-projects)
38. [Interview Q&A](#38-interview-qa)

---

# 1. Java Introduction

## Hindi/Hinglish Explanation

Java ek high-level, object-oriented, platform-independent programming language hai. Iska main concept hai:

> Write Once, Run Anywhere

Matlab Java code ek baar likho, aur JVM ki help se different operating systems par run kar sakte ho.

Java ka use hota hai:

- Backend development
- Android development
- Enterprise applications
- Banking systems
- Microservices
- Cloud applications
- Big data tools
- Desktop applications

## English Model Answer

Java is a high-level, object-oriented, platform-independent programming language. It follows the principle of "Write Once, Run Anywhere" because Java source code is compiled into bytecode, which can run on any platform having a JVM.

---

# 2. Java Setup

## Required Tools

- JDK: Java Development Kit
- IDE: IntelliJ IDEA / Eclipse / VS Code
- Build Tools: Maven / Gradle
- Version Control: Git

## Check Java Version

```bash
java -version
javac -version
```

## Compile and Run

```bash
javac Main.java
java Main
```

---

# 3. First Java Program

```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello Java");
    }
}
```

## Explanation

- `public`: access modifier
- `class`: class declare karne ke liye
- `Main`: class name
- `main`: program ka entry point
- `String[] args`: command line arguments
- `System.out.println`: output print karta hai

## English Model Answer

The `main` method is the entry point of a Java application. JVM starts execution from this method.

---

# 4. Variables and Data Types

## Variables

Variable ek container hota hai jo data store karta hai.

```java
int age = 25;
String name = "Rahul";
double salary = 45000.50;
boolean isActive = true;
```

## Primitive Data Types

| Type | Size | Example |
|---|---:|---|
| byte | 1 byte | `byte b = 10;` |
| short | 2 bytes | `short s = 100;` |
| int | 4 bytes | `int x = 1000;` |
| long | 8 bytes | `long l = 100000L;` |
| float | 4 bytes | `float f = 10.5f;` |
| double | 8 bytes | `double d = 99.99;` |
| char | 2 bytes | `char c = 'A';` |
| boolean | JVM dependent | `boolean flag = true;` |

## Non-Primitive Types

- String
- Array
- Class
- Interface
- Enum

## English Model Answer

Java has primitive and non-primitive data types. Primitive types store simple values, while non-primitive types store object references.

---

# 5. Operators

## Types of Operators

```java
int a = 10;
int b = 5;

System.out.println(a + b); // arithmetic
System.out.println(a > b); // relational
System.out.println(a == b); // equality
System.out.println(a > 5 && b < 10); // logical
```

## Important Operators

| Operator Type | Examples |
|---|---|
| Arithmetic | `+ - * / %` |
| Relational | `> < >= <=` |
| Equality | `== !=` |
| Logical | `&& || !` |
| Assignment | `= += -= *=` |
| Ternary | `condition ? value1 : value2` |

---

# 6. Control Flow

## if-else

```java
int age = 18;

if (age >= 18) {
    System.out.println("Eligible");
} else {
    System.out.println("Not eligible");
}
```

## switch

```java
int day = 2;

switch (day) {
    case 1 -> System.out.println("Monday");
    case 2 -> System.out.println("Tuesday");
    default -> System.out.println("Invalid day");
}
```

## loops

```java
for (int i = 1; i <= 5; i++) {
    System.out.println(i);
}

int j = 1;
while (j <= 5) {
    System.out.println(j);
    j++;
}
```

## Hands-on

Write a program to check:

- Even or odd
- Prime number
- Leap year
- Largest of three numbers
- Factorial of a number

---

# 7. Arrays

## Explanation

Array same type ke multiple values store karta hai.

```java
int[] numbers = {10, 20, 30, 40};

for (int num : numbers) {
    System.out.println(num);
}
```

## 2D Array

```java
int[][] matrix = {
    {1, 2},
    {3, 4}
};

System.out.println(matrix[0][1]); // 2
```

## English Model Answer

An array is a fixed-size data structure used to store multiple values of the same type.

---

# 8. Strings

## String Basics

```java
String name = "Java";
System.out.println(name.length());
System.out.println(name.toUpperCase());
System.out.println(name.charAt(0));
```

## String Immutability

Java me `String` immutable hoti hai. Matlab ek baar object create ho gaya to uska value change nahi hota. Modification karne par new object create hota hai.

```java
String s = "Java";
s = s + " Programming";
```

Yaha `"Java"` change nahi hua, new string `"Java Programming"` create hui.

## StringBuilder

Mutable string ke liye `StringBuilder` use karte hain.

```java
StringBuilder sb = new StringBuilder("Java");
sb.append(" Programming");
System.out.println(sb);
```

## String vs StringBuilder vs StringBuffer

| Feature | String | StringBuilder | StringBuffer |
|---|---|---|---|
| Mutable | No | Yes | Yes |
| Thread-safe | Yes, because immutable | No | Yes |
| Performance | Slow for frequent changes | Fast | Slower than StringBuilder |

## Interview Answer

String is immutable in Java. Any modification creates a new object. StringBuilder is mutable and faster for single-threaded string manipulation. StringBuffer is also mutable but synchronized, so it is thread-safe.

---

# 9. Methods

## Method Example

```java
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}
```

## Static Method

```java
public static int square(int n) {
    return n * n;
}
```

## Method Overloading

Same class me same method name but different parameters.

```java
class MathUtil {
    int add(int a, int b) {
        return a + b;
    }

    double add(double a, double b) {
        return a + b;
    }
}
```

---

# 10. Object-Oriented Programming

## Main OOP Concepts

1. Encapsulation
2. Inheritance
3. Polymorphism
4. Abstraction

## Class and Object

Class blueprint hoti hai. Object us blueprint ka real instance hota hai.

```java
class Student {
    String name;
    int age;

    void study() {
        System.out.println(name + " is studying");
    }
}

public class Main {
    public static void main(String[] args) {
        Student s = new Student();
        s.name = "Amit";
        s.age = 22;
        s.study();
    }
}
```

## English Model Answer

A class is a blueprint for creating objects, and an object is an instance of a class.

---

# 11. Constructors

## Explanation

Constructor object create hote time call hota hai. Iska naam class ke naam jaisa hota hai.

```java
class Employee {
    String name;

    Employee(String name) {
        this.name = name;
    }
}
```

## Types

- Default constructor
- Parameterized constructor
- Copy constructor style

```java
class User {
    String name;

    User() {
        this.name = "Default";
    }

    User(String name) {
        this.name = name;
    }
}
```

---

# 12. Inheritance

## Explanation

Inheritance me child class parent class ke properties aur methods use kar sakti hai.

```java
class Animal {
    void eat() {
        System.out.println("Eating");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Barking");
    }
}
```

## Types in Java

Java class-level multiple inheritance support nahi karta, but interfaces ke through possible hai.

## Interview Answer

Inheritance allows a class to acquire properties and behavior of another class. It promotes code reuse. Java does not support multiple inheritance with classes to avoid ambiguity, but it supports multiple inheritance using interfaces.

---

# 13. Polymorphism

## Explanation

Polymorphism ka matlab hai one thing many forms.

## Compile-time Polymorphism

Method overloading.

```java
class Printer {
    void print(String msg) {
        System.out.println(msg);
    }

    void print(int number) {
        System.out.println(number);
    }
}
```

## Runtime Polymorphism

Method overriding.

```java
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Cat extends Animal {
    @Override
    void sound() {
        System.out.println("Meow");
    }
}
```

## Interview Answer

Polymorphism allows the same method or object to behave differently in different contexts. Compile-time polymorphism is achieved by method overloading, while runtime polymorphism is achieved by method overriding.

---

# 14. Abstraction

## Explanation

Abstraction ka matlab implementation details hide karna aur sirf required functionality expose karna.

```java
abstract class Vehicle {
    abstract void start();

    void stop() {
        System.out.println("Vehicle stopped");
    }
}

class Car extends Vehicle {
    @Override
    void start() {
        System.out.println("Car started");
    }
}
```

## Interview Answer

Abstraction hides implementation details and exposes only essential behavior. In Java, abstraction is achieved using abstract classes and interfaces.

---

# 15. Encapsulation

## Explanation

Encapsulation ka matlab data ko private rakhna aur public getter/setter ke through access dena.

```java
class Account {
    private double balance;

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
}
```

## Interview Answer

Encapsulation is the process of wrapping data and methods into a single unit and restricting direct access to data using access modifiers.

---

# 16. Interfaces

## Explanation

Interface contract jaisa hota hai. Jo class interface implement karegi, usko methods provide karne honge.

```java
interface Payment {
    void pay(double amount);
}

class UpiPayment implements Payment {
    @Override
    public void pay(double amount) {
        System.out.println("Paid using UPI: " + amount);
    }
}
```

## Java 8 Interface Features

- Default methods
- Static methods

```java
interface Logger {
    default void log(String message) {
        System.out.println(message);
    }

    static void info() {
        System.out.println("Logger utility");
    }
}
```

---

# 17. Exception Handling

## Explanation

Exception runtime error hota hai jo program flow break kar sakta hai. Java me exception handle karne ke liye `try-catch-finally` use hota hai.

```java
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
} finally {
    System.out.println("Always executes");
}
```

## Checked vs Unchecked Exception

| Checked Exception | Unchecked Exception |
|---|---|
| Compile-time check | Runtime check |
| Must handle or declare | Optional |
| Example: IOException | Example: NullPointerException |

## Custom Exception

```java
class InvalidAgeException extends Exception {
    InvalidAgeException(String message) {
        super(message);
    }
}
```

## Interview Answer

Checked exceptions are checked at compile time and must be handled or declared. Unchecked exceptions occur at runtime and are subclasses of RuntimeException.

---

# 18. Collections Framework

## Explanation

Collections group of objects ko store and manipulate karne ke liye use hota hai.

## Main Interfaces

- List
- Set
- Queue
- Map

## List

Ordered collection, duplicates allowed.

```java
List<String> names = new ArrayList<>();
names.add("Amit");
names.add("Rahul");
names.add("Amit");
```

## Set

Duplicates allowed nahi hote.

```java
Set<String> cities = new HashSet<>();
cities.add("Delhi");
cities.add("Delhi");
```

## Map

Key-value pair store karta hai.

```java
Map<Integer, String> users = new HashMap<>();
users.put(1, "Amit");
users.put(2, "Neha");
```

## Common Implementations

| Interface | Implementations |
|---|---|
| List | ArrayList, LinkedList, Vector |
| Set | HashSet, LinkedHashSet, TreeSet |
| Queue | PriorityQueue, ArrayDeque |
| Map | HashMap, LinkedHashMap, TreeMap, ConcurrentHashMap |

## ArrayList vs LinkedList

| ArrayList | LinkedList |
|---|---|
| Dynamic array | Doubly linked list |
| Fast random access | Slow random access |
| Slow insertion/deletion in middle | Faster insertion/deletion |

## HashMap Internal Working

HashMap internally array of buckets use karta hai. Key ka `hashCode()` calculate hota hai, phir bucket index decide hota hai. Collision hone par linked list ya tree structure use hota hai.

Java 8 ke baad agar bucket me entries zyada ho jayein, linked list tree me convert ho sakti hai.

## Interview Answer

HashMap stores data in key-value pairs. It uses the key's hashCode to calculate bucket index. If multiple keys map to the same bucket, collision is handled using linked list or balanced tree after Java 8.

---

# 19. Generics

## Explanation

Generics type safety provide karte hain. Isse runtime ClassCastException avoid hota hai.

```java
List<String> names = new ArrayList<>();
names.add("Amit");
// names.add(100); // compile-time error
```

## Generic Class

```java
class Box<T> {
    private T value;

    public void setValue(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

## Interview Answer

Generics allow classes and methods to work with different data types while providing compile-time type safety.

---

# 20. Java 8 Features

## Main Features

- Lambda expressions
- Functional interfaces
- Streams API
- Optional
- Default methods in interfaces
- Method references
- Date Time API

## Lambda Expression

```java
Runnable r = () -> System.out.println("Running");
r.run();
```

## Functional Interface

Interface with exactly one abstract method.

```java
@FunctionalInterface
interface Greeting {
    void sayHello();
}
```

## Optional

NullPointerException avoid karne ke liye useful.

```java
Optional<String> name = Optional.ofNullable(null);
System.out.println(name.orElse("Default"));
```

## Method Reference

```java
List<String> names = List.of("Amit", "Rahul");
names.forEach(System.out::println);
```

---

# 21. Streams API

## Explanation

Streams collection data ko functional style me process karne ke liye use hote hain.

```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);

List<Integer> evenNumbers = numbers.stream()
        .filter(n -> n % 2 == 0)
        .toList();

System.out.println(evenNumbers);
```

## Common Stream Operations

| Operation | Type |
|---|---|
| filter | Intermediate |
| map | Intermediate |
| sorted | Intermediate |
| distinct | Intermediate |
| collect | Terminal |
| count | Terminal |
| reduce | Terminal |
| forEach | Terminal |

## Example: Employee Salary Filter

```java
class Employee {
    String name;
    double salary;

    Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
}

List<Employee> employees = List.of(
        new Employee("Amit", 50000),
        new Employee("Neha", 70000)
);

List<String> highEarners = employees.stream()
        .filter(e -> e.salary > 60000)
        .map(e -> e.name)
        .toList();
```

## Interview Answer

Streams provide a declarative and functional way to process collections. They support operations like filtering, mapping, sorting, and reducing without modifying the original data source.

---

# 22. Multithreading

## Explanation

Multithreading ka matlab ek program me multiple threads simultaneously execute karna.

## Creating Thread by Extending Thread

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread t = new MyThread();
        t.start();
    }
}
```

## Creating Thread using Runnable

```java
class MyTask implements Runnable {
    public void run() {
        System.out.println("Task running");
    }
}

Thread t = new Thread(new MyTask());
t.start();
```

## start vs run

- `start()` new thread create karta hai.
- `run()` normal method call jaisa execute hota hai.

## Synchronization

```java
class Counter {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }
}
```

## Interview Answer

Multithreading allows concurrent execution of multiple threads. In Java, threads can be created by extending Thread class or implementing Runnable interface. The `start()` method creates a new thread, while `run()` executes like a normal method.

---

# 23. Concurrency Utilities

## ExecutorService

```java
ExecutorService executor = Executors.newFixedThreadPool(3);

executor.submit(() -> {
    System.out.println("Task executed by " + Thread.currentThread().getName());
});

executor.shutdown();
```

## Callable and Future

```java
ExecutorService executor = Executors.newSingleThreadExecutor();

Future<Integer> future = executor.submit(() -> 10 + 20);

System.out.println(future.get());

executor.shutdown();
```

## ConcurrentHashMap

```java
Map<String, Integer> map = new ConcurrentHashMap<>();
map.put("A", 1);
```

## Volatile

`volatile` variable ki latest value directly main memory se read hoti hai.

```java
private volatile boolean running = true;
```

## Interview Answer

ExecutorService provides a higher-level API for managing threads. Callable can return a result, unlike Runnable. Future represents the result of an asynchronous computation.

---

# 24. File Handling and I/O

## Read File

```java
import java.nio.file.Files;
import java.nio.file.Path;

public class FileReadExample {
    public static void main(String[] args) throws Exception {
        String content = Files.readString(Path.of("data.txt"));
        System.out.println(content);
    }
}
```

## Write File

```java
Files.writeString(Path.of("output.txt"), "Hello Java");
```

## Try-with-resources

```java
try (BufferedReader reader = new BufferedReader(new FileReader("data.txt"))) {
    String line = reader.readLine();
    System.out.println(line);
}
```

## Interview Answer

Try-with-resources automatically closes resources that implement AutoCloseable, reducing boilerplate and preventing resource leaks.

---

# 25. JVM, JRE, JDK

## Explanation

| Term | Meaning |
|---|---|
| JDK | Development kit, includes compiler and tools |
| JRE | Runtime environment to run Java apps |
| JVM | Executes bytecode |

## Flow

```text
Java Source Code -> javac compiler -> Bytecode -> JVM -> Machine Code
```

## Interview Answer

JDK is used to develop Java applications, JRE is used to run Java applications, and JVM executes Java bytecode.

---

# 26. Memory Management

## JVM Memory Areas

- Heap
- Stack
- Method Area
- PC Register
- Native Method Stack

## Stack

Method calls aur local variables stack me store hote hain.

## Heap

Objects heap memory me store hote hain.

```java
Person p = new Person();
```

Yaha `p` reference stack me hota hai aur actual object heap me.

## Interview Answer

In Java, objects are stored in heap memory, while local variables and method calls are stored in stack memory. JVM manages memory automatically.

---

# 27. Garbage Collection

## Explanation

Garbage Collector unused objects ko memory se remove karta hai.

```java
Person p = new Person();
p = null;
```

Ab object eligible ho sakta hai garbage collection ke liye.

## Important GC Concepts

- Minor GC
- Major GC
- Full GC
- Young Generation
- Old Generation
- Metaspace

## Interview Answer

Garbage Collection is an automatic memory management process in Java that removes objects which are no longer referenced by the application.

---

# 28. Immutability

## Immutable Class Example

```java
final class Employee {
    private final String name;
    private final int age;

    public Employee(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

## Rules

- Class final banao
- Fields private final banao
- No setters
- Defensive copy for mutable objects

## Interview Answer

An immutable class is a class whose object state cannot be changed after creation. String is a common example of an immutable class in Java.

---

# 29. Comparable and Comparator

## Comparable

Natural sorting ke liye.

```java
class Student implements Comparable<Student> {
    int marks;

    Student(int marks) {
        this.marks = marks;
    }

    public int compareTo(Student other) {
        return this.marks - other.marks;
    }
}
```

## Comparator

Custom sorting ke liye.

```java
List<String> names = new ArrayList<>(List.of("Rahul", "Amit", "Neha"));

names.sort((a, b) -> a.compareTo(b));
```

## Interview Answer

Comparable is used for natural ordering and is implemented inside the class. Comparator is used for custom ordering and can be defined separately.

---

# 30. Annotations

## Explanation

Annotations metadata provide karti hain.

```java
@Override
public String toString() {
    return "Hello";
}
```

## Common Annotations

- `@Override`
- `@Deprecated`
- `@SuppressWarnings`
- `@FunctionalInterface`

## Custom Annotation

```java
@interface MyAnnotation {
    String value();
}
```

---

# 31. Reflection

## Explanation

Reflection runtime par class ke metadata ko inspect aur modify karne ki facility deta hai.

```java
Class<?> clazz = Class.forName("java.lang.String");
System.out.println(clazz.getName());
```

## Interview Answer

Reflection allows inspection and modification of classes, methods, and fields at runtime. It is powerful but should be used carefully because it can impact performance and security.

---

# 32. JDBC Basics

## Explanation

JDBC Java application ko database se connect karne ke liye use hota hai.

## Steps

1. Load driver
2. Create connection
3. Create statement
4. Execute query
5. Process result
6. Close connection

```java
Connection con = DriverManager.getConnection(
        "jdbc:mysql://localhost:3306/test",
        "root",
        "password"
);

PreparedStatement ps = con.prepareStatement("SELECT * FROM users WHERE id = ?");
ps.setInt(1, 1);

ResultSet rs = ps.executeQuery();

while (rs.next()) {
    System.out.println(rs.getString("name"));
}

con.close();
```

## Statement vs PreparedStatement

PreparedStatement SQL injection prevent karta hai aur precompiled hota hai.

## Interview Answer

JDBC is an API used to connect Java applications with databases. PreparedStatement is preferred over Statement because it prevents SQL injection and improves performance for repeated queries.

---

# 33. Unit Testing with JUnit

## Example

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {
    @Test
    void shouldAddTwoNumbers() {
        Calculator calculator = new Calculator();
        assertEquals(5, calculator.add(2, 3));
    }
}
```

## Good Unit Test

- Independent
- Fast
- Repeatable
- Clear assertion
- Covers edge cases

## Interview Answer

Unit testing verifies small units of code independently. JUnit is commonly used in Java for writing and running unit tests.

---

# 34. Design Patterns

## Singleton Pattern

```java
class Singleton {
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {
    }

    public static Singleton getInstance() {
        return INSTANCE;
    }
}
```

## Factory Pattern

```java
interface Notification {
    void send();
}

class EmailNotification implements Notification {
    public void send() {
        System.out.println("Email sent");
    }
}

class SmsNotification implements Notification {
    public void send() {
        System.out.println("SMS sent");
    }
}

class NotificationFactory {
    static Notification create(String type) {
        if ("email".equalsIgnoreCase(type)) {
            return new EmailNotification();
        }
        return new SmsNotification();
    }
}
```

## Builder Pattern

```java
class User {
    private String name;
    private int age;

    private User(Builder builder) {
        this.name = builder.name;
        this.age = builder.age;
    }

    static class Builder {
        private String name;
        private int age;

        Builder name(String name) {
            this.name = name;
            return this;
        }

        Builder age(int age) {
            this.age = age;
            return this;
        }

        User build() {
            return new User(this);
        }
    }
}
```

## Common Patterns

| Pattern | Use |
|---|---|
| Singleton | Single object |
| Factory | Object creation logic |
| Builder | Complex object creation |
| Strategy | Runtime behavior change |
| Observer | Event notification |
| Adapter | Interface conversion |

---

# 35. SOLID Principles

## S - Single Responsibility Principle

Ek class ka ek hi responsibility hona chahiye.

## O - Open/Closed Principle

Class extension ke liye open aur modification ke liye closed honi chahiye.

## L - Liskov Substitution Principle

Child class parent class ko replace kar sake bina behavior break kiye.

## I - Interface Segregation Principle

Large interface ke badle small specific interfaces banao.

## D - Dependency Inversion Principle

High-level modules low-level modules par directly depend nahi hone chahiye. Dono abstraction par depend karein.

## Interview Answer

SOLID principles are object-oriented design principles that help create maintainable, flexible, and scalable software.

---

# 36. Common Coding Problems

## Reverse String

```java
public static String reverse(String input) {
    StringBuilder sb = new StringBuilder(input);
    return sb.reverse().toString();
}
```

## Palindrome

```java
public static boolean isPalindrome(String input) {
    int left = 0;
    int right = input.length() - 1;

    while (left < right) {
        if (input.charAt(left) != input.charAt(right)) {
            return false;
        }
        left++;
        right--;
    }

    return true;
}
```

## Count Character Frequency

```java
public static Map<Character, Integer> frequency(String input) {
    Map<Character, Integer> map = new HashMap<>();

    for (char ch : input.toCharArray()) {
        map.put(ch, map.getOrDefault(ch, 0) + 1);
    }

    return map;
}
```

## Find Duplicate Numbers

```java
public static Set<Integer> findDuplicates(int[] arr) {
    Set<Integer> seen = new HashSet<>();
    Set<Integer> duplicates = new HashSet<>();

    for (int num : arr) {
        if (!seen.add(num)) {
            duplicates.add(num);
        }
    }

    return duplicates;
}
```

## Two Sum

```java
public static int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();

    for (int i = 0; i < nums.length; i++) {
        int required = target - nums[i];

        if (map.containsKey(required)) {
            return new int[]{map.get(required), i};
        }

        map.put(nums[i], i);
    }

    return new int[]{-1, -1};
}
```

---

# 37. Hands-on Projects

## Project 1: Student Management System

### Requirements

- Add student
- Update student
- Delete student
- Search student
- Display all students

### Concepts Used

- Class and Object
- ArrayList
- Scanner
- Encapsulation
- CRUD operations

### Basic Model

```java
class Student {
    private int id;
    private String name;
    private int age;

    public Student(int id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }

    public int getId() {
        return id;
    }

    public String toString() {
        return id + " " + name + " " + age;
    }
}
```

---

## Project 2: Banking System

### Requirements

- Create account
- Deposit money
- Withdraw money
- Check balance
- Transaction history

### Concepts Used

- Encapsulation
- Exception handling
- Collections
- OOP

---

## Project 3: Employee Payroll System

### Requirements

- Add employee
- Calculate salary
- Sort employees by salary
- Filter high salary employees using streams

### Concepts Used

- Collections
- Comparator
- Streams API
- OOP

---

## Project 4: File-based Todo App

### Requirements

- Add task
- Mark task complete
- Delete task
- Save tasks into file
- Load tasks from file

### Concepts Used

- File I/O
- Collections
- Exception handling

---

## Project 5: Mini E-commerce Cart

### Requirements

- Product list
- Add to cart
- Remove from cart
- Calculate total price
- Apply discount strategy

### Concepts Used

- OOP
- Strategy pattern
- Collections
- Interfaces

---

# 38. Interview Q&A

## 1. What is Java?

**Answer:**  
Java is a high-level, object-oriented, platform-independent programming language. Java code is compiled into bytecode, which runs on the JVM.

---

## 2. Why is Java platform independent?

**Answer:**  
Java is platform independent because Java source code is compiled into bytecode. This bytecode can run on any platform that has a JVM.

---

## 3. What is JVM?

**Answer:**  
JVM stands for Java Virtual Machine. It executes Java bytecode and provides runtime environment, memory management, and garbage collection.

---

## 4. Difference between JDK, JRE, and JVM?

**Answer:**  
JDK is used for developing Java applications. JRE provides the environment to run Java applications. JVM executes bytecode.

---

## 5. Is Java fully object-oriented?

**Answer:**  
No. Java is not fully object-oriented because it supports primitive data types like int, char, boolean, etc.

---

## 6. What is constructor?

**Answer:**  
A constructor is a special method used to initialize objects. It has the same name as the class and does not have a return type.

---

## 7. What is method overloading?

**Answer:**  
Method overloading means having multiple methods with the same name but different parameter lists in the same class.

---

## 8. What is method overriding?

**Answer:**  
Method overriding occurs when a child class provides its own implementation of a method already defined in the parent class.

---

## 9. Difference between overloading and overriding?

**Answer:**  
Overloading is compile-time polymorphism and happens in the same class with different parameters. Overriding is runtime polymorphism and happens between parent and child classes.

---

## 10. What is inheritance?

**Answer:**  
Inheritance allows one class to acquire properties and methods of another class. It promotes code reuse.

---

## 11. Why does Java not support multiple inheritance with classes?

**Answer:**  
Java does not support multiple inheritance with classes to avoid ambiguity, commonly known as the diamond problem.

---

## 12. What is interface?

**Answer:**  
An interface is a contract that defines methods a class must implement. It is used to achieve abstraction and multiple inheritance.

---

## 13. Difference between abstract class and interface?

**Answer:**  
An abstract class can have abstract and non-abstract methods, constructors, and instance variables. An interface mainly defines contracts and supports default and static methods from Java 8.

---

## 14. What is encapsulation?

**Answer:**  
Encapsulation is the process of wrapping data and methods into a single unit and restricting direct access to fields using access modifiers.

---

## 15. What is abstraction?

**Answer:**  
Abstraction hides implementation details and exposes only essential features to the user.

---

## 16. Why is String immutable?

**Answer:**  
String is immutable for security, caching, thread-safety, and performance reasons. Since String objects cannot be changed, they can be safely shared.

---

## 17. Difference between String, StringBuilder, and StringBuffer?

**Answer:**  
String is immutable. StringBuilder is mutable and not thread-safe. StringBuffer is mutable and thread-safe because its methods are synchronized.

---

## 18. What is exception handling?

**Answer:**  
Exception handling is a mechanism to handle runtime errors and maintain normal application flow using try, catch, finally, throw, and throws.

---

## 19. Checked vs unchecked exception?

**Answer:**  
Checked exceptions are checked at compile time and must be handled or declared. Unchecked exceptions occur at runtime and are subclasses of RuntimeException.

---

## 20. What is final keyword?

**Answer:**  
The `final` keyword can be used with variables, methods, and classes. A final variable cannot be reassigned, a final method cannot be overridden, and a final class cannot be inherited.

---

## 21. What is static keyword?

**Answer:**  
The `static` keyword belongs to the class rather than an object. Static variables and methods can be accessed without creating an object.

---

## 22. What is HashMap?

**Answer:**  
HashMap is a Map implementation that stores key-value pairs. It allows one null key and multiple null values. It does not maintain insertion order.

---

## 23. How does HashMap work internally?

**Answer:**  
HashMap uses hashing. It calculates the hash of the key to find the bucket index. If collision occurs, entries are stored in a linked list or tree structure after Java 8.

---

## 24. Difference between HashMap and ConcurrentHashMap?

**Answer:**  
HashMap is not thread-safe. ConcurrentHashMap is thread-safe and allows concurrent read and write operations with better performance than Hashtable.

---

## 25. Difference between ArrayList and LinkedList?

**Answer:**  
ArrayList uses a dynamic array and provides fast random access. LinkedList uses a doubly linked list and is better for frequent insertions and deletions.

---

## 26. What is fail-fast iterator?

**Answer:**  
A fail-fast iterator throws ConcurrentModificationException if the collection is modified while iterating, except through the iterator's own remove method.

---

## 27. What is Comparable?

**Answer:**  
Comparable is used to define natural ordering of objects using the compareTo method.

---

## 28. What is Comparator?

**Answer:**  
Comparator is used to define custom ordering of objects using the compare method.

---

## 29. What is lambda expression?

**Answer:**  
A lambda expression is a concise way to represent an anonymous function. It is mainly used with functional interfaces.

---

## 30. What is functional interface?

**Answer:**  
A functional interface is an interface with exactly one abstract method. It can have default and static methods.

---

## 31. What is Stream API?

**Answer:**  
Stream API is used to process collections in a functional and declarative way. It supports operations like filter, map, sorted, collect, and reduce.

---

## 32. Difference between map and flatMap?

**Answer:**  
`map` transforms each element into another element. `flatMap` transforms each element into a stream and then flattens multiple streams into a single stream.

---

## 33. What is Optional?

**Answer:**  
Optional is a container object that may or may not contain a non-null value. It helps avoid NullPointerException.

---

## 34. What is multithreading?

**Answer:**  
Multithreading is the process of executing multiple threads concurrently to improve application performance and responsiveness.

---

## 35. Difference between Thread and Runnable?

**Answer:**  
Thread is a class, while Runnable is an interface. Implementing Runnable is preferred because Java supports single class inheritance but multiple interface implementation.

---

## 36. Difference between start and run?

**Answer:**  
`start()` creates a new thread and internally calls `run()`. Calling `run()` directly executes it like a normal method in the current thread.

---

## 37. What is synchronization?

**Answer:**  
Synchronization is used to control access to shared resources by multiple threads to avoid data inconsistency.

---

## 38. What is volatile?

**Answer:**  
The `volatile` keyword ensures that the latest value of a variable is read from main memory by all threads.

---

## 39. What is deadlock?

**Answer:**  
Deadlock occurs when two or more threads wait for each other forever to release resources.

---

## 40. What is ExecutorService?

**Answer:**  
ExecutorService is a framework for managing and controlling thread execution using thread pools.

---

## 41. Difference between Runnable and Callable?

**Answer:**  
Runnable does not return a result and cannot throw checked exceptions. Callable returns a result and can throw checked exceptions.

---

## 42. What is garbage collection?

**Answer:**  
Garbage collection is the automatic process of removing unused objects from memory.

---

## 43. Can we force garbage collection?

**Answer:**  
No. We can request garbage collection using `System.gc()`, but it is not guaranteed.

---

## 44. What is immutable class?

**Answer:**  
An immutable class is a class whose object state cannot be changed after creation.

---

## 45. How to create immutable class?

**Answer:**  
Make the class final, fields private and final, provide no setters, initialize fields through constructor, and use defensive copies for mutable fields.

---

## 46. What is reflection?

**Answer:**  
Reflection allows inspecting and modifying classes, methods, fields, and constructors at runtime.

---

## 47. What is serialization?

**Answer:**  
Serialization is the process of converting an object into a byte stream. Deserialization converts byte stream back into an object.

---

## 48. What is transient keyword?

**Answer:**  
The `transient` keyword prevents a field from being serialized.

---

## 49. What is JDBC?

**Answer:**  
JDBC is a Java API used to connect and interact with databases.

---

## 50. Statement vs PreparedStatement?

**Answer:**  
Statement executes static SQL queries. PreparedStatement executes parameterized queries, prevents SQL injection, and can be precompiled.

---

# Advanced Interview Questions for 3 Years Experience

## 51. What are equals and hashCode?

**Answer:**  
`equals()` checks logical equality between objects. `hashCode()` returns an integer hash value. If two objects are equal according to equals, they must have the same hashCode.

---

## 52. What happens if hashCode is not overridden with equals?

**Answer:**  
Hash-based collections like HashMap and HashSet may behave incorrectly because equal objects may be placed in different buckets.

---

## 53. What is contract between equals and hashCode?

**Answer:**  
If two objects are equal using equals, their hashCode must be same. However, two objects with same hashCode may not necessarily be equal.

---

## 54. What is classloader?

**Answer:**  
ClassLoader loads Java classes into JVM at runtime. Java has Bootstrap, Extension/Platform, and Application classloaders.

---

## 55. What is memory leak in Java?

**Answer:**  
A memory leak happens when objects are no longer needed but still referenced, so garbage collector cannot remove them.

---

## 56. What are common causes of memory leaks?

**Answer:**  
Common causes include static collections, unclosed resources, listeners not removed, improper cache usage, and ThreadLocal misuse.

---

## 57. What is ThreadLocal?

**Answer:**  
ThreadLocal provides variables that are local to each thread. Each thread has its own isolated copy.

---

## 58. What is ForkJoinPool?

**Answer:**  
ForkJoinPool is used for parallel task execution by splitting large tasks into smaller subtasks and combining their results.

---

## 59. What is CompletableFuture?

**Answer:**  
CompletableFuture is used for asynchronous programming. It allows chaining, combining, and handling async computation results.

---

## 60. What is difference between parallel stream and normal stream?

**Answer:**  
Normal stream processes data sequentially. Parallel stream splits data into multiple chunks and processes them concurrently using ForkJoinPool.

---

# Quick Revision Cheat Sheet

## OOP

```text
Encapsulation = Data hiding
Inheritance = Code reuse
Polymorphism = Many forms
Abstraction = Hide implementation
```

## Collection Selection

```text
Need duplicates + order: ArrayList
Need unique values: HashSet
Need sorted unique values: TreeSet
Need key-value: HashMap
Need thread-safe map: ConcurrentHashMap
Need FIFO: Queue
Need priority ordering: PriorityQueue
```

## Java 8

```text
Lambda = Anonymous function
Functional Interface = Single abstract method
Stream = Data processing pipeline
Optional = Null handling container
```

## Threading

```text
start() = new thread
run() = normal method call
synchronized = lock
volatile = visibility
Callable = returns value
Runnable = no return value
```

---

# Daily Practice Plan

## Week 1

- Java basics
- Variables
- Operators
- Loops
- Arrays
- Strings

## Week 2

- OOP
- Inheritance
- Polymorphism
- Abstraction
- Encapsulation
- Interfaces

## Week 3

- Exception handling
- Collections
- Generics
- Comparable
- Comparator

## Week 4

- Java 8
- Lambda
- Streams
- Optional
- Date Time API

## Week 5

- Multithreading
- ExecutorService
- Callable
- Future
- Synchronization

## Week 6

- JVM
- GC
- Memory management
- Design patterns
- SOLID

## Week 7

- JDBC
- File handling
- Unit testing
- Mini projects

## Week 8

- Interview questions
- Coding problems
- Mock interviews
- Resume project explanation

---

# Final Advice

Java me strong banne ke liye sirf theory enough nahi hai. Har topic ke baad small hands-on program zaroor banao.

For 3 years experience, interview me mostly ye areas deeply puchte hain:

- OOP
- Collections internal working
- HashMap
- Java 8 streams
- Exception handling
- Multithreading
- JVM memory
- Garbage collection
- SQL/JDBC basics
- Design patterns
- Real project explanation

Keep practicing and build small projects.

---
