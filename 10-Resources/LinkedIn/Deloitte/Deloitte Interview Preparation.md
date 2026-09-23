# 1. Java 17 Features

Java 17 is an **LTS (Long-Term Support)** release and is widely used in enterprise applications.

### Important Java 17 features

1. Sealed Classes
2. Pattern Matching for `switch` — preview in Java 17
3. Pattern Matching for `instanceof`
4. Records
5. Text Blocks
6. Helpful NullPointerExceptions
7. Strong Encapsulation of JDK Internals

---

## 1.1 Sealed Classes

A sealed class restricts which classes can extend it.

```
public sealed class Payment
        permits CardPayment, UPIPayment {
}
```

Allowed:

```
public final class CardPayment extends Payment {
}
```

```
public final class UPIPayment extends Payment {
}
```

A class not listed in `permits` cannot extend `Payment`.

### Why use it?

It gives controlled inheritance.

### Real-world example

Suppose your application supports only:

```
Payment
 ├── CardPayment
 ├── UPIPayment
 └── CashPayment
```

You can explicitly control the permitted subclasses.

---
## 1.2 Pattern Matching for switch — preview in Java 17

In **Java 17**, **Pattern Matching for switch** was introduced as a **preview feature** via Prior to this version, `switch` statements were strictly restricted to checking exact values of primitives, enums, and Strings.

This preview capability allows developers to switch on **arbitrary object types**, matching patterns directly in the `case` labels while eliminating the verbose boilerplate of `instanceof` checks and explicit type casting.

Because it is a **preview feature** in Java 17, it requires compiling and running your code using the `--enable-preview` flag.


Core Mechanics & Features

1. Type Patterns (Smart Casting)

Instead of matching an exact constant, you can match a type pattern. When an object matches the target type, the compiler **automatically casts it** and assigns it to a new pattern variable available strictly within that case block.

java

```
// Traditional verbose way before Java 17
if (obj instanceof String) {
    String s = (String) obj;
    System.out.println("String length: " + s.length());
}

// Java 17 Preview: Switch Pattern Matching
switch (obj) {
    case String s  -> System.out.println("String length: " + s.length());
    case Integer i -> System.out.println("Integer value: " + i * 2);
    default        -> System.out.println("Unknown type");
}
```

Use code with caution.

2. Guarded Patterns (`&&` or `when`)

You can add extra conditional filters directly into a `case` label using logic expressions. Note that in Java 17's early preview, this was written using `&&` expressions inside the case label, which later evolved into the `when` keyword in Java 21.

java

```
switch (obj) {
    // Java 17 preview syntax used "&&" to guard a pattern
    case String s && s.length() > 5 -> System.out.println("Long string: " + s);
    case String s                   -> System.out.println("Short string: " + s);
    default                         -> System.out.println("Not a string");
}
```

Use code with caution.

3. Explicit `null` Handling

Historically, passing a `null` reference into a `switch` statement immediately threw a `NullPointerException`. Java 17 introduced the ability to explicitly isolate `null` with a dedicated `case null` label.

java

```
switch (obj) {
    case null      -> System.out.println("Object is null!");
    case String s  -> System.out.println("It's a string: " + s);
    default        -> System.out.println("Something else");
}
```

Use code with caution.

How it Intersects with Sealed Classes

As discussed in your earlier questions, this feature is incredibly powerful when paired with **sealed classes**. Because the compiler knows every allowable child of a sealed class hierarchy at compile time, you achieve **exhaustiveness**. You do not need a `default` branch:

java

```
public sealed interface Vehicle permits Car, Truck {}
public final class Car implements Vehicle {}
public final class Truck implements Vehicle {}

// The compiler knows all options; no 'default' is needed!
String description = switch (vehicle) {
    case Car c   -> "Driving a car";
    case Truck t -> "Driving a truck";
};
```

---
## 1.3 Pattern Matching for `instanceof`

Unlike pattern matching for `switch` (which was a preview feature in Java 17), **Pattern Matching for `instanceof`** is a **fully production-ready, standard feature** in Java 17. It was finalized earlier in Java 16 via.

It eliminates the tedious, repetitive boilerplate of checking an object's type and then explicitly casting it on the very next line.


The Problem vs. The Solution

The Old Way (Pre-Java 16)

You had to state the type **three times** just to use a basic method:

java

```
if (obj instanceof String) {            // 1. Check type
    String s = (String) obj;            // 2. Cast explicitly
    System.out.println(s.toUpperCase());// 3. Use it
}
```

Use code with caution.

The Modern Way (Java 16+)

You combine the type check and the variable declaration into a single, clean expression:

java

```
if (obj instanceof String s) {
    // 's' is automatically cast and ready to use!
    System.out.println(s.toUpperCase());
}
```

Use code with caution.

---

Core Mechanics & Scope Rules

The pattern variable (like `s` in the example above) is governed by **Flow Scoping**. It is only in scope where the compiler can absolutely guarantee that the `instanceof` check evaluated to `true`.

1. Conditional Logic (`&&`)

You can use the pattern variable immediately on the same line if you use a conditional **AND** (`&&`).

java

```
// Perfectly legal: 's' is in scope for the second condition
if (obj instanceof String s && s.length() > 5) {
    System.out.println(s.substring(0, 5));
}
```

Use code with caution.

2. Why Conditional OR (`||`) Fails

You **cannot** use the pattern variable with an **OR** (`||`) operator. If the first part is false, the second part evaluates, but `s` would be uninitialized.

java

```
// COMPILATION ERROR! 
if (obj instanceof String s || s.length() > 5) { 
    // The compiler stops you because if 'obj' is not a String, 
    // s.length() makes no sense.
}
```

Use code with caution.

3. Early Returns (Inverting Scope)

If you use a pattern matching check to return early from a method, that variable remains safely in scope for the rest of the method body.

java

```
public void process(Object obj) {
    if (!(obj instanceof String s)) {
        return; // Exit early if it's not a String
    }
    
    // 's' is completely safe and in scope here!
    System.out.println("Processing: " + s.toLowerCase());
}
```

Use code with caution.

Clean Real-World Example: Overriding `equals()`

One of the most immediate, practical places to use this feature is when overriding the `equals()` method in domain classes. It turns a multi-line casting mess into a highly readable one-liner:

java

```
public class Employee {
    private String name;
    private int id;

    @Override
    public boolean equals(Object obj) {
        // Checks type, smart-casts to 'other', and compares fields all at once
        return (obj instanceof Employee other) && 
               this.id == other.id && 
               this.name.equals(other.name);
    }
}
```

Use code with caution.

Would you like to see how this feature fits into the bigger picture? I can:

1. Show how **Pattern Matching for `instanceof`** works hand-in-hand with **Java Records**.
2. Discuss the **performance impact** (spoiler: it's purely a compile-time sugar feature, so no runtime overhead!).
3. Transition back to how this logic expands into **complex multi-layered Switch expressions**.
---
## 1.4 record

Introduced as a standard feature in Java 16 via **Java Records** are a special type of class designed to act as transparent, immutable containers for shallow data.

Before Records, creating a simple data transfer object (DTO) required writing a massive amount of boilerplate code (private fields, constructors, getters, `equals()`, `hashCode()`, and `toString()`). Records eliminate all of it.

---

The Boilerplate Killer

The Old Way: A Standard Data Class (30+ lines of code)

java

```
public class User {
    private final String name;
    private final int id;

    public User(String name, int id) {
        this.name = name;
        this.id = id;
    }

    public String getName() { return name; }
    public int getId() { return id; }

    @Override
    public boolean equals(Object o) { /* ... tedious logic ... */ }
    @Override
    public int hashCode() { /* ... tedious logic ... */ }
    @Override
    public String toString() { return "User{name='" + name + "', id=" + id + "}"; }
}
```

Use code with caution.

The Modern Way: A Java Record (1 line of code)

java

```
public record User(String name, int id) {}
```

Use code with caution.

By writing just this single line, the Java compiler **automatically generates**:

- **Immutable fields** (`private final String name;`, etc.)
- A **constructor** initializing all fields.
- **Getter methods** matching the field names directly (e.g., `user.name()` and `user.id()` instead of `getName()`).
- Perfectly implemented **`equals()`**, **`hashCode()`**, and **`toString()`** methods.

---

Core Characteristics of Records

1. **Implicitly Final:** Records are `final`. They cannot be extended by other classes, and they cannot extend any other class (because they already implicitly extend `java.lang.Record`).
2. **Shallow Immutability:** The fields generated are `final`. Once created, their references cannot change. However, if a field holds a mutable object (like a `List`), the contents of that list can still be modified.
3. **Interfaces Allowed:** While they cannot inherit classes, records **can implement interfaces**.

---

Advanced Customization: The Compact Constructor

If you want to validate data (e.g., prevent a negative ID or null name) before the record is created, you can use a **compact constructor**. You don't need to manually map `this.name = name;` because the compiler handles that automatically right after your validation block.

java

```
public record User(String name, int id) {
    // Compact constructor (no parameter list)
    public User {
        if (id < 0) {
            throw new IllegalArgumentException("ID cannot be negative!");
        }
        if (name == null || name.isBlank()) {
            name = "Anonymous"; // You can also reassign values
        }
    }
}
```

Use code with caution.


Direct Synergy with Sealed Classes & Pattern Matching

Records represent the **data**, Sealed Classes represent the **hierarchy**, and Pattern Matching represents the **logic**. Together, they allow you to write incredibly expressive code.

java

```
public sealed interface Order permits DigitalOrder, PhysicalOrder {}

// Records seamlessly implementing a sealed interface
public record DigitalOrder(String email, double price) implements Order {}
public record PhysicalOrder(String shippingAddress, double price) implements Order {}

// Processing them cleanly via Switch Pattern Matching
public void processOrder(Order order) {
    switch (order) {
        case DigitalOrder d  -> System.out.println("Emailing download link to " + d.email());
        case PhysicalOrder p -> System.out.println("Shipping package to " + p.shippingAddress());
    }
}
```

Use code with caution.

Would you like to explore how Records work in deep architectural scenarios? I can:

1. Show how **Record Patterns** (introduced as a preview in Java 19 and finalized in Java 21) allow you to deconstruct records inside `switch` statements or `instanceof` blocks without even calling the getter methods.
2. Explain how to handle **serialization** with Records (which is significantly safer than regular classes).
3. Contrast **Java Records** with **Lombok's `@Data` or `@Value` annotations** to see when you should use one over the other.

Just reply with the number or concept you'd like to look at next!

---
# 2. Java 21 Features

Java 21 is also an **LTS release**.

Important features:

- Virtual Threads
- Record Patterns
- Pattern Matching for `switch`
- Sequenced Collections
- String Templates were introduced as a preview feature in Java 21
- Generational ZGC
- Pattern matching improvements

For a Java backend interview, **Virtual Threads** are especially important.

---

# 3. Virtual Threads

Virtual threads are lightweight threads designed to make high-concurrency applications easier to build.

Traditional platform threads are relatively expensive.

Virtual threads are much lighter.

### Example

```
Thread.startVirtualThread(() -> {
    System.out.println("Running task");
});
```

### Traditional approach

```
new Thread(() -> {
    System.out.println("Task");
}).start();
```

### Why Virtual Threads?

Suppose your application receives:

```
10,000 requests
```

Many requests spend time waiting for:

- Database
- REST API
- File I/O
- Network

Virtual threads can allow many concurrent blocking tasks without requiring a huge number of heavyweight OS/platform threads.

### Important interview point

Virtual threads primarily improve **concurrency**, not the raw execution speed of CPU-heavy calculations.

---

# 4. Java 17 vs Java 21

| Java 17                           | Java 21                                 |
| --------------------------------- | --------------------------------------- |
| LTS                               | LTS                                     |
| Sealed classes                    | Virtual threads                         |
| Records                           | Record patterns                         |
| Pattern matching for `instanceof` | Pattern matching for `switch` finalized |
| Text blocks                       | Sequenced collections                   |
| Strong encapsulation              | Generational ZGC                        |

### Interview Answer

> Java 17 introduced features such as sealed classes, records, pattern matching for instanceof and text blocks. Java 21 added major features such as virtual threads, record patterns, pattern matching for switch and sequenced collections. For backend applications, virtual threads are particularly important because they improve scalability for high-concurrency I/O-bound workloads.

---

# 5. Singleton Class

## Definition

A Singleton is a class designed so that **only one instance of the class exists within the intended scope**, commonly the application/runtime scope.

### Basic implementation

```
public class Singleton {

    private static Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {

        if (instance == null) {
            instance = new Singleton();
        }

        return instance;
    }
}
```

Usage:

```
Singleton s1 = Singleton.getInstance();
Singleton s2 = Singleton.getInstance();

System.out.println(s1 == s2);
```

Output:

```
true
```

---

# Problem with this implementation

The above implementation is **not thread-safe**.

Two threads could simultaneously see:

```
instance == null
```

and create two objects.

---

# Thread-Safe Singleton

One approach:

```
public class Singleton {

    private static volatile Singleton instance;

    private Singleton() {
    }

    public static Singleton getInstance() {

        if (instance == null) {

            synchronized (Singleton.class) {

                if (instance == null) {
                    instance = new Singleton();
                }

            }
        }

        return instance;
    }
}
```

This is called **Double-Checked Locking**.

---

# Singleton in Spring Boot

An important interview point:

Spring beans are **singleton-scoped by default**.

```
@Service
public class PaymentService {
}
```

By default, Spring creates one bean instance per Spring application context.

You don't normally need to manually implement the Singleton pattern for ordinary Spring services.

---

# Cross Question

### Is Spring Singleton the same as Singleton Design Pattern?

Not exactly.

Spring's singleton scope means:

> One bean instance per Spring `ApplicationContext`.

The traditional Singleton pattern controls instance creation through the class itself.

---

# 6. Abstract Class vs Interface

## Abstract Class

An abstract class is a class that cannot normally be instantiated directly and can contain both abstract and concrete methods.

```
abstract class Animal {

    abstract void sound();

    void eat() {
        System.out.println("Eating");
    }
}
```

Child:

```
class Dog extends Animal {

    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

---

# Interface

An interface defines a contract that implementing classes agree to follow.

```
interface Payment {

    void pay();
}
```

Implementation:

```
class UPIPayment implements Payment {

    @Override
    public void pay() {
        System.out.println("Pay using UPI");
    }
}
```

---

# Difference

|Abstract Class|Interface|
|---|---|
|Uses `abstract class`|Uses `interface`|
|Can have instance fields|Fields are implicitly `public static final`|
|Can have constructors|Cannot have constructors|
|Can have concrete methods|Can have default/static methods|
|A class extends one class|A class can implement multiple interfaces|
|Useful for shared state/behavior|Useful for contracts/capabilities|

### Interview Answer

> I use an abstract class when related classes share common state or implementation. I use an interface when I want to define a contract that multiple unrelated classes can implement.

---

# 7. Immutable Class

## Definition

An immutable object is an object whose state **cannot be changed after it is created**.

A classic example is:

```
String
```

Example:

```
String name = "Java";

name.concat(" Programming");

System.out.println(name);
```

Output:

```
Java
```

A new String would be created rather than modifying the original object.

---

# Creating an Immutable Class

```
public final class Employee {

    private final int id;
    private final String name;

    public Employee(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }
}
```

### Important characteristics

- Class should generally be `final`
- Fields should be `private`
- Fields should be `final`
- Initialize fields through constructor
- Don't provide setters
- Don't expose mutable internal objects directly
- Use defensive copies when fields are mutable

---

# Why Immutable Objects?

Advantages:

- Thread safety
- Easier reasoning
- Safe sharing
- Useful for caching
- Good for keys in hash-based collections

---

# Important Cross Question

### Is `final` enough to make an object immutable?

**No.**

Example:

```
private final List<String> names;
```

The reference cannot point to another list, but the list itself can still be modified.

Therefore, defensive copying may be required.

---

# 8. Stored Procedure

## Definition

A Stored Procedure is a set of SQL statements stored and executed inside the database.

Example in MySQL:

```
CREATE PROCEDURE getEmployeeById(IN empId INT)
BEGIN
    SELECT *
    FROM employee
    WHERE id = empId;
END;
```

Call:

```
CALL getEmployeeById(101);
```

---

# Why use Stored Procedures?

They can be useful for:

- Complex database operations
- Reusable database logic
- Batch operations
- Centralizing some database-side processing
- Existing enterprise/database systems

---

# Stored Procedure with Spring Data JPA

For example:

```
@Procedure(procedureName = "getEmployeeById")
Employee getEmployeeById(Integer empId);
```

The exact mapping depends on the procedure's parameters and result structure.

---

# Stored Procedure vs Normal Query

|Stored Procedure|Normal Query|
|---|---|
|Stored in database|Usually defined in application code|
|Can contain multiple SQL statements|Usually a specific query|
|Executes on DB|Sent from application|
|Useful for complex DB operations|Common for normal CRUD|

### Interview Cross Question

**Should we always use stored procedures?**

No.

The choice depends on the application's architecture, database requirements, existing systems, performance requirements, maintainability, and team practices.

---

# 9. Spring Boot Auto-Configuration

This is a **very important Spring Boot interview question**.

## Definition

Spring Boot Auto-Configuration automatically configures Spring components based on:

- Dependencies present in the classpath
- Existing beans
- Application configuration/properties
- Conditional configuration

---

# Example

Suppose you add:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

Spring Boot detects relevant web dependencies and configures components needed for a web application.

---

# `@SpringBootApplication`

This annotation combines three important annotations:

```
@SpringBootApplication
```

Conceptually:

```
@SpringBootApplication
       │
       ├── @SpringBootConfiguration
       ├── @EnableAutoConfiguration
       └── @ComponentScan
```

---

# `@EnableAutoConfiguration`

This tells Spring Boot to apply appropriate auto-configuration based on the application's environment.

For example, if JPA dependencies are available, Spring Boot can configure infrastructure such as:

- EntityManagerFactory
- Transaction management infrastructure
- DataSource-related configuration

provided the necessary conditions/configuration are satisfied.

---

# How Auto-Configuration Works

Simplified flow:

```
Spring Boot Application
        ↓
@SpringBootApplication
        ↓
@EnableAutoConfiguration
        ↓
Check Classpath
        ↓
Check Conditions
        ↓
Create Required Beans
        ↓
Application Starts
```

---

# Conditional Configuration

Spring Boot heavily uses conditional annotations.

Examples:

```
@ConditionalOnClass
@ConditionalOnMissingBean
@ConditionalOnProperty
```

For example:

```
If DataSource class exists
        +
No custom DataSource bean exists
        ↓
Auto-configure DataSource
```

---

# Important Interview Question

### Does Auto-Configuration override our custom configuration?

Generally, Spring Boot's auto-configuration is designed to **back off when you provide your own configuration/bean** where applicable.

Example:

```
@Bean
DataSource myDataSource() {
    ...
}
```

If the relevant auto-configuration sees an existing bean and has a `@ConditionalOnMissingBean` condition, it won't create its default bean.

---

# 10. Race Condition

## Definition

A race condition occurs when multiple threads access shared mutable data concurrently and the final result depends on the timing/order of execution.

---

## Example

Suppose:

```
int count = 0;
```

Two threads execute:

```
count++;
```

`count++` is not a single indivisible operation.

Conceptually:

```
Read count
   ↓
Add 1
   ↓
Write count
```

Two threads can read the same old value.

---

# Example

Initial:

```
count = 0
```

Thread 1:

```
read 0
```

Thread 2:

```
read 0
```

Both calculate:

```
0 + 1 = 1
```

Both write:

```
count = 1
```

Expected:

```
2
```

Actual:

```
1
```

---

# How to Avoid Race Conditions

### 1. `synchronized`

```
public synchronized void increment() {
    count++;
}
```

---

### 2. Lock

```
Lock lock = new ReentrantLock();

lock.lock();

try {
    count++;
} finally {
    lock.unlock();
}
```

---

### 3. Atomic Variables

```
AtomicInteger count = new AtomicInteger();

count.incrementAndGet();
```

---

### 4. Immutable Objects

Immutable state reduces the need for synchronization.

---

### 5. Concurrent Collections

For example:

```
ConcurrentHashMap
```

instead of using a normal `HashMap` for concurrent modifications.

---

# `volatile` Important Point

A common interview trap:

### Does `volatile` make `count++` thread-safe?

**No.**

`volatile` provides visibility guarantees, but it does not make compound operations like:

```
count++;
```

atomic.

For atomic increments, use:

```
AtomicInteger
```

or appropriate synchronization.

---

# 11. Scalability in a Project

## Definition

Scalability is the ability of an application to handle increasing workload by adding resources while maintaining acceptable performance.

Suppose:

```
100 users
```

becomes:

```
100,000 users
```

A scalable system should be able to handle the increased load.

---

# Types of Scalability

## Vertical Scaling

Increase resources of one server.

```
4 CPU
8 GB RAM

        ↓

16 CPU
32 GB RAM
```

Also called:

**Scale Up**

---

## Horizontal Scaling

Add more application instances.

```
             Load Balancer
                  |
       ┌──────────┼──────────┐
       ↓          ↓          ↓
   Server 1   Server 2   Server 3
```

Also called:

**Scale Out**

---

# How to Make Spring Boot Application Scalable?

Common approaches:

### Application

- Stateless services
- Efficient database queries
- Pagination
- Caching
- Async processing where appropriate
- Connection pooling

### Database

- Indexing
- Query optimization
- Read replicas
- Appropriate schema design

### Infrastructure

- Horizontal scaling
- Load balancing
- Containerization
- Auto-scaling

### Architecture

- Clear module boundaries
- Asynchronous messaging where appropriate
- Externalized configuration
- Observability

---

# 12. Load Balancing

## Definition

Load balancing distributes incoming requests across multiple application servers/instances.

Example:

```
                 Users
                   |
                   ↓
             Load Balancer
             /      |      \
            ↓       ↓       ↓
        Instance  Instance  Instance
           1         2         3
```

Instead of every request going to one server, traffic is distributed.

---

# Why Load Balancing?

- Better availability
- Better resource utilization
- Horizontal scalability
- Fault tolerance
- Reduced load on individual instances

---

# Common Load Balancing Algorithms

### 1. Round Robin

Requests are distributed sequentially.

```
Request 1 → Server 1
Request 2 → Server 2
Request 3 → Server 3
Request 4 → Server 1
```

---

### 2. Least Connections

Send the request to the server with the fewest active connections.

---

### 3. Weighted Round Robin

Servers receive traffic according to assigned weights.

Example:

```
Server 1 → Weight 2
Server 2 → Weight 1
```

Server 1 receives more traffic.

---

# Load Balancer + Spring Boot

You might have:

```
Client
   ↓
Load Balancer
   ↓
┌─────────────┬─────────────┐
↓             ↓
Spring Boot  Spring Boot
Instance 1   Instance 2
```

Both instances run the same application.

---

# Important Cross Question

## What if one Spring Boot instance goes down?

The load balancer can detect that the instance is unhealthy through health checks and stop sending traffic to it, while continuing to route requests to healthy instances.

---

# Important: Stateless Application

Horizontal scaling is easier when application instances are **stateless**.

Instead of storing user session state only in:

```
Server 1 memory
```

you can use approaches such as:

- Stateless JWT authentication
- Shared session storage such as Redis
- Database-backed sessions

This prevents problems when subsequent requests reach a different instance.

---

# Complete Architecture Example

A scalable Spring Boot system could look like:

```
                  Clients
                     |
                     ↓
               Load Balancer
                     |
          ┌──────────┼──────────┐
          ↓          ↓          ↓
       Spring     Spring     Spring
       Boot 1    Boot 2     Boot 3
          |          |          |
          └──────────┼──────────┘
                     ↓
                   Cache
                     |
                     ↓
                  Database
```

For larger systems:

```
Client
  ↓
Load Balancer
  ↓
API Gateway
  ↓
Spring Boot Instances
  ↓
Cache / Message Broker
  ↓
Database
```

---

# ⭐ Interview Rapid-Fire Questions

### Java

1. What are the major features introduced in Java 17?
2. What are the important features of Java 21?
3. What are virtual threads?
4. Are virtual threads faster than normal threads?
5. What is a Singleton?
6. How do you make Singleton thread-safe?
7. Is Spring's Singleton the same as the Singleton design pattern?
8. Abstract class vs interface?
9. What is an immutable class?
10. How do you create an immutable class?
11. Is `final` enough for immutability?
12. What is a stored procedure?

### Spring Boot

13. What is Spring Boot Auto-Configuration?
14. How does `@SpringBootApplication` work?
15. What is `@EnableAutoConfiguration`?
16. How does Spring Boot decide what to auto-configure?
17. What are conditional annotations?
18. What happens if you define your own bean?

### Concurrency

19. What is a race condition?
20. How can you prevent a race condition?
21. `synchronized` vs `Lock`?
22. `AtomicInteger` vs `int`?
23. Does `volatile` make operations atomic?

### System Design

24. What is scalability?
25. Vertical vs horizontal scaling?
26. What is load balancing?
27. What are load-balancing algorithms?
28. Why should a horizontally scaled application be stateless?
29. What happens when one server goes down?
30. How would you scale a Spring Boot application from 100 users to 100,000 users?

---

# ⭐ Most Important for Your Interview

For a **1–2 year Java/Spring Boot developer**, prioritize these first:

```
1. Java 17 vs Java 21
        ↓
2. Abstract Class vs Interface
        ↓
3. Immutable Class
        ↓
4. Singleton + Spring Singleton
        ↓
5. Spring Boot Auto-Configuration
        ↓
6. Race Condition
        ↓
7. synchronized / AtomicInteger
        ↓
8. Scalability
        ↓
9. Load Balancing
        ↓
10. Stored Procedure
```

The most important skill is not memorizing definitions. For each topic, be ready to answer **"Why do we use it?", "How does it work?", "Give a real-world example", and "What are its limitations?"**.