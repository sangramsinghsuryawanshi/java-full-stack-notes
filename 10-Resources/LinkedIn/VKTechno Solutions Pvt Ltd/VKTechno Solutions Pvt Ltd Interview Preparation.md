
# Part 1: Java & OOPs

## 1. Java 8 Features

Java 8 introduced several features that made Java code more concise, readable, and suitable for functional programming.

The most important features for interviews are:

1. Lambda Expressions

Allow us to write implementations of functional interfaces with less code.

```
Runnable r = () -> System.out.println("Hello");
r.run();
```

Syntax: `(parameters) -> expression`

2. Functional Interfaces

An interface with exactly one abstract method. It can have multiple default and static methods.

```
@FunctionalInterface
interface Calculator {
    int add(int a, int b);
}

Calculator c = (a, b) -> a + b;
System.out.println(c.add(10, 20)); // 30
```

3. Stream API

Processes collections using operations such as `filter()`, `map()`, and `collect()`.

```
List<Integer> nums =
    Arrays.asList(1, 2, 3, 4, 5, 6);

List<Integer> result = nums.stream()
    .filter(n -> n % 2 == 0)
    .collect(Collectors.toList());

System.out.println(result); // [2, 4, 6]
```

4. Optional

Helps represent a value that may or may not be present and reduces accidental null handling errors.

```
Optional<String> name =
    Optional.ofNullable(null);

System.out.println(
    name.orElse("Unknown")
); // Unknown
```

5. Date and Time API

Provides modern date/time classes such as `LocalDate`, `LocalTime`, and `LocalDateTime`.

```
LocalDate today = LocalDate.now();
LocalDate tomorrow = today.plusDays(1);
```

6. Default and Static Interface Methods

Interfaces can provide method implementations.

```
interface Payment {
    default void status() {
        System.out.println("Processing");
    }

    static void info() {
        System.out.println("Payment service");
    }
}
```

### Common Java 8 interview questions

- What is a lambda expression?
    
- What is a functional interface?
    
- What is the difference between `map()` and `filter()`?
    
- What is the difference between a Stream and a Collection?
    
- What is the difference between `findFirst()` and `findAny()`?
    
- What is `Optional`, and why do we use it?
    
- What is the difference between intermediate and terminal operations?
    

Interview-ready answer: What is Stream API?

The Stream API was introduced in Java 8 to process collections in a declarative way. It supports operations such as filtering, mapping, sorting, and collecting data. It does not normally modify the original collection, and stream pipelines are generally evaluated when a terminal operation is invoked.

## 2. Class vs Object

A class is a blueprint that defines the properties and behavior of objects. An object is an instance of a class.

![Object Oriented Java. In this, we will discuss Object… | by Gursimar Singh | Medium](https://images.openai.com/static-rsc-4/n3ENFm74vnjyO56FM1BfGEom2tGva2d8XAeCO_TkLmy6j-5ggiaKUv2td9d_7mJAhUjFVyLFPK_w0S2Dx9ruSkFikvOMT1q-W6gqUjFLyVgpyBA8EyW2xi5k9so4k6vqVe47Xl4aJnRCcgkH_ZZ2YTjBjlKWPbEu69LncVl5RJg?purpose=inline)

Class

Defines fields and methods, such as `color`, `start()` and `stop()`.

![sedan cars vehicles transport icons](https://images.openai.com/static-rsc-4/5eTLaPz7fnh0C1Eg35MynVaN9iU6I8syDDWHSzOsa6Q20PCzY_VQEHCr24MeyXQazNGqPmMqXfc3LngEaUGJyGbYC2-aNyCtjCqCG4qj9aGX_H5aZNCECAyoAG0bVuM4WyB38vShd41wi4x72AmCvIqlS5-LQwz0rYGP6PEP9sryWiBXFzPF--wrXBS7TXse?purpose=inline)

Object

A specific instance with its own state, such as a red car or a blue car.

### Java example

```
class Car {
    String color;

    void start() {
        System.out.println(color + " car started");
    }
}

public class Main {
    public static void main(String[] args) {
        Car car1 = new Car();
        car1.color = "Red";

        Car car2 = new Car();
        car2.color = "Blue";

        car1.start();
        car2.start();
    }
}
```

Output:

```
Red car started
Blue car started
```

Here, `Car` is the class, while `car1` and `car2` refer to two distinct objects.

Interview follow-up: What happens when we use `new Car()`?

Java creates a new `Car` object and initializes its instance fields to their default values before running the constructor. The reference to the object is assigned to `car1` or `car2`.

## 3. Four pillars of OOPs

OOP stands for Object-Oriented Programming. It organizes software around objects containing state and behavior.

![Learn how to design a car rental system with dynamic pricing and overbooking edge cases | by DevPatel | Medium](https://images.openai.com/static-rsc-4/KvsNuaDPTg9eh4ktvnjCdz2V0CLp0HZGoD9R6_G2EPcIHdf7zvEHK_YBCDCr6OtOx7kEger7XnpUs5XxXI6waBxjcTUebkfxmxIuR20flaqLTiOudxGT0tGyAd0iWa3ycnPZzkNTEtle86af85Plt_tbNgG9ruHM0eoCjDcgbj0?purpose=inline)

1. Encapsulation

Bundling data and methods into a class and controlling access to internal state.

```
class Employee {
    private double salary;

    public void setSalary(double salary) {
        if (salary >= 0) {
            this.salary = salary;
        }
    }

    public double getSalary() {
        return salary;
    }
}
```

Real-world use: Protecting employee salary or account balance from invalid changes.

![Inheritance in Java. Hi there, are you new to Java? If… | by Mukta Sharma | Medium](https://images.openai.com/static-rsc-4/r4WV6yuxvz8TerDyez0Dxz7x_bTCSVoBW2oI7bwN1usDMUy_XKQ4ndMv8O-Nf2x1qJ1M9yo0DY-fDLLEjntOh0Kg3eluN5m9DYQdsLPnfJGMpK0mrf8EZEJDZFQpA8iC-ksf7MJSQ6ggKaStLMpOOTRO0Jpsn_IQt-AilMq-i-o?purpose=inline)

2. Inheritance

One class inherits accessible members from another class using `extends`.

```
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

Real-world use: Sharing common behavior between related classes.

![Mastering Java Polymorphism: The Ultimate Guide with Code, Benchmarks, and Diagrams | by inside Nikita's Mind | Medium](https://images.openai.com/static-rsc-4/vTiNJj8yCBZY66BYLUuPrUAUguCK4Y7y8-suM9P1TCrhGhWseMym6mcXxL1isEHuVNCVjvq67qr7JsOLWpH5dNY8-JpCPadNu7S2Ho5f6fTYxv0aMBp8xMCUb4U2-ViVFR6dBnyzVhO_uODQRfp3EqkjjYRIP4J-P4-EJd4i_Qg?purpose=inline)

3. Polymorphism

The same method name or interface can produce different behavior.

Method overloading — compile-time polymorphism

```
int add(int a, int b) {
    return a + b;
}

int add(int a, int b, int c) {
    return a + b + c;
}
```

Method overriding — runtime polymorphism

```
class Animal {
    void sound() {
        System.out.println("Animal sound");
    }
}

class Dog extends Animal {
    @Override
    void sound() {
        System.out.println("Bark");
    }
}
```

Real-world use: Different payment methods implementing a common payment operation.

![Belajar Koding dengan Cara yang Menyenangkan — Buat Kamu yang Baru Mulai | Kodingyuk.com](https://images.openai.com/static-rsc-4/AePQBfe8GzSSrjK8O5v6sJ5wwxMWCaQ9MAHot3-H-XbBw-lP3F54VHjHQp4KG7yXf2sD-t-LtfXypM4b_KSlUc-KloxsHH0O00dS3tjg8_HO5lFZxtQqH6vQ45pDFhDwNEunVEJNynKn9t0B_OiZciVrxEJygcsXzQpOxAvHj2w?purpose=inline)

4. Abstraction

Hiding implementation details and exposing essential behavior through interfaces or abstract classes.

```
interface Payment {
    void pay(double amount);
}

class UpiPayment implements Payment {
    public void pay(double amount) {
        System.out.println("UPI payment");
    }
}
```

Real-world use: A service calls `pay()` without needing to know every internal payment-processing step.

### Important comparison: Overloading vs Overriding

|Overloading|Overriding|
|---|---|
|Same method name, different parameter list|Subclass provides a new implementation|
|Usually in the same class|Requires inheritance or interface implementation|
|Resolved at compile time|Instance method dispatch occurs at runtime|
|Return type alone cannot distinguish overloads|Return type must be compatible|

### Encapsulation vs Abstraction

- Encapsulation: Controls access to an object's data and internal state.
    
- Abstraction: Exposes essential operations while hiding implementation details.
    

A common interview mistake is saying both concepts mean exactly the same thing. They are related but different.

## 4. Collections and Core Java concepts

The Java Collections Framework provides interfaces and implementations for storing and processing groups of objects.

### Collections hierarchy

Iterable

Collection

List

ArrayList, LinkedList

Ordered; duplicates allowed

Set

HashSet, TreeSet

Unique elements

Queue

PriorityQueue, Deque

Processing elements

Map — separate interface

HashMap, LinkedHashMap, TreeMap — stores key-value pairs

### List, Set, Map and Queue

|Type|Main purpose|Example|
|---|---|---|
|`ArrayList`|Indexed access and general-purpose lists|Employee names|
|`LinkedList`|List operations and deque functionality|Queue-like processing|
|`HashSet`|Unique elements|Remove duplicate IDs|
|`TreeSet`|Sorted unique elements|Sorted employee IDs|
|`HashMap`|Key-value lookup|Employee ID → Employee|
|`TreeMap`|Keys maintained in sorted order|Sorted ID mapping|
|`PriorityQueue`|Process by priority|Task scheduling|

### Example: HashMap

```
import java.util.HashMap;
import java.util.Map;

public class Main {
    public static void main(String[] args) {
        Map<Integer, String> employees = new HashMap<>();

        employees.put(101, "Rahul");
        employees.put(102, "Amit");
        employees.put(103, "Priya");

        System.out.println(employees.get(102));
        System.out.println(employees.containsKey(101));
    }
}
```

Output:

```
Amit
true
```

How does HashMap work internally?

1. It calculates a hash from the key.
    
2. The hash helps determine the bucket where the entry belongs.
    
3. It uses `equals()` to distinguish keys that may have the same hash.
    
4. Collisions are handled within the bucket, using linked nodes and, under certain conditions, tree structures.
    

Average lookup is generally \(O(1)\), assuming good hash distribution. Worst-case behavior depends on collisions and implementation details.

### Important Core Java concepts

- JDK vs JRE vs JVM: JDK provides development tools; JVM executes bytecode; JRE is the traditional runtime concept containing the JVM and runtime libraries.
    
- `==` vs `equals()`: `==` compares primitive values or object references; `equals()` compares objects according to its implementation.
    
- `String` vs `StringBuilder`: `String` is immutable; `StringBuilder` is mutable and useful for repeated string modifications.
    
- Checked vs unchecked exceptions: Checked exceptions are subject to compile-time handling/declaration requirements; unchecked exceptions are subclasses of `RuntimeException` or `Error`.
    
- `final` keyword: Can prevent reassignment of a variable, overriding of a method, or inheritance from a class.
    
- `static` keyword: Members belong to the class rather than individual instances.
    
- `equals()` and `hashCode()`: Equal objects must have equal hash codes. This is essential for correct behavior in hash-based collections.
    
- Generics: Provide compile-time type safety, such as `List<String>`.
    
- Access modifiers: `private`, package-private, `protected`, and `public` control access.
    
- Exception handling: `try`, `catch`, `finally`, `throw`, and `throws`.
    

# Part 2: Software Development Process

## 5. What is SDLC?

SDLC stands for Software Development Life Cycle. It is a structured process used to plan, develop, test, deploy, and maintain software.

1

Planning

Define objectives, scope, cost and timeline.

2

Requirement Analysis

Understand business and technical requirements.

3

Design

Create architecture, database schema and API design.

4

Development

Implement features and write code.

5

Testing

Verify functionality, integration, performance and security.

6

Deployment

Release the application to the target environment.

7

Maintenance

Fix bugs, monitor systems and improve features.

### Example from a Java backend project

Imagine your team is developing an invoice management feature.

|SDLC phase|Example activity|
|---|---|
|Planning|Estimate time and identify dependencies|
|Requirements|Define invoice creation, validation and retrieval|
|Design|Design REST endpoints, DTOs and database tables|
|Development|Implement Controller → Service → Repository|
|Testing|Test valid invoices, invalid input and database failures|
|Deployment|Deploy the application to a test or production environment|
|Maintenance|Fix defects and monitor API errors|

### Interview-ready answer

“SDLC is a systematic process for developing and maintaining software. Its main phases are planning, requirement analysis, design, development, testing, deployment and maintenance. It helps teams manage project scope, improve quality, reduce risks and deliver software in a controlled way.”

Follow-up: Are SDLC phases always sequential?

No. In Waterfall, phases are largely sequential. In Agile, the team revisits these activities iteratively in smaller increments.

## 6. What is Agile methodology?

Agile is an iterative and incremental approach to software development that emphasizes customer collaboration, working software, feedback, and responding to changing requirements.

Instead of waiting until the entire product is finished, a team delivers smaller increments and improves them using feedback.

### Agile vs Waterfall

![What Is a Systems Development Life Cycle (SDLC)?](https://images.openai.com/static-rsc-4/8gRJ6ObTe9TYm5PDYw_YRsdgaD75YRfPV2ipjzwS2tYQNM1-AJTdichOAIOtdHmkq3C8JMK7akZIzB_7_Byvwxk_6srwOJ5e7Lf8WRLgwGHSaq9id-uljyg5frFFk2JYAzgNeL0SAdExExpHGGndYRCr0gecnF2_1pHewZrq1AI?purpose=inline)

Waterfall

- Mostly sequential phases
    
- Requirements planned early
    
- Changes can be expensive later
    
- Useful when requirements are stable
    

![A Guide to Getting the Most Out of Agile | Onward Developer Toolkit](https://images.openai.com/static-rsc-4/38giqxNaR4Ph_DFB3bKg-OS_sMMB4JLEVFCnTpmkoL5wuKgbQIKKDVs7S6ljiwmv-GGg1akOqipi251Nh1XkhNfYpEwjRbFPL6I29Sp-KokSHjtgeejrhX71GfzhSAs-WcRjYQp0BcfoZN59d56ba9C92JUxJ1cHXiar1hr1ODg?purpose=inline)

Agile

- Iterative development
    
- Frequent feedback
    
- Continuous testing and improvement
    
- Adapts to changing requirements
    

### Common Agile and Scrum terminology

|Term|Meaning|
|---|---|
|Sprint|A fixed-length development period, commonly 1–4 weeks in Scrum|
|Product Backlog|Ordered list of work needed for the product|
|Sprint Backlog|Selected work and the plan for the current sprint|
|Daily Scrum|Short daily event to inspect progress and adapt the plan|
|Sprint Planning|Establish the Sprint Goal and plan the work|
|Sprint Review|Inspect the increment and gather feedback|
|Sprint Retrospective|Identify improvements to the team's process|
|User Story|A short description of a user's need|
|Product Owner|Accountable for maximizing product value and effective Product Backlog management|
|Scrum Master|Accountable for establishing Scrum and improving its effectiveness|
|Definition of Done|Shared criteria for considering work complete|

### Example: Agile workflow for a Spring Boot developer

1. Sprint Planning

The team selects a user story: “As a user, I want to retrieve an invoice by ID.”

2. Development

Implement `GET /api/v1/invoices/{id}` using a controller, service and repository.

3. Testing and Code Review

Validate successful responses, missing invoices, invalid input and database errors. Review the code and run automated tests.

4. Review and Retrospective

Demonstrate the working feature, collect feedback and identify improvements for the next sprint.

### Interview-ready answer

“Agile is an iterative software development approach where teams deliver working software in small increments, collaborate with stakeholders, and adapt to changing requirements. In Scrum, work is organized into sprints. Common events include Sprint Planning, Daily Scrum, Sprint Review and Sprint Retrospective. As a Java developer, I would work on assigned user stories, implement and test features, participate in code reviews, and collaborate with QA and other team members.”

Only describe ceremonies and responsibilities that reflect your actual experience when answering an interviewer.


# Spring / Spring Boot — Interview Notes

Java Backend Developer | 1–2 Years Experience | Concepts + Code + Real-World Examples

These notes cover the Spring Boot questions commonly asked in Java backend interviews. We'll use a practical Invoice Management REST API as our example so you can connect the concepts to real project development.

## 1. What is Spring Boot?

Spring Boot is a framework built on top of the Spring Framework that simplifies the development of standalone, production-ready Java applications.

Traditional Spring applications often required more manual configuration. Spring Boot reduces that work through auto-configuration, starter dependencies, and embedded servers.

For example, you can build a REST API using Spring Boot without manually configuring and deploying an external Tomcat server.

```
@SpringBootApplication
public class InvoiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(
            InvoiceApplication.class, args
        );
    }
}
```

When this application starts, Spring Boot creates and configures the Spring application context, discovers components, configures applicable beans, and starts the embedded web server when web dependencies are present.

### Spring vs Spring Boot

|Spring Framework|Spring Boot|
|---|---|
|Core framework for Java applications|Simplifies applications built with Spring|
|Configuration may require more manual work|Provides convention-based auto-configuration|
|Server setup may be external or embedded|Commonly uses an embedded server|
|Dependencies can be managed individually|Provides starter dependencies|
|Production monitoring needs configuration|Provides Actuator for application monitoring|

Interview answer: Spring Boot simplifies Spring application development by providing auto-configuration, starter dependencies, embedded servers, and production-ready features. It allows developers to focus more on business logic and less on infrastructure configuration.

## 2. Main features of Spring Boot

1. Auto-Configuration

Automatically configures beans based on classpath dependencies, existing beans, and configuration conditions.

Example: With the relevant web dependencies, Spring Boot can configure the web application infrastructure.

2. Starter Dependencies

Convenient dependency bundles for common tasks.

Examples: `spring-boot-starter-webmvc` in newer Spring Boot versions, or `spring-boot-starter-web` in Spring Boot 3.x projects; `spring-boot-starter-data-jpa` for JPA.

3. Embedded Server

Can run an application with an embedded server, such as Tomcat, without requiring separate server deployment.

4. Externalized Configuration

Supports `application.properties`, `application.yml`, environment variables and command-line arguments.

```
server:
  port: 8081

spring:
  application:
    name: invoice-service
```

5. Production-Ready Features

Spring Boot Actuator provides endpoints for health checks, metrics and other operational information.

6. Testing Support

Integrates with Spring Test, JUnit and MockMvc to help test application components and REST endpoints.

Other useful features include logging integration, profiles, executable JAR packaging and external configuration.

## 3. What is an IoC Container?

IoC stands for Inversion of Control. It means that control over creating and managing application objects is given to a framework or container rather than being handled entirely by application code.

In Spring, the IoC container creates and manages objects called beans, resolves their dependencies, and manages their lifecycle.

The two main container interfaces are:

- `BeanFactory` — the fundamental container interface.
    
- `ApplicationContext` — a richer container that builds on `BeanFactory` and provides features such as event publication and integration with other Spring facilities.
    

### Without Spring IoC

```
class InvoiceService {
    private final InvoiceRepository repository =
        new InvoiceRepository();
}
```

`InvoiceService` directly creates its dependency. Replacing that repository with another implementation can require changing the service code.

### With Spring IoC and DI

```
@Repository
class InvoiceRepository {
    public String findInvoice() {
        return "Invoice-101";
    }
}

@Service
class InvoiceService {
    private final InvoiceRepository repository;

    public InvoiceService(InvoiceRepository repository) {
        this.repository = repository;
    }
}
```

Spring manages the repository bean and injects it into the service bean.

Remember the distinction

- IoC is the broader principle of handing control to the framework.
    
- DI (Dependency Injection) is a technique Spring uses to implement IoC.
    
- A Spring bean is an object managed by the Spring container.
    

## 4. What is Dependency Injection (DI), and what are its types?

Dependency Injection is a design technique in which an object receives the objects it depends on from an external source, rather than creating them itself.

Suppose `InvoiceService` needs `InvoiceRepository`. Instead of using `new InvoiceRepository()` inside the service, Spring supplies the repository.

### Three types of dependency injection

1

Constructor Injection — Recommended

Dependencies are provided through the class constructor.

```
@Service
class InvoiceService {
    private final InvoiceRepository repository;

    public InvoiceService(
            InvoiceRepository repository) {
        this.repository = repository;
    }
}
```

Advantages: Dependencies can be `final`, required dependencies are explicit, and unit testing is easier.

2

Setter Injection

Dependencies are supplied through a setter method.

```
@Service
class InvoiceService {
    private InvoiceRepository repository;

    @Autowired
    public void setRepository(
            InvoiceRepository repository) {
        this.repository = repository;
    }
}
```

Use case: Optional dependencies that may be configured after object creation.

3

Field Injection

Spring injects the dependency directly into a field.

```
@Service
class InvoiceService {
    @Autowired
    private InvoiceRepository repository;
}
```

Limitation: Harder to instantiate with dependencies in ordinary unit tests, and dependencies are less explicit. Generally avoid this style in new application code.

### How does Spring inject dependencies?

1. Spring discovers eligible components during application startup.
    
2. It registers bean definitions for those components.
    
3. It creates beans and resolves their required dependencies.
    
4. It supplies the dependencies through constructors, setters or fields.
    
5. The fully configured beans are made available for application use.
    

For a single constructor, `@Autowired` is usually unnecessary when Spring can use that constructor for injection.

### What if two implementations implement the same interface?

For example:

```
interface PaymentService {
    void pay();
}

@Service("upiPayment")
class UpiPaymentService implements PaymentService {
    public void pay() {
        System.out.println("UPI payment");
    }
}

@Service("cardPayment")
class CardPaymentService implements PaymentService {
    public void pay() {
        System.out.println("Card payment");
    }
}
```

Injecting `PaymentService` alone is ambiguous because two beans qualify. You can resolve it using `@Qualifier`:

```
@Service
class CheckoutService {
    private final PaymentService paymentService;

    public CheckoutService(
        @Qualifier("upiPayment")
        PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Alternatively, `@Primary` can mark a preferred bean when no more specific qualifier is provided.

Interview answer: Constructor injection is generally preferred because it makes mandatory dependencies explicit, supports immutability, and improves testability.

## 5. How do dependencies get injected in Spring Boot?

Consider this dependency chain:

Spring IoC Container

Creates and manages beans

InvoiceRepository

Data access component

InvoiceService

Receives the repository through its constructor

InvoiceController

Receives the service through its constructor

Example:

```
@Repository
class InvoiceRepository {
    public String findById(Long id) {
        return "Invoice-" + id;
    }
}

@Service
class InvoiceService {
    private final InvoiceRepository repository;

    public InvoiceService(InvoiceRepository repository) {
        this.repository = repository;
    }

    public String getInvoice(Long id) {
        return repository.findById(id);
    }
}

@RestController
@RequestMapping("/api/invoices")
class InvoiceController {
    private final InvoiceService service;

    public InvoiceController(InvoiceService service) {
        this.service = service;
    }

    @GetMapping("/{id}")
    public String getInvoice(@PathVariable Long id) {
        return service.getInvoice(id);
    }
}
```

When Spring discovers these components, it resolves the dependency graph and supplies the required beans. A request to `/api/invoices/101` can then flow through the controller, service and repository.

Important: In a real application, the repository would commonly be a Spring Data JPA repository and return an entity or DTO rather than a string.

## 6. What is `@SpringBootApplication`?

`@SpringBootApplication` is the main annotation typically used on a Spring Boot application's entry-point class.

It combines three annotations:

### `@SpringBootConfiguration`

Identifies the class as a source of Spring configuration. It is a specialized form of `@Configuration`.

### `@EnableAutoConfiguration`

Enables Spring Boot's conditional auto-configuration based on the classpath, application configuration and existing beans.

### `@ComponentScan`

Enables scanning for eligible components in the configured scan packages.

Example:

```
package com.example.invoice;

@SpringBootApplication
public class InvoiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(
            InvoiceApplication.class, args
        );
    }
}
```

### What happens when `main()` runs?

- `SpringApplication.run()` bootstraps the Spring application.
    
- Spring prepares the application context.
    
- Configuration classes and component scanning are processed.
    
- Auto-configuration applies where its conditions are met.
    
- Beans are created and dependencies are resolved.
    
- For a web application, the embedded server starts.
    

## 7. What is `@ComponentScan`?

`@ComponentScan` tells Spring where to look for classes that should be registered as beans, such as those annotated with `@Component`, `@Service`, `@Repository` and `@Controller`.

Example project structure:

```
com.example.invoice
├── InvoiceApplication.java
├── controller
│   └── InvoiceController.java
├── service
│   └── InvoiceService.java
└── repository
    └── InvoiceRepository.java
```

If `InvoiceApplication` is in `com.example.invoice`, Spring Boot's default scanning typically covers that package and its subpackages.

```
@SpringBootApplication
@ComponentScan(basePackages = "com.example.invoice")
public class InvoiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(
            InvoiceApplication.class, args
        );
    }
}
```

In this example, the explicit `@ComponentScan` is redundant because `@SpringBootApplication` already enables component scanning.

Interview follow-up: What happens if a component is outside the scanned package?

Spring may not discover that component, leading to an error such as `NoSuchBeanDefinitionException` when another bean requires it. You can move the class into the scanned package hierarchy or configure the scan packages appropriately.

## 8. Spring Boot application flow: Frontend to Backend

Suppose a React frontend sends a request to retrieve an invoice. Here's how the request typically travels through a Spring Boot application.

1. Frontend — React / Angular

User clicks “View Invoice”

2. HTTP Request

GET /api/v1/invoices/101

Browser sends the request to the backend

3. DispatcherServlet

Routes the request to the matching controller handler

4. Controller

Receives the request and extracts path variables or request data

5. Service Layer

Executes business logic and validation rules

6. Repository Layer

Uses JPA/Hibernate or SQL to access the database

7. MySQL Database

Returns the requested invoice data

The response travels back through the repository, service and controller to the frontend.

### Example request and response

Request:

```
GET /api/v1/invoices/101
Accept: application/json
```

Response:

```
{
  "id": 101,
  "invoiceNumber": "INV-2026-101",
  "amount": 2500.00,
  "status": "PAID"
}
```

The controller normally returns a DTO, and Spring MVC uses an HTTP message converter (typically Jackson for JSON) to serialize it into a response.

### What happens in each layer?

|Layer|Responsibility|
|---|---|
|Controller|HTTP endpoints, request mapping, response status|
|Service|Business rules and application logic|
|Repository|Database access|
|Entity|Maps domain data to database persistence|
|DTO|Defines the request or response data contract|
|Database|Persists application data|

Interview answer: A frontend sends an HTTP request to the Spring Boot backend. The DispatcherServlet routes it to the appropriate controller, which calls the service layer. The service applies business logic and uses the repository to access the database. The result is returned through the controller as an HTTP response, commonly in JSON format.

## 9. How do you create REST APIs in Spring Boot?

A REST API exposes resources through HTTP endpoints. Spring MVC provides annotations such as `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping` and `@DeleteMapping`.

### Step 1: Add dependencies

For a typical Spring Boot 3.x Maven project, add Spring Web and Validation. If using a relational database, add Spring Data JPA and the relevant database driver.

For example, Spring Boot 3.x commonly uses:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

Use the dependency names appropriate to your Spring Boot version. Newer Spring Boot versions may use the `spring-boot-starter-webmvc` starter for MVC applications.

### Step 2: Create a DTO

```
public record InvoiceResponse(
    Long id,
    String invoiceNumber,
    BigDecimal amount,
    String status
) {}
```

DTOs help define what data is exposed through the API without exposing persistence entities directly.

### Step 3: Create the service

```
@Service
public class InvoiceService {

    public InvoiceResponse getInvoice(Long id) {
        // Demonstration only; replace with database lookup.
        if (id != 101L) {
            throw new ResponseStatusException(
                HttpStatus.NOT_FOUND,
                "Invoice not found"
            );
        }

        return new InvoiceResponse(
            101L,
            "INV-2026-101",
            new BigDecimal("2500.00"),
            "PAID"
        );
    }
}
```

This service uses demonstration data to illustrate the API flow. A production service would retrieve the invoice through a repository.

### Step 4: Create the controller

```
@RestController
@RequestMapping("/api/v1/invoices")
public class InvoiceController {

    private final InvoiceService invoiceService;

    public InvoiceController(InvoiceService invoiceService) {
        this.invoiceService = invoiceService;
    }

    @GetMapping("/{id}")
    public InvoiceResponse getInvoice(
            @PathVariable Long id) {
        return invoiceService.getInvoice(id);
    }
}
```

### Step 5: Run and test the API

Start the application and test the endpoint using Postman or curl.

```
curl -i http://localhost:8080/api/v1/invoices/101
```

Expected result: HTTP `200 OK` and a JSON invoice response.

For an unknown invoice ID, this demonstration returns HTTP `404 Not Found`.

### Important REST annotations

|Annotation|Purpose|
|---|---|
|`@RestController`|Defines a REST controller; return values are written to the response body by default|
|`@RequestMapping`|Defines a common path or request mapping|
|`@GetMapping`|Handles GET requests|
|`@PostMapping`|Handles POST requests|
|`@PutMapping`|Handles PUT requests|
|`@PatchMapping`|Handles PATCH requests|
|`@DeleteMapping`|Handles DELETE requests|
|`@PathVariable`|Extracts values from the URL path|
|`@RequestParam`|Extracts query parameters|
|`@RequestBody`|Binds an HTTP request body to an object|
|`@Valid`|Triggers applicable bean validation|
|`@ResponseStatus`|Specifies an HTTP status for a handler or exception|

### How to create a POST API?

For example, to create an invoice:

```
public record CreateInvoiceRequest(
    @NotBlank String invoiceNumber,
    @NotNull @Positive BigDecimal amount
) {}
```

```
@PostMapping
public ResponseEntity<InvoiceResponse> createInvoice(
        @Valid @RequestBody CreateInvoiceRequest request) {

    InvoiceResponse created =
        invoiceService.createInvoice(request);

    return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(created);
}
```

This controller method assumes a corresponding `createInvoice()` service method exists.

A production implementation should also consider duplicate invoice numbers, authentication and authorization, transaction boundaries, persistence, and consistent error responses.

### REST API interview follow-ups

- GET: Retrieve a resource.
    
- POST: Create a resource or initiate an operation.
    
- PUT: Replace a resource representation; generally intended to be idempotent.
    
- PATCH: Partially update a resource.
    
- DELETE: Remove a resource; generally intended to be idempotent.
    

Also prepare HTTP status codes: `200 OK`, `201 Created`, `204 No Content`, `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found`, `409 Conflict` and `500 Internal Server Error`.

## 10. What is Spring Boot Actuator?

Spring Boot Actuator provides production-oriented features for monitoring and managing a running application. It exposes endpoints that can provide health information, metrics, application details and other operational data.

It is especially useful for checking whether a service is running properly and integrating with monitoring systems.

### Common Actuator endpoints

|Endpoint|Purpose|
|---|---|
|`/actuator/health`|Reports application health|
|`/actuator/info`|Displays configured application information|
|`/actuator/metrics`|Lists available application metrics|
|`/actuator/metrics/{name}`|Shows details for a specific metric|
|`/actuator/prometheus`|Exposes metrics in Prometheus format when the required registry is configured|
|`/actuator/loggers`|Inspects or changes logger levels when appropriately configured and secured|

### Step 1: Add the dependency

For a typical Spring Boot 3.x Maven application:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Use the dependency naming appropriate to your Spring Boot release.

### Step 2: Configure exposure

In `application.properties`:

```
management.endpoints.web.exposure.include=health,info,metrics
```

The default web base path is usually `/actuator`.

### Step 3: Test the health endpoint

Start the application and open:

`http://localhost:8080/actuator/health`

Example response:

```
{
  "status": "UP"
}
```

The exact response depends on the application's health indicators and configuration. Additional components, such as a database, may contribute to the health result.

### How is Actuator used in production?

Imagine an invoice service deployed to a server. A monitoring system can periodically call `/actuator/health` to check service health and collect metrics to investigate performance problems.

Security warning: Do not expose all Actuator endpoints publicly without reviewing access controls. Some endpoints can reveal sensitive information or allow changes to application behavior. Use appropriate authentication, authorization, network restrictions and endpoint exposure settings.

# Java Backend Interview Notes: Database, REST APIs, Docker

Target: Java / Spring Boot Developer — 1–2 years of experience

These notes focus on interview-ready definitions, code examples, practical workflows, and the differences interviewers commonly ask about. We'll use an Invoice Management application with Spring Boot, MySQL and Docker as the running example.

# Part 1: Database Connectivity

## 1. What is JDBC?

JDBC (Java Database Connectivity) is a Java API that allows Java applications to connect to relational databases, execute SQL statements, and process query results.

JDBC provides interfaces and classes such as:

- `DriverManager` — obtains a database connection.
    
- `Connection` — represents a database connection.
    
- `PreparedStatement` — executes parameterized SQL.
    
- `ResultSet` — represents the results of a query.
    
- `SQLException` — represents database access errors.
    

### How JDBC works

Java Application

JDBC API

Database JDBC Driver

MySQL Database

### JDBC example using MySQL

First, add the MySQL Connector/J JDBC driver to your Maven project.

```
<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

In a Spring Boot project, let the Spring Boot parent or BOM manage the compatible driver version.

Example of plain Java JDBC:

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;

public class JdbcExample {
    public static void main(String[] args) throws Exception {

        String url =
            "jdbc:mysql://localhost:3306/invoice_db";

        try (Connection con = DriverManager.getConnection(
                    url, System.getenv("DB_USER"),
                    System.getenv("DB_PASSWORD"));
             PreparedStatement ps =
                    con.prepareStatement(
                        "SELECT id, invoice_number FROM invoices");
             ResultSet rs = ps.executeQuery()) {

            while (rs.next()) {
                System.out.println(
                    rs.getLong("id") + " " +
                    rs.getString("invoice_number")
                );
            }
        }
    }
}
```

This example assumes the database, table, and environment variables already exist.

Important interview point: `PreparedStatement` supports parameterized SQL and helps prevent SQL injection when used correctly. Use try-with-resources to close JDBC resources reliably.

## 2. How do you connect Java / Spring Boot to a database?

In a typical Spring Boot application, you configure a database driver and connection properties. Spring Boot then configures a `DataSource`; Spring Data JPA can use it through Hibernate to perform persistence operations.

### Step 1: Add dependencies

For a Spring Boot 3.x project using Maven:

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

The JPA starter supplies the JPA integration and Hibernate ORM implementation. The MySQL driver enables JDBC connectivity to MySQL.

### Step 2: Configure `application.properties`

```
spring.datasource.url=jdbc:mysql://localhost:3306/invoice_db
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASSWORD}
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false
```

Set `DB_USER` and `DB_PASSWORD` in your environment. Don't commit real database credentials to source control.

The `validate` setting asks Hibernate to validate the schema against the mapped entities rather than automatically creating or updating tables. It assumes the schema has already been created.

### Step 3: Create an entity

```
import jakarta.persistence.*;

@Entity
@Table(name = "invoices")
public class Invoice {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "invoice_number", nullable = false)
    private String invoiceNumber;

    protected Invoice() {}

    public Invoice(String invoiceNumber) {
        this.invoiceNumber = invoiceNumber;
    }

    public Long getId() {
        return id;
    }

    public String getInvoiceNumber() {
        return invoiceNumber;
    }
}
```

An entity represents a persistent domain object mapped to a database table.

### Step 4: Create a repository

```
import org.springframework.data.jpa.repository.JpaRepository;

public interface InvoiceRepository
        extends JpaRepository<Invoice, Long> {
}
```

Spring Data JPA provides common operations such as `save()`, `findById()`, `findAll()` and `deleteById()` through the repository interface.

### Step 5: Use the repository in a service

```
@Service
public class InvoiceService {

    private final InvoiceRepository repository;

    public InvoiceService(InvoiceRepository repository) {
        this.repository = repository;
    }

    public Invoice createInvoice(String invoiceNumber) {
        return repository.save(new Invoice(invoiceNumber));
    }

    public Optional<Invoice> getInvoice(Long id) {
        return repository.findById(id);
    }
}
```

The application can now use the service to save and retrieve invoices.

### Database connection flow

Spring Boot configuration

DataSource + JDBC driver

Hibernate / JPA

Repository → MySQL

Interview answer: I connect Spring Boot to a database by adding the JDBC driver and, if needed, Spring Data JPA dependencies. I configure the connection URL and credentials in application properties or environment variables. Spring Boot configures the `DataSource`, and the application accesses data through JDBC directly or through JPA/Hibernate and repositories.

## 3. JPA vs Hibernate

JPA is a persistence specification; Hibernate is an ORM framework that implements that specification.

JPA is now called Jakarta Persistence in newer Jakarta EE versions. In Spring Boot 3+, imports generally use `jakarta.persistence.*`; older applications may use `javax.persistence.*`.

|Feature|JPA / Jakarta Persistence|Hibernate|
|---|---|---|
|Type|Specification and API|ORM implementation|
|Purpose|Defines persistence standards|Implements ORM behavior|
|Annotations|`@Entity`, `@Id`, `@ManyToOne`|Supports JPA annotations and additional Hibernate-specific features|
|Portability|Code can work with different compliant providers|Hibernate-specific APIs may reduce portability|
|Example|`EntityManager` API|Hibernate ORM engine|

### How they work together

Spring Data JPA Repository

JPA / Jakarta Persistence API

Hibernate ORM — implementation

JDBC → MySQL

For example:

```
@Entity
public class Invoice {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
}
```

`@Entity` and `@Id` are JPA annotations. Hibernate can use them to map the Java object to a database table.

Interview-ready answer: JPA defines the standard API and annotations for object-relational mapping in Java. Hibernate is a popular implementation that maps Java objects to relational database tables and generates SQL to perform database operations. Spring Data JPA further simplifies access through repository interfaces.

# Part 2: REST APIs

## 4. What are REST APIs?

REST stands for Representational State Transfer. It is an architectural style for designing networked applications. A REST API exposes resources through URLs and uses HTTP methods to perform operations on those resources.

For example, an invoice API might expose:

|HTTP method|Endpoint|Purpose|
|---|---|---|
|GET|`/api/v1/invoices`|Retrieve invoices|
|GET|`/api/v1/invoices/101`|Retrieve one invoice|
|POST|`/api/v1/invoices`|Create an invoice|
|PUT|`/api/v1/invoices/101`|Replace an invoice|
|PATCH|`/api/v1/invoices/101`|Partially update an invoice|
|DELETE|`/api/v1/invoices/101`|Delete an invoice|

### Main REST principles

- Client-server separation: The frontend and backend have separate responsibilities.
    
- Stateless requests: Each request contains the information needed to process it; the server doesn't rely on stored conversational client state between requests.
    
- Resource-based URLs: Endpoints represent resources such as invoices and customers.
    
- Uniform interface: Resources are accessed through consistent representations and HTTP semantics.
    
- Cacheability: Responses can specify whether they may be cached.
    
- Layered system: The client may communicate through intermediaries such as gateways and proxies.
    

REST APIs commonly exchange JSON, although REST does not require JSON specifically.

### Example request

```
POST /api/v1/invoices
Content-Type: application/json
```

```
{
  "invoiceNumber": "INV-101",
  "amount": 2500.00
}
```

Possible response:

```
HTTP/1.1 201 Created
Content-Type: application/json
```

```
{
  "id": 101,
  "invoiceNumber": "INV-101",
  "amount": 2500.00
}
```

### What is the difference between REST and RESTful?

REST is the architectural style. An API designed to follow REST principles is commonly described as RESTful.

## 5. How do you create REST APIs in Spring Boot?

The typical implementation uses a controller, service, repository, entity and DTO.

Suggested package structure

com.example.invoice  
├── InvoiceApplication.java  
├── controller/  
│ └── InvoiceController.java  
├── service/  
│ └── InvoiceService.java  
├── repository/  
│ └── InvoiceRepository.java  
├── entity/  
│ └── Invoice.java  
└── dto/  
├── CreateInvoiceRequest.java  
└── InvoiceResponse.java

Here is a simple example that creates and retrieves invoices using an in-memory list. It illustrates REST routing; use a database repository for persistent application data.

### DTO

```
public record CreateInvoiceRequest(
    @NotBlank String invoiceNumber,
    @NotNull @Positive BigDecimal amount
) {}

public record InvoiceResponse(
    Long id,
    String invoiceNumber,
    BigDecimal amount
) {}
```

### Service

```
@Service
public class InvoiceService {

    private final Map<Long, InvoiceResponse> invoices =
        new ConcurrentHashMap<>();

    private final AtomicLong idGenerator = new AtomicLong();

    public InvoiceResponse create(CreateInvoiceRequest request) {
        long id = idGenerator.incrementAndGet();

        InvoiceResponse invoice = new InvoiceResponse(
            id, request.invoiceNumber(), request.amount()
        );

        invoices.put(id, invoice);
        return invoice;
    }

    public InvoiceResponse getById(Long id) {
        InvoiceResponse invoice = invoices.get(id);

        if (invoice == null) {
            throw new ResponseStatusException(
                HttpStatus.NOT_FOUND, "Invoice not found"
            );
        }

        return invoice;
    }
}
```

### Controller

```
@RestController
@RequestMapping("/api/v1/invoices")
public class InvoiceController {

    private final InvoiceService service;

    public InvoiceController(InvoiceService service) {
        this.service = service;
    }

    @PostMapping
    public ResponseEntity<InvoiceResponse> create(
            @Valid @RequestBody CreateInvoiceRequest request) {

        InvoiceResponse created = service.create(request);

        return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(created);
    }

    @GetMapping("/{id}")
    public InvoiceResponse getById(@PathVariable Long id) {
        return service.getById(id);
    }
}
```

This code requires the usual Spring Web and Validation dependencies, plus the necessary Java and Spring imports. The in-memory map is temporary: records disappear when the application restarts, so this isn't a substitute for a production database.

Testing with Postman:

1. Start the application.
    
2. Send a POST request to `http://localhost:8080/api/v1/invoices`.
    
3. Set `Content-Type: application/json`.
    
4. Provide a valid JSON body with an invoice number and positive amount.
    
5. Copy the returned ID and send a GET request to `/api/v1/invoices/{id}`.
    

For production, replace the in-memory map with a JPA repository and add appropriate authentication, authorization, exception handling, logging and automated tests.

# Part 3: Docker & Containerization

## 6. What is Docker?

Docker is a platform for building, packaging, distributing and running applications in containers.

A container packages an application with its required runtime, libraries and configuration so it can run consistently across supported environments.

For a Java developer, Docker is useful for running Spring Boot applications, MySQL databases, Redis, Kafka and other dependencies without installing each service directly on the development machine.

### Why do we use Docker?

- Consistent development and deployment environments.
    
- Easier application packaging and distribution.
    
- Isolation between applications and their dependencies.
    
- Faster environment setup for teams.
    
- Convenient local testing of databases and other services.
    
- Support for scalable deployments and CI/CD pipelines.
    

## 7. What is a Docker Image?

A Docker image is an immutable, versioned template used to create containers.

It contains the application files, runtime dependencies, filesystem layers and metadata needed to run the application.

For example, a Java application image might include:

- A Java 21 runtime.
    
- Your Spring Boot executable JAR.
    
- The command to launch the application.
    
- Required filesystem and configuration metadata.
    

An image is typically built from a `Dockerfile` and can be stored in a container registry.

## 8. What is a Docker Container?

A Docker container is a running or stopped instance of an image, with its own runtime state and isolated processes.

For example, you can create multiple containers from the same Spring Boot image, each configured with a different port or environment.

Docker Image

Java 21 + Spring Boot JAR + startup configuration

Create containers

Container A

Port 8081

Container B

Port 8082

The containers have separate runtime state, but both originate from the same image. Persistent data should be stored in volumes or external services when it needs to survive container replacement.

### Image vs Container

|Docker Image|Docker Container|
|---|---|
|Template used to create containers|Instance created from an image|
|Immutable image layers|Has a writable container layer by default|
|Stored and distributed through registries|Created and run on a host|
|One image can create many containers|Each container has its own runtime state|

## 9. Write a Dockerfile for a Spring Boot application

A `Dockerfile` contains instructions used to build a Docker image.

Here is a practical multi-stage Dockerfile for a Maven-based Spring Boot project using Java 21.

### Step 1: Build the application

Run this from the project root:

```
mvn clean package -DskipTests
```

This generates an executable JAR under `target/`, assuming the project is configured correctly.

### Step 2: Create `Dockerfile`

```

# Stage 1: Build the application
FROM maven:3.9-eclipse-temurin-21 AS build

WORKDIR /app

COPY pom.xml .
COPY src ./src

RUN mvn clean package -DskipTests

# Stage 2: Run the application
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=build /app/target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
```

This is a basic multi-stage example. For reproducible production builds, pin base images to approved versions or digests and ensure the selected Maven image and Java runtime are compatible with the project's dependencies.

### Step 3: Build the Docker image

```
docker build -t invoice-service:1.0 .
```

Here:

- `docker build` builds an image.
    
- `-t invoice-service:1.0` assigns a name and tag.
    
- `.` specifies the build context.
    

### Step 4: Run the container

```
docker run -d \
  --name invoice-app \
  -p 8080:8080 \
  invoice-service:1.0
```

On Windows Command Prompt, use a single line or replace the backslashes with the appropriate Windows line-continuation syntax.

Open `http://localhost:8080` to access the application if an endpoint is mapped there.

### Step 5: Check the container

```
docker ps
docker logs invoice-app
docker stop invoice-app
```

These commands list running containers, display application logs and stop the container.

### Important production notes

- Don't package database credentials into the image.
    
- Inject configuration through environment variables or secrets.
    
- Use a non-root runtime user where practical.
    
- Add a `.dockerignore` file to exclude unnecessary files such as `.git`, local build outputs and secrets.
    
- Don't assume `EXPOSE 8080` publishes the port; the `-p` option publishes it.
    
- If the application needs MySQL, configure it to reach the database through the appropriate network and hostname.
    

## 10. Difference between `ENTRYPOINT` and `CMD`

Both instructions define how a container starts, but they behave differently.

|`ENTRYPOINT`|`CMD`|
|---|---|
|Defines the main executable or startup command|Provides default arguments or a default command|
|Usually stays fixed when runtime arguments are supplied|Default values can be overridden easily|
|Commonly used for the main application process|Commonly used for defaults|

### Example 1: `ENTRYPOINT` only

```
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Running:

```
docker run invoice-service:1.0
```

Executes:

```
java -jar app.jar
```

Additional runtime arguments are appended to the entrypoint.

### Example 2: `CMD` only

```
CMD ["java", "-jar", "app.jar"]
```

Running the image without additional arguments executes the default command. Supplying a command to `docker run` replaces the `CMD`.

### Example 3: Use both together

```
ENTRYPOINT ["java", "-jar", "app.jar"]
CMD ["--spring.profiles.active=dev"]
```

By default, the container runs:

```
java -jar app.jar --spring.profiles.active=dev
```

You can override the default argument:

```
docker run invoice-service:1.0 \
  --spring.profiles.active=prod
```

Now it runs with the `prod` profile instead.

Important: These examples use exec-form JSON arrays, which are generally preferable for application containers because the executable receives signals directly rather than running through an extra shell process.


# Java Backend Interview Notes: Problem Solving, SQL & Scenario-Based Questions

Target: Java Developer / Spring Boot Developer | 1–2 years of experience

These questions test three things: your coding fundamentals, your ability to retrieve data from a database, and how you investigate real production issues. For scenario-based answers, connect your existing Java/Spring experience to the question without claiming technologies or responsibilities you haven't actually used.

# Part 1: Problem Solving

## 1. Find the first non-repeating character in a string

Question: Given a string, find the first character that appears only once.

Example:

```
Input:  "swiss"
Output: "w"
```

The character `s` repeats, while `w` is the first character that occurs only once.

### Approach 1: Using LinkedHashMap

A `LinkedHashMap` preserves insertion order, so we can count each character and then find the first character with a count of one.

```
import java.util.LinkedHashMap;
import java.util.Map;

public class FirstNonRepeating {

    public static void main(String[] args) {
        String str = "swiss";

        Map<Character, Integer> frequency =
                new LinkedHashMap<>();

        for (char ch : str.toCharArray()) {
            frequency.put(
                ch, frequency.getOrDefault(ch, 0) + 1
            );
        }

        for (Map.Entry<Character, Integer> entry
                : frequency.entrySet()) {

            if (entry.getValue() == 1) {
                System.out.println(entry.getKey());
                return;
            }
        }

        System.out.println("No non-repeating character");
    }
}
```

Output:

```
w
```

Complexity

- Time: \(O(n)\) average.
    
- Space: \(O(k)\), where \(k\) is the number of distinct characters.
    

### Approach 2: Using an array

If the input is restricted to ASCII characters, a frequency array is another efficient option.

```
public class FirstUniqueChar {

    public static void main(String[] args) {
        String str = "swiss";
        int[] freq = new int[128];

        for (char ch : str.toCharArray()) {
            freq[ch]++;
        }

        for (char ch : str.toCharArray()) {
            if (freq[ch] == 1) {
                System.out.println(ch);
                return;
            }
        }

        System.out.println("No unique character");
    }
}
```

This solution also runs in \(O(n)\) time, with \(O(1)\) auxiliary space for the fixed-size ASCII frequency array. It assumes the input characters fit within the array's supported range.

Interview tip: Explain why you need two passes: the first calculates frequencies; the second preserves the original string order to identify the first unique character.

# Part 2: SQL Queries

## 2. Find the 1st, 2nd and 3rd highest salary

Assume an `employees` table:

|id|name|salary|
|---|---|---|
|1|Amit|50000|
|2|Priya|80000|
|3|Rahul|70000|
|4|Neha|80000|
|5|Sneha|60000|

The phrase Nth highest salary can mean either the Nth distinct salary or the salary of the Nth employee after sorting. In interviews, clarify which interpretation is required.

### A. Find the highest salary

```
SELECT MAX(salary) AS highest_salary
FROM employees;
```

Result: `80000`

### B. Find the second-highest distinct salary

```
SELECT MAX(salary) AS second_highest_salary
FROM employees
WHERE salary < (
    SELECT MAX(salary)
    FROM employees
);
```

Result: `70000`

This handles duplicate top salaries because it searches for the maximum salary strictly below the highest salary.

### C. Find the third-highest distinct salary

```
SELECT MAX(salary) AS third_highest_salary
FROM employees
WHERE salary < (
    SELECT MAX(salary)
    FROM employees
    WHERE salary < (
        SELECT MAX(salary)
        FROM employees
    )
);
```

Result: `60000`

This nested approach works, although window functions are usually clearer when ranking multiple salaries.

### D. Best approach: `DENSE_RANK()`

```
SELECT salary, salary_rank
FROM (
    SELECT DISTINCT salary,
           DENSE_RANK() OVER (
               ORDER BY salary DESC
           ) AS salary_rank
    FROM employees
) ranked
WHERE salary_rank IN (1, 2, 3)
ORDER BY salary_rank;
```

Expected result:

|salary|salary_rank|
|---|---|
|80000|1|
|70000|2|
|60000|3|

`DENSE_RANK()` assigns the same rank to tied values and doesn't skip ranks. The `DISTINCT` here ensures one output row per salary.

### E. Difference between `ROW_NUMBER()`, `RANK()` and `DENSE_RANK()`

|Function|How ties are handled|Example ranks for salaries 80000, 80000, 70000|
|---|---|---|
|`ROW_NUMBER()`|Every row gets a unique number|1, 2, 3|
|`RANK()`|Ties share rank; next rank is skipped|1, 1, 3|
|`DENSE_RANK()`|Ties share rank; no rank is skipped|1, 1, 2|

If the interviewer asks for the second-highest employee by row position, `ROW_NUMBER()` can be appropriate. If they ask for the second-highest distinct salary, use `DENSE_RANK()` or a distinct-salary query.

### F. Find the employee receiving the second-highest salary

```
SELECT id, name, salary
FROM (
    SELECT id, name, salary,
           DENSE_RANK() OVER (
               ORDER BY salary DESC
           ) AS salary_rank
    FROM employees
) ranked
WHERE salary_rank = 2;
```

This returns every employee earning the second-highest distinct salary.

## 3. What are SQL joins and their types?

A JOIN combines rows from two or more tables using a related column or join condition.

Suppose you have these tables:

employees

|id|name|department_id|
|---|---|---|
|1|Amit|10|
|2|Priya|20|
|3|Rahul|10|
|4|Neha|NULL|

departments

|department_id|department_name|
|---|---|
|10|IT|
|20|HR|
|30|Finance|

### 1. INNER JOIN

Returns only rows that match in both tables.

```
SELECT e.name, d.department_name
FROM employees e
INNER JOIN departments d
    ON e.department_id = d.department_id;
```

Result: Amit–IT, Priya–HR and Rahul–IT.

### 2. LEFT JOIN

Returns all rows from the left table and matching rows from the right. If there is no match, the right-side columns are `NULL`.

```
SELECT e.name, d.department_name
FROM employees e
LEFT JOIN departments d
    ON e.department_id = d.department_id;
```

Neha is included even though she has no department.

### 3. RIGHT JOIN

Returns all rows from the right table and matching rows from the left.

```
SELECT e.name, d.department_name
FROM employees e
RIGHT JOIN departments d
    ON e.department_id = d.department_id;
```

Finance appears even though no employee belongs to it.

### 4. FULL OUTER JOIN

Returns matching rows plus unmatched rows from both tables.

```
SELECT e.name, d.department_name
FROM employees e
FULL OUTER JOIN departments d
    ON e.department_id = d.department_id;
```

MySQL note: MySQL does not support `FULL OUTER JOIN` directly. You can emulate it using `LEFT JOIN` and `RIGHT JOIN` combined with `UNION` or a carefully constructed `UNION ALL` query.

### 5. CROSS JOIN

Returns the Cartesian product: every row from one table paired with every row from the other.

```
SELECT e.name, d.department_name
FROM employees e
CROSS JOIN departments d;
```

With four employees and three departments, this returns 12 combinations.

### 6. SELF JOIN

Joins a table to itself, for example to find each employee's manager.

```
SELECT e.name AS employee,
       m.name AS manager
FROM employees e
LEFT JOIN employees m
    ON e.manager_id = m.id;
```

This assumes the employees table contains a `manager_id` column referencing another employee.

Quick memory trick: `INNER` = matches only; `LEFT` = everything on the left; `RIGHT` = everything on the right; `FULL` = everything from both sides; `CROSS` = all combinations; `SELF` = table joined to itself.

# Part 3: Scenario-Based Interview Questions

## 4. Explain the Razorpay payment gateway flow in your project

This question tests your understanding of payment integration, REST APIs, database transactions and handling failures.

Important: If you haven't personally integrated Razorpay in your project, don't claim that you did. Explain the standard integration flow and distinguish it from your actual project experience.

### Typical Razorpay payment flow

1. Frontend

User clicks Pay Now

2. Spring Boot Backend

Validates the order and calculates the amount server-side

3. Razorpay Orders API

Creates an order using secret credentials stored on the backend

4. Checkout

Frontend opens the Razorpay checkout and the user completes payment

5. Payment Verification

Backend verifies the signature and processes trusted payment status updates

6. Database

Persist the payment/order status and update the business record safely

### Step-by-step explanation

1. The user clicks Pay Now on the frontend.
    
2. The backend validates the requested business transaction and calculates the amount using trusted server-side data.
    
3. The backend creates a Razorpay order and stores the local order/payment record as pending.
    
4. The frontend opens Razorpay Checkout using the order ID returned by the backend.
    
5. After checkout, the frontend sends the returned payment details to the backend.
    
6. The backend verifies the Razorpay payment signature using the correct order ID and secret.
    
7. The backend also processes verified payment status updates, commonly using webhooks, to handle asynchronous outcomes and retries.
    
8. The application updates the order status and allows the business operation only when the payment is confirmed.
    

### Important payment-handling concepts

- Never trust the amount or payment-success status supplied by the frontend alone.
    
- Keep API secrets on the backend, not in frontend code or source control.
    
- Verify payment signatures and webhook signatures as appropriate.
    
- Handle duplicate callbacks and webhook retries idempotently.
    
- Don't assume a checkout success screen means the payment is fully settled.
    
- Use pending, successful, failed and other appropriate payment states.
    
- Reconcile payment records if callbacks are delayed or a network failure occurs.
    

### Interview-ready answer if you have NOT integrated Razorpay yourself

You can use this answer without misrepresenting your experience:

I understand the standard payment gateway integration flow in a Spring Boot application. The backend validates the order and amount, creates an order with the payment gateway, and returns the order details to the frontend. After the customer completes checkout, the backend verifies the payment signature and processes payment status updates, including webhooks. We should also handle duplicate callbacks, failed payments, and network errors safely. I haven't personally implemented Razorpay in my current project, but I understand the integration architecture and the security considerations involved.

If your actual project did not use a payment gateway, be transparent and relate the answer to the real APIs and database workflows you worked on.

## 5. How do you deploy your project?

A good answer explains the overall deployment process, from preparing the application to verifying it after release.

### Typical Spring Boot deployment flow

1

Code and Git

Commit changes and push the code to the repository.

2

Build

Run Maven tests and package the Spring Boot application.

3

Quality Checks

Review test results, dependencies and configuration.

4

Package

Create an executable JAR or Docker image.

5

Deploy

Release to the target server, container platform or cloud environment.

6

Verify

Check application logs, health endpoints, database connectivity and API responses.

### Common Maven commands

Run tests:

```
mvn clean test
```

Package the application:

```
mvn clean package
```

Run the executable JAR:

```
java -jar target/invoice-service.jar
```

The JAR filename depends on your Maven project configuration.

### Interview-ready answer

The deployment process generally involves committing the code to Git, building the application with Maven, running tests, packaging the application, and deploying it to the target environment. After deployment, we verify application startup, review logs, check health endpoints, and test important APIs. The exact deployment steps depend on the project's infrastructure and CI/CD pipeline.

Be prepared for a follow-up: If you haven't personally deployed to production, say so. You can describe the process you understand or have practised without claiming responsibility for a production release.

## 6. During deployment, an error occurs. How do you find it?

Interviewers want to see a structured troubleshooting approach, not random trial and error.

### My troubleshooting checklist

1. Check application logs

Find the first meaningful error, exception type, stack trace, timestamp and root-cause message.

```
docker logs invoice-app
```

2. Check application health

Verify whether the application started successfully and whether its health endpoint responds.

3. Verify configuration and dependencies

Check environment variables, database connectivity, credentials, ports, network settings and required services.

4. Compare with the last working release

Identify recent code, dependency, configuration or infrastructure changes that could explain the failure.

5. Fix and verify

Reproduce the problem where possible, apply a tested fix, redeploy through the approved process, and verify the affected functionality.

### Common deployment errors

|Error|What to investigate|
|---|---|
|Application fails to start|Stack trace, missing bean, invalid configuration|
|Database connection refused|Database availability, hostname, port and network|
|Authentication failure to database|Credentials, permissions and secret configuration|
|Port already in use|Port allocation and running processes|
|`ClassNotFoundException`|Packaging, classpath and missing dependencies|
|`OutOfMemoryError`|Memory usage, container limits, heap configuration|
|API returns HTTP 500|Server logs, exception stack trace and failing code path|
|API returns HTTP 404|URL, request mapping, context path and deployed version|

### Interview-ready answer

If an error occurs during deployment, I first check the application logs and stack trace to identify the root cause. Then I check the application health, configuration, environment variables, database connectivity, and recent code or dependency changes. I try to reproduce the issue, fix it, run the relevant tests, and verify the application after redeployment. If the issue affects production availability, I follow the team's incident process and consider rollback to a known working version.

## 7. How do you debug your application?

Debugging is the process of identifying, understanding and fixing defects in software.

For Java and Spring Boot, a systematic debugging process is more effective than changing code without evidence.

### My debugging workflow

1. Reproduce the issue: Identify the failing API, input, expected result and actual result.
    
2. Read logs: Look for the exception, stack trace, request details and relevant timestamps.
    
3. Use IDE debugging: Set breakpoints and inspect variables and method calls.
    
4. Trace the application layers: Follow Controller → Service → Repository → Database.
    
5. Check external dependencies: Inspect API responses, timeouts, database queries and configuration.
    
6. Fix and test: Add or update tests to prevent the issue from returning.
    

### IntelliJ IDEA debugging example

Suppose an invoice API returns an incorrect amount.

- Set a breakpoint in the controller or service.
    
- Send the same request from Postman.
    
- Step through the service logic.
    
- Inspect the request DTO, calculated amount and database result.
    
- Identify the incorrect calculation or mapping.
    
- Apply the fix and test both valid and invalid cases.
    

Useful debugger controls include:

- Step Over: Execute the current line without entering a called method.
    
- Step Into: Enter a called method.
    
- Step Out: Finish the current method and return to its caller.
    
- Resume: Continue execution until the next breakpoint or completion.
    

### Logging example

```
private static final Logger log =
    LoggerFactory.getLogger(InvoiceService.class);

public InvoiceResponse processInvoice(Long id) {
    log.info("Processing invoice id={}", id);

    // Business logic

    return response;
}
```

Use structured, meaningful logs and avoid recording passwords, access tokens, payment secrets or unnecessary sensitive data.

### Interview-ready answer

I start by reproducing the issue and checking the logs to understand the exception and affected code path. I use the IDE debugger with breakpoints to inspect variables and follow the execution flow across the controller, service and repository layers. If database or external API integration is involved, I verify the queries, responses and configuration. After identifying the root cause, I fix the issue, test the affected scenarios, and verify that the fix doesn't introduce regressions.

# Part 4: Adaptability and Learning New Technologies

These questions evaluate your approach to learning, not just your technical knowledge.

## 8. How quickly can you learn a new technology?

A strong answer shows that you learn systematically and measure progress through practical implementation.

I learn new technologies by first understanding their core concepts and how they fit into the existing application architecture. Then I follow the official documentation, build a small practical example, and apply the knowledge to a realistic use case. I validate my understanding through testing and debugging rather than relying only on theory. The time required depends on the technology's complexity and the project's requirements, but this approach helps me become productive progressively.

## 9. How do you transfer programming concepts between technologies?

The key idea is that many programming principles remain the same even when the language or framework changes.

|Existing knowledge|Transferable concept|
|---|---|
|Java classes and interfaces|Object-oriented design in other languages|
|Spring dependency injection|Dependency management in other frameworks|
|REST API development|HTTP methods, status codes and request/response contracts|
|JDBC / JPA|Database access, transactions and persistence concepts|
|Java exception handling|Error handling in other languages and frameworks|
|Git and testing|Version control and software quality across technology stacks|

I focus on the underlying programming concepts rather than memorizing framework-specific syntax. For example, my understanding of interfaces, dependency injection, REST APIs, database transactions and exception handling gives me a foundation for learning other frameworks. I then study the new technology's conventions and differences, build a small working example, and validate it with tests. This helps me transfer existing knowledge while adapting to the new environment.

## 10. How do you adapt when project requirements change?

When requirements change, I first clarify the expected behavior and understand the impact on existing functionality. I discuss dependencies and potential risks with the team, break the work into manageable tasks, and update the implementation and tests accordingly. I also communicate progress and blockers early. My focus is to meet the revised requirements without compromising code quality, reliability or existing functionality.

## 11. How would you approach a technology you have never used before?

Use a five-step approach:

1. Understand the problem the technology solves and why the project needs it.
    
2. Learn its fundamentals and official recommended practices.
    
3. Build a small proof of concept to validate the core functionality.
    
4. Integrate it into a realistic scenario with appropriate error handling and tests.
    
5. Document what you learned, seek feedback, and improve the implementation.
    

For example, if you need to learn Kafka for asynchronous invoice processing, first understand producers, topics, partitions, consumers and consumer groups. Then build a small producer-consumer application and test retries, duplicate messages and failure handling before using it in a production workflow.