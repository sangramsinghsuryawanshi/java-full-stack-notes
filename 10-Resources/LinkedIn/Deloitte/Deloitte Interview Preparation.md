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
if (obj instanceof String s) 
{
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
public class Employee 
{
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

    public String getName() 
    { return name; }
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

### 1. Virtual Threads

Virtual threads are lightweight threads managed by the JVM. They were introduced as a preview feature in Java 19 and became a permanent feature in Java 21.

### Why do we need virtual threads?

Traditional platform threads are relatively expensive because they are closely associated with operating-system threads. Handling thousands of concurrent blocking tasks can consume substantial resources.

Virtual threads allow Java applications to handle many concurrent tasks with less thread-management overhead.

Traditional platform threads

Java Task 1 → OS Thread 1

Java Task 2 → OS Thread 2

Creating and managing large numbers of threads can be expensive.

Virtual threads

Many virtual threads

Fewer carrier platform threads

The JVM schedules virtual threads onto carrier threads.

### Example

```
public class VirtualThreadExample {
    public static void main(String[] args) {

        Thread.startVirtualThread(() -> {
            System.out.println("Running in a virtual thread");
        });
    }
}
```

You can also create a virtual-thread-per-task executor:

```
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Main {
    public static void main(String[] args) {

        try (ExecutorService executor =
                 Executors.newVirtualThreadPerTaskExecutor()) {

            for (int i = 0; i < 1000; i++) {
                executor.submit(() -> {
                    // Simulate a task
                    System.out.println("Task running");
                });
            }
        }
    }
}
```

### Real-world backend example

Imagine a Spring Boot application receives 10,000 requests, many of which wait for responses from a database or external API.

Virtual threads can reduce the overhead of managing those concurrent, waiting tasks, depending on the application's workload and its libraries.

Important: Virtual threads improve concurrency for blocking I/O workloads. They do not automatically make CPU-intensive calculations faster or eliminate database connection limits.

Interview question: Are virtual threads replacements for all platform threads?

Answer: No. They are especially useful for large numbers of concurrent blocking tasks. Platform threads remain useful for other workloads, including CPU-bound processing.

### 2. Record Patterns

Record patterns allow you to destructure a record into its individual components during pattern matching.

### Before Java 21

```
record Employee(String name, int age) {}

public class Main {
    public static void main(String[] args) {
        Object obj = new Employee("Rahul", 25);

        if (obj instanceof Employee employee) {
            String name = employee.name();
            int age = employee.age();

            System.out.println(name);
            System.out.println(age);
        }
    }
}
```

### Java 21

```
record Employee(String name, int age) {}

public class Main {
    public static void main(String[] args) {
        Object obj = new Employee("Rahul", 25);

        if (obj instanceof Employee(String name, int age)) {
            System.out.println(name);
            System.out.println(age);
        }
    }
}
```

The pattern checks the type and extracts the record components in one step.

### Real-world use

Suppose an API processes different record types, such as customer details, employee details, or payment details. Record patterns can simplify extracting the data you need.

Interview question: What is the main benefit of record patterns?

Answer: They reduce boilerplate by combining type checking with extraction of record components.

### 3. Pattern Matching for `switch`

Java 21 finalized pattern matching for `switch`, which was a preview feature in earlier releases.

It allows `switch` to work with object types and patterns, not just traditional constants.

### Example

```
public class Main {
    static String describe(Object obj) {
        return switch (obj) {
            case Integer i -> "Integer: " + i;
            case String s  -> "String: " + s;
            case Double d  -> "Double: " + d;
            case null      -> "Value is null";
            default        -> "Unknown type";
        };
    }

    public static void main(String[] args) {
        System.out.println(describe(100));
        System.out.println(describe("Java"));
        System.out.println(describe(10.5));
    }
}
```

Output:

```
Integer: 100
String: Java
Double: 10.5
```

### Why is it useful?

- Reduces repetitive `instanceof` checks and casting.
    
- Makes type-based branching easier to read.
    
- Supports more expressive handling of different object types.
    

Interview question: What is the difference between traditional `switch` and pattern matching for `switch`?

Answer: Traditional `switch` commonly matches values such as constants, whereas pattern matching can match an object's type and bind its components or value to a variable.

### 4. Sequenced Collections

Java 21 introduced interfaces for collections with a defined encounter order and consistent operations at both ends.

The main interfaces are:

- `SequencedCollection`
    
- `SequencedSet`
    
- `SequencedMap`
    

They provide operations such as `getFirst()`, `getLast()`, `addFirst()`, `addLast()`, `removeFirst()`, `removeLast()`, and `reversed()`, where supported by the interface.

### Example

```
import java.util.ArrayList;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        List<String> names =
            new ArrayList<>(List.of("Amit", "Rahul", "Priya"));

        System.out.println(names.getFirst()); // Amit
        System.out.println(names.getLast());  // Priya

        names.addFirst("Neha");
        names.addLast("Vikas");

        System.out.println(names);
        System.out.println(names.reversed());
    }
}
```

Output:

```
Amit
Priya
[Neha, Amit, Rahul, Priya, Vikas]
[Vikas, Priya, Rahul, Amit, Neha]
```

### Real-world use

Consider a task-processing application that needs to inspect or modify the first and last items of an ordered collection. Sequenced collection methods provide a consistent API for these operations.

Interview question: Why were sequenced collections introduced?

Answer: To provide a uniform API for accessing, adding, removing, and reversing elements at the beginning and end of ordered collections.

### 5. String Templates — Preview Feature

String templates were introduced as a preview feature in Java 21. They were not a permanent feature in that release.

They were intended to make string interpolation more readable and provide a mechanism for processing embedded expressions.

A Java 21 preview example looked like this:

```
String name = "Rahul";
int age = 25;

String message = STR."Name: \{name}, Age: \{age}";

System.out.println(message);
```

To compile and run preview features in Java 21, you need to enable preview support, for example:

```
javac --release 21 --enable-preview Main.java
java --enable-preview Main
```

Important interview point: Do not confuse string templates with text blocks. Text blocks are a permanent feature since Java 15; string templates were preview-only in Java 21 and were subsequently withdrawn from the language design. They are not a permanent Java 21 feature.

### 6. Generational ZGC

ZGC is a low-latency garbage collector. Java 21 introduced Generational ZGC, which separates objects into young and old generations.

### Why is this useful?

Most objects in many applications become eligible for garbage collection quickly. Generational collection allows the garbage collector to treat short-lived and long-lived objects differently.

### Young generation

Recently created, often short-lived objects

### Old generation

Objects that survive and remain in use longer

### Real-world use

For a high-throughput Java backend, reducing garbage-collection overhead and maintaining low pause times can help improve latency consistency.

Interview question: Does Generational ZGC eliminate garbage collection pauses entirely?

Answer: No. ZGC aims for very low pause times, but it does not guarantee zero pauses or eliminate all garbage-collection overhead.

### 7. Pattern-Matching Improvements

Java 21 brought together several improvements to pattern matching, including finalized pattern matching for `switch` and record patterns.

Consider nested records:

```
record Address(String city) {}
record Employee(String name, Address address) {}

public class Main {
    static void printCity(Object obj) {
        if (obj instanceof Employee(
                String name,
                Address(String city))) {
            System.out.println(name + " lives in " + city);
        }
    }

    public static void main(String[] args) {
        printCity(new Employee(
            "Rahul",
            new Address("Pune")
        ));
    }
}
```

Output:

```
Rahul lives in Pune
```

This example combines record patterns with nested component extraction, avoiding multiple accessor calls and temporary variables.

## Quick revision table

|Feature|Main benefit|
|---|---|
|Virtual Threads|Lightweight concurrency for many tasks|
|Record Patterns|Extract record components during pattern matching|
|Pattern Matching for `switch`|Cleaner type-based branching|
|Sequenced Collections|Consistent first/last/reversed operations|
|String Templates|Preview string interpolation feature in Java 21|
|Generational ZGC|Low-latency garbage collection with generations|
|Pattern-matching improvements|Less boilerplate when processing different object types|

### Top 8 Java 21 interview questions

1. Is Java 21 an LTS release?
    
2. What are virtual threads, and how do they differ from platform threads?
    
3. Do virtual threads make CPU-intensive tasks faster?
    
4. What are record patterns?
    
5. What is pattern matching for `switch`?
    
6. What problem do sequenced collections solve?
    
7. Were string templates finalized in Java 21?
    
8. What is Generational ZGC?
    

For your Java backend interviews, be ready to explain each feature, write a short code example for virtual threads and pattern matching, and describe when the feature is useful in a Spring Boot application.

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

| Abstract Class                   | Interface                                   |
| -------------------------------- | ------------------------------------------- |
| Uses `abstract class`            | Uses `interface`                            |
| Can have instance fields         | Fields are implicitly `public static final` |
| Can have constructors            | Cannot have constructors                    |
| Can have concrete methods        | Can have default/static methods             |
| A class extends one class        | A class can implement multiple interfaces   |
| Useful for shared state/behavior | Useful for contracts/capabilities           |

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
# Kafka & Microservices — Interview Notes for Java Backend Developers

For a Java Backend Developer with around 2 years of experience, you should understand not only what Kafka is, but also why we use it, how it scales, how consumer groups work, and how to handle failures and reprocess messages in a Spring Boot application.

## 1. Why do we use Kafka?

Apache Kafka is a distributed event-streaming platform used to publish, store, and consume events between applications.

In microservices, Kafka helps services communicate asynchronously without requiring the producer to wait for the consumer to finish processing.

### Real-world example: E-commerce order processing

Imagine a customer places an order in an online shopping application.

Order Service

Creates the order

Kafka — `order-created` topic

Stores and distributes the event

Payment Service

Processes payment

Notification Service

Sends confirmation

Each service can consume the same event independently.

Without Kafka, the Order Service might call the Payment and Notification services synchronously using REST APIs. If the Notification Service is slow or unavailable, the order request may also be delayed.

With Kafka:

1. The Order Service publishes an `OrderCreated` event.
    
2. Kafka stores the event in a topic.
    
3. The Payment Service consumes and processes it.
    
4. The Notification Service independently consumes and processes it.
    
5. The Order Service does not have to wait for both services to finish.
    

### Main reasons for using Kafka

- Loose coupling: Services communicate through events instead of directly depending on one another.
    
- Asynchronous processing: The producer can continue without waiting for downstream processing.
    
- Scalability: Multiple consumers can process partitions in parallel.
    
- Durability: Events can be retained and read again, subject to topic retention and configuration.
    
- Failure recovery: Consumers can resume from stored offsets and reprocess retained events.
    
- Event-driven architecture: One event can trigger multiple independent business processes.
    

Interview answer: “We use Kafka for asynchronous, reliable event communication between microservices. It decouples services, supports high-throughput event processing, and allows consumers to scale independently.”

## 2. How does Kafka help with scalability?

Kafka supports scalability through two important mechanisms: partitioning and consumer groups.

### A. Partitioning

A Kafka topic is divided into partitions. Each partition is an ordered sequence of records.

For example, an `orders` topic could have three partitions:

Topic: `orders`

Partition 0

Order 101

Order 104

Partition 1

Order 102

Order 105

Partition 2

Order 103

Order 106

Different partitions can be processed concurrently while preserving order within each partition.

If all records were processed by a single consumer, processing could become a bottleneck. Partitions allow multiple consumers in the same group to work concurrently.

### B. Consumer groups

Suppose your topic has three partitions and your consumer group has three consumer instances.

Kafka topic: 3 partitions

Consumer 1

Partition 0

Consumer 2

Partition 1

Consumer 3

Partition 2

Three consumers can process three partitions concurrently.

If traffic increases, you can increase the number of partitions where appropriate and add consumer instances.

Important limitations:

- Within a consumer group, a partition is assigned to at most one consumer at a time.
    
- If a topic has three partitions, adding ten consumers to the same group does not give you ten-way partition-level parallelism; some consumers will be idle.
    
- Increasing partitions does not automatically improve throughput if the database, external API, or consumer code is the bottleneck.
    
- Kafka preserves ordering within a partition, not across all partitions.
    

### Interview question: Can we scale Kafka consumers horizontally?

Answer: Yes. We can run multiple instances with the same `group.id`. Kafka distributes the topic's partitions among those instances. Parallelism is limited by the number of partitions and by the capacity of the downstream systems.

## 3. Consumer groups and parallel processing

A consumer group is a set of consumers that cooperate to consume a topic. Each group maintains its own committed offsets.

Suppose both Payment and Notification services subscribe to the `order-created` topic.

`order-created` topic

Group A

Payment Service

Processes payment events

Group B

Notification Service

Sends notifications

Both groups receive the event independently. Consumers inside each group share the work.

### Two rules to remember

Rule 1: Different consumer groups

Each group can independently read the same event. This is how separate microservices react to the same business event.

Rule 2: Same consumer group

Consumers divide the partitions among themselves. Ordinarily, one record is delivered to one consumer in that group, rather than being broadcast to every consumer.

### Example configuration in Spring Boot

```
spring:
  kafka:
    consumer:
      group-id: payment-service-group
      auto-offset-reset: earliest
```

And a listener:

```
@Component
public class OrderConsumer {

    @KafkaListener(
        topics = "order-created",
        groupId = "payment-service-group"
    )
    public void consume(String message) {
        System.out.println("Received: " + message);
    }
}
```

For parallel consumption, run multiple instances of this consumer with the same group ID and ensure that the topic has enough partitions.

`auto-offset-reset: earliest` applies when a group has no valid committed offset (or its offset is unavailable). It does not automatically rewind a group that already has valid committed offsets.

## 4. Handling failures and message reprocessing

This is one of the most important Kafka interview topics.

Failures can happen when:

- The consumer application crashes.
    
- The database is temporarily unavailable.
    
- A message contains invalid data.
    
- An external payment or notification API times out.
    
- The consumer processes a message but fails before committing its offset.
    

A robust consumer needs retry handling, appropriate offset management, dead-letter handling, and idempotent business operations.

### Typical failure-handling flow

1. Consume message

2. Process message

3. Did processing succeed?

Yes

Commit offset after successful processing

No

Retry, then send to a dead-letter topic if needed

### A. Retry transient failures

Examples of transient failures include a temporary database outage or a network timeout.

Retry with a bounded number of attempts and backoff delays, for example:

- First attempt: immediate.
    
- Second attempt: after 1 second.
    
- Third attempt: after 2 seconds.
    
- After the retry limit: route the message to a dead-letter topic (DLT/DLQ), depending on the application's error-handling configuration.
    

Do not retry permanent failures indefinitely. Invalid payloads or unsupported event types may require immediate rejection or dead-letter handling.

### B. Dead-letter topic

A dead-letter topic stores messages that cannot be processed successfully after the configured attempts or because they are not recoverable.

For example:

- Main topic: `order-created`
    
- Dead-letter topic: `order-created-dlt`
    

Store useful diagnostic metadata, such as the original topic, exception details, timestamp, and attempt count, while avoiding unnecessary sensitive data.

An engineer can investigate the message, correct the underlying issue, and replay it through a controlled process.

### C. Offset management

An offset identifies a record's position in a Kafka partition.

If a consumer commits its offset before the business operation completes and then crashes, the message might not be processed successfully again by that consumer group.

If it processes the business operation successfully but crashes before committing the offset, Kafka may deliver the message again.

This is why duplicate processing is possible, and why committing offsets alone does not guarantee exactly-once business effects.

### D. Idempotency

Idempotency means that processing the same event more than once does not produce duplicate business effects.

For example, a payment service should not charge a customer twice just because the same event was delivered again.

A common implementation is to give each event a unique `eventId` and record processed IDs in the database. The deduplication record and business update should be committed atomically in the same database transaction.

```
@Transactional
public void processPayment(OrderCreatedEvent event) {

    if (processedEventRepository.existsById(event.eventId())) {
        return; // Already processed
    }

    paymentService.createPayment(event);

    processedEventRepository.save(
        new ProcessedEvent(event.eventId())
    );
}
```

This illustrates the idea; production code must handle concurrent duplicate deliveries correctly, usually with a database uniqueness constraint and suitable transaction/error handling. If processing also calls an external payment provider, use its idempotency mechanism where available.

### E. Reprocessing a message

There are several approaches:

1. Retry the message: Attempt processing again after a transient failure.
    
2. Replay from an offset: Reset a consumer group's offsets or use a separate group to read retained records again.
    
3. Replay from a dead-letter topic: Correct the cause, then republish or route the failed event through a controlled replay process.
    

Reprocessing requires idempotent handlers because previously successful messages may be encountered again.

### Interview question: Does Kafka guarantee exactly-once processing?

Answer: Kafka supports transactions and exactly-once processing for specific Kafka-to-Kafka workflows. However, exactly-once business effects across Kafka, a relational database, and an external API are not automatic. Those workflows generally need idempotency, transaction coordination, or patterns such as the transactional outbox.

## 5. At-least-once vs at-most-once vs exactly-once

|Delivery behavior|Meaning|Main risk|
|---|---|---|
|At-most-once|A message is delivered zero or one time|Message loss|
|At-least-once|A message is delivered one or more times|Duplicates|
|Exactly-once|Processing effects occur once within a defined transactional scope|Requires the scope and boundaries to be understood|

For many Spring Boot business consumers, at-least-once delivery plus idempotent processing is a practical design.

## 6. Quick interview practice

## Test yourself

8 of 8 answers shown

1. Why use Kafka instead of synchronous REST calls?

Kafka enables asynchronous communication, buffers events, decouples services, and allows independent consumer scaling. REST remains useful when an immediate response is required.

2. How do consumer groups improve scalability?

Consumers in the same group share topic partitions. Multiple partitions can be processed concurrently by different consumers.

3. Can two consumers in the same group process the same partition concurrently?

Normally, one partition is assigned to at most one consumer in a group at a time. The consumer can still process records concurrently in custom designs, but ordering and offset safety then need careful handling.

4. What happens if a consumer crashes?

Kafka can reassign its partitions to another group member. Records may be delivered again depending on processing progress and committed offsets.

5. What is a dead-letter topic?

A topic used to isolate messages that could not be processed successfully after the configured handling attempts, so they can be investigated and replayed.

6. How do you prevent duplicate payment processing?

Use a unique event or business idempotency key, enforce uniqueness in persistent storage, and make business updates transactionally safe.

7. What limits Kafka consumer parallelism?

The number of partitions limits the number of consumers in a group that can actively own separate partitions at the same time.

8. What is the difference between retry and replay?

Retry attempts a failed message again as part of error handling. Replay deliberately reads or republishes previously retained messages, often for recovery or backfill.