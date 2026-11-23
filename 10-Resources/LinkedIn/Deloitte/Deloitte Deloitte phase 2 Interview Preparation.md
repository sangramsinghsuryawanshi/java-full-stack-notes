## **1. What is class metadata in the context of Java class loading?**

**Class metadata** is the information JVM maintains about a loaded class. It describes the class's structure and behavior, rather than storing the values of every object created from it.

When a class is loaded, the JVM creates a runtime representation of that class.

Class metadata includes:

- Class name and package information.
    
- Parent class and implemented interfaces.
    
- Fields and their types.
    
- Methods and constructors.
    
- Access modifiers.
    
- Runtime constant pool.
    
- Annotation and generic-related metadata.
    
- Method and field information needed by the JVM.
    

**Where is it stored?**

In modern HotSpot JVMs, class metadata is primarily stored in **Metaspace**, which uses native memory. Static variables themselves are associated with the class but are not simply all stored as class metadata in Metaspace.

**Class loading process:**

**Loading**

JVM locates the .class file and creates the class representation.

**Linking**

Verification → Preparation → Resolution

**Initialization**

Static initializers and static field initializations execute.

**Example:**

```
class Employee {
    private int id;
    private String name;

    public void display() {
        System.out.println(name);
    }
}
```

The JVM maintains metadata describing `Employee`, including its fields and `display()` method. Objects created from `Employee` hold their own instance field values.

**Interview answer:** Class metadata is the runtime information maintained by the JVM about a class, including its fields, methods, superclass, interfaces, and other structural details. In HotSpot JVM, much of this metadata is stored in Metaspace.

## **2. What happens when we try to reuse a Stream?**

Java Streams are **single-use**. Once a terminal operation consumes a stream, it cannot be reused.

**Example:**

```
List<Integer> numbers = List.of(10, 20, 30, 40);

Stream<Integer> stream = numbers.stream();

stream.forEach(System.out::println);

// This throws IllegalStateException
stream.count();
```

Exception:

```
java.lang.IllegalStateException:
stream has already been operated upon or closed
```

**Why?**

A Stream represents a pipeline of operations over a data source. Once the terminal operation executes, the pipeline is consumed or closed.

**Correct approach:**

Create a new stream whenever you need another operation.

```
List<Integer> numbers = List.of(10, 20, 30, 40);

long count = numbers.stream().count();

int sum = numbers.stream()
        .mapToInt(Integer::intValue)
        .sum();

System.out.println(count);
System.out.println(sum);
```

**Interview answer:** Java Streams cannot be reused after a terminal operation. Reusing them results in `IllegalStateException`. We should create a new stream from the original collection for each independent pipeline.

## **3. How can null be handled in TreeSet?**

A `TreeSet` stores elements in sorted order using natural ordering or a supplied `Comparator`.

A `TreeSet` using natural ordering does not allow `null`, because it cannot compare null with other elements.

```
TreeSet<Integer> set = new TreeSet<>();

set.add(10);
set.add(20);

// Throws NullPointerException
set.add(null);
```

**How can we support null?**

Provide a null-safe comparator.

```
TreeSet<Integer> set = new TreeSet<>(
        Comparator.nullsFirst(Comparator.naturalOrder())
);

set.add(20);
set.add(10);
set.add(null);

System.out.println(set);
```

Output:

```
[null, 10, 20]
```

For null at the end:

```
TreeSet<Integer> set = new TreeSet<>(
        Comparator.nullsLast(Comparator.naturalOrder())
);
```

**Important:** The comparator must consistently handle null values. Without that, insertion or comparison may throw `NullPointerException`.

**Interview answer:** A TreeSet using natural ordering does not support null because comparison requires a non-null value. We can handle null by providing `Comparator.nullsFirst()` or `Comparator.nullsLast()`.

## **4. How does IdentityHashMap differ from HashMap?**

Both are implementations of the `Map` interface, but they compare keys differently.

|Feature|HashMap|IdentityHashMap|
|---|---|---|
|Key comparison|`equals()`|`==`|
|Hash calculation|`hashCode()`|`System.identityHashCode()`|
|Logical equality|Yes|No, object identity|
|Null key|One allowed|One allowed|
|Typical use|General-purpose mapping|Object identity-based tracking|

**Example:**

```
String s1 = new String("Java");
String s2 = new String("Java");

System.out.println(s1 == s2);      // false
System.out.println(s1.equals(s2)); // true
```

Using HashMap:

```
Map<String, Integer> map = new HashMap<>();

map.put(s1, 100);
map.put(s2, 200);

System.out.println(map.size()); // 1
```

Because both strings are logically equal.

Using IdentityHashMap:

```
Map<String, Integer> map = new IdentityHashMap<>();

map.put(s1, 100);
map.put(s2, 200);

System.out.println(map.size()); // 2
```

Because they are two different objects in memory.

**Real-world use cases:**

- Object graph traversal.
    
- Tracking object references.
    
- Serialization frameworks.
    
- Detecting cycles based on object identity.
    

**Interview answer:** HashMap compares keys using `equals()` and `hashCode()`, whereas IdentityHashMap compares keys using reference equality (`==`). IdentityHashMap is useful when two objects with identical values must still be treated as separate objects.

## **5. What is a cache map? What are the benefits of using Streams?**

### **A. Cache map**

A **cache map** is a map-like data structure used to temporarily store frequently accessed data so that the application can retrieve it faster instead of repeatedly fetching it from a database or another service.

A basic example using `HashMap`:

```
Map<Integer, String> cache = new HashMap<>();

cache.put(101, "Rahul");
cache.put(102, "Amit");

String name = cache.get(101);

System.out.println(name); // Rahul
```

However, a normal `HashMap` is not a complete production caching solution. It does not automatically provide expiration, eviction policies, or thread safety.

**Common caching solutions:**

|Cache|Purpose|
|---|---|
|HashMap|Basic in-memory storage|
|ConcurrentHashMap|Thread-safe in-memory map|
|Caffeine|High-performance Java caching library|
|Redis|Distributed cache shared across application instances|
|Ehcache|Java caching provider|

**Benefits:**

- Reduces repeated database queries.
    
- Improves response time.
    
- Reduces load on downstream services.
    
- Can improve application scalability.
    

**Important cache concepts:**

- TTL: Time To Live; how long an entry remains valid.
    
- Eviction: Removing entries when the cache needs space or entries expire.
    
- Cache invalidation: Removing or updating stale data.
    
- Cache hit: Requested data is found in the cache.
    
- Cache miss: Requested data is not found in the cache.
    

Example with Caffeine:

```
Cache<Integer, String> cache = Caffeine.newBuilder()
        .maximumSize(1000)
        .expireAfterWrite(Duration.ofMinutes(10))
        .build();

cache.put(101, "Rahul");

String name = cache.getIfPresent(101);
```

### **B. Benefits of Java Streams**

Java Streams, introduced in Java 8, help process collections in a declarative and readable way.

**Benefits:**

1. Less boilerplate code.
    
2. Easy filtering, mapping, sorting and grouping.
    
3. Supports functional-style programming.
    
4. Supports method chaining.
    
5. Supports parallel processing where appropriate.
    
6. Does not require modifying the original collection.
    

**Example without Streams:**

```
List<Integer> numbers = List.of(10, 15, 20, 25, 30);

List<Integer> result = new ArrayList<>();

for (Integer number : numbers) {
    if (number > 20) {
        result.add(number);
    }
}
```

**Using Streams:**

```
List<Integer> result = numbers.stream()
        .filter(n -> n > 20)
        .toList();

System.out.println(result);
```

Other useful operations:

|Operation|Purpose|
|---|---|
|`filter()`|Select elements based on a condition|
|`map()`|Transform elements|
|`sorted()`|Sort elements|
|`distinct()`|Remove duplicates|
|`collect()`|Collect results|
|`reduce()`|Combine elements into one result|
|`forEach()`|Perform an action|
|`flatMap()`|Flatten nested structures|

**Interview answer:** Streams simplify collection processing using operations such as filter, map, sorted and collect. They improve readability and support functional programming. Parallel streams are also available, but should be used only when the workload benefits from parallel execution.

## **6. What is the life cycle of a Spring Bean?**

A Spring Bean is an object that is created, configured and managed by the Spring IoC container.

**Bean lifecycle:**

**1**

**1. Instantiation**

Spring creates the bean object.

**2**

**2. Dependency injection**

Spring injects required dependencies.

**3**

**3. Aware callbacks**

Applicable Aware interfaces are invoked.

**4**

**4. Before initialization**

BeanPostProcessor.postProcessBeforeInitialization().

**5**

**5. Initialization**

@PostConstruct, InitializingBean.afterPropertiesSet(), custom init method.

**6**

**6. After initialization**

BeanPostProcessor.postProcessAfterInitialization(); proxies may be created.

**7**

**7. Bean is ready**

The application can use the bean.

**8**

**8. Destruction**

@PreDestroy, DisposableBean.destroy(), custom destroy method.

**Example:**

```
@Component
public class EmployeeService {

    public EmployeeService() {
        System.out.println("Constructor called");
    }

    @PostConstruct
    public void init() {
        System.out.println("Bean initialized");
    }

    @PreDestroy
    public void destroy() {
        System.out.println("Bean destroyed");
    }
}
```

**Important annotations and interfaces:**

|Mechanism|Purpose|
|---|---|
|`@PostConstruct`|Runs after dependency injection|
|`InitializingBean`|Provides `afterPropertiesSet()`|
|`@PreDestroy`|Runs before bean destruction|
|`DisposableBean`|Provides `destroy()`|
|`BeanPostProcessor`|Allows processing before and after initialization|

Note: For prototype-scoped beans, Spring does not manage the complete destruction lifecycle in the same way it does for singleton beans.

**Interview answer:** The Spring Bean lifecycle starts with instantiation, followed by dependency injection, initialization callbacks and post-processing. The bean then becomes available for use. During container shutdown, destruction callbacks are invoked for managed singleton beans.

## **7. How do you build a REST API in Spring Boot?**

A REST API allows clients such as web applications, mobile applications or other services to communicate with the backend over HTTP.

**Steps:**

1. Create a Spring Boot project.
    
2. Add Spring Web dependency.
    
3. Create an entity.
    
4. Create a repository.
    
5. Create a service layer.
    
6. Create a REST controller.
    
7. Add validation and exception handling.
    
8. Test endpoints using Postman.
    

**Typical architecture:**

**Client / Postman**

**Controller**

Receives HTTP requests

**Service**

Business logic

**Repository**

Database operations using JPA

**MySQL Database**

**Example: Employee REST API**

Entity:

```
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private String email;

    // Getters and setters
}
```

Repository:

```
public interface EmployeeRepository
        extends JpaRepository<Employee, Long> {
}
```

Service:

```
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(EmployeeRepository repository) {
        this.repository = repository;
    }

    public Employee save(Employee employee) {
        return repository.save(employee);
    }

    public List<Employee> getAll() {
        return repository.findAll();
    }
}
```

Controller:

```
@RestController
@RequestMapping("/api/employees")
public class EmployeeController {

    private final EmployeeService service;

    public EmployeeController(EmployeeService service) {
        this.service = service;
    }

    @PostMapping
    public ResponseEntity<Employee> create(
            @RequestBody Employee employee) {

        Employee saved = service.save(employee);

        return ResponseEntity
                .status(HttpStatus.CREATED)
                .body(saved);
    }

    @GetMapping
    public ResponseEntity<List<Employee>> getAll() {
        return ResponseEntity.ok(service.getAll());
    }
}
```

**Example endpoints:**

|Method|Endpoint|Purpose|
|---|---|---|
|POST|`/api/employees`|Create employee|
|GET|`/api/employees`|Get all employees|
|GET|`/api/employees/1`|Get employee by ID|
|PUT|`/api/employees/1`|Replace/update employee|
|PATCH|`/api/employees/1`|Partially update employee|
|DELETE|`/api/employees/1`|Delete employee|

For production applications, prefer request/response DTOs instead of exposing JPA entities directly. Add validation, centralized exception handling, authorization, pagination and API documentation as needed.

**Interview answer:** I build REST APIs using Spring Boot with a layered architecture. The controller handles HTTP requests, the service layer contains business logic, the repository handles database operations, and DTOs help manage request and response data. I use Postman to test endpoints and implement validation and exception handling.

## **8. What is the difference between POST and PUT?**

Both are HTTP methods used to send data to a server, but they have different semantics.

|Feature|POST|PUT|
|---|---|---|
|Main purpose|Create a resource or trigger processing|Create or replace a resource at a known URI|
|Idempotent|No, by default|Yes|
|Resource URL|Often assigned by server|Usually specified by client|
|Repeated identical request|May create multiple resources|Should leave the resource in the same final state|
|Typical use|Create employee|Replace employee with ID 101|

**POST example:**

```
POST /api/employees
Content-Type: application/json

{
  "name": "Rahul",
  "email": "rahul@example.com"
}
```

The server may generate ID `101` and return:

```
201 Created
Location: /api/employees/101
```

Calling the same POST again may create another employee.

**PUT example:**

```
PUT /api/employees/101
Content-Type: application/json

{
  "name": "Rahul Sharma",
  "email": "rahul@example.com"
}
```

PUT replaces the representation of the resource at the specified URI. Repeating the same request should produce the same final resource state.

**Other HTTP methods:**

- GET: Retrieve a resource.
    
- PATCH: Partially update a resource.
    
- DELETE: Delete a resource.
    
- HEAD: Retrieve response headers without the response body.
    
- OPTIONS: Discover communication options supported by a resource.
    

**Important interview point:** PUT is idempotent by HTTP semantics; POST is not inherently idempotent. An API can implement idempotency for POST using an idempotency key, such as in payment processing.

## **9. What is the main infrastructure required to support microservices?**

Microservices need supporting infrastructure because multiple independent services must communicate, discover each other, share configuration and remain observable.

**Important infrastructure components:**

|Component|Purpose|Example|
|---|---|---|
|API Gateway|Single entry point for client requests|Spring Cloud Gateway|
|Service Discovery|Find service instances dynamically|Eureka, Consul|
|Load Balancer|Distribute requests across instances|Spring Cloud LoadBalancer, NGINX|
|Config Server|Centralized configuration|Spring Cloud Config|
|Message Broker|Asynchronous communication|Kafka, RabbitMQ|
|Database|Store service-specific data|MySQL, PostgreSQL|
|Distributed Tracing|Track requests across services|OpenTelemetry, Zipkin|
|Monitoring|Monitor application health and metrics|Prometheus, Grafana|
|Containerization|Package services consistently|Docker|
|Orchestration|Deploy and scale containers|Kubernetes|
|Secrets Management|Securely manage credentials|Vault, Kubernetes Secrets|

**Example architecture:**

[Microservices Architecture for Cloud Solutions](https://images.openai.com/static-rsc-4/9G2AEex003AwymZv_jm16CDD7TXjQy_NR1wNUjQvkoiITV44JTndaGSSYc3mmyWIKQifMoUn20geyMCBZPr5Ok7nqJvFAYFYef_ADDQyFuaPT2Q_4yanW2AZf2SbO6Z_Vq4LNthxchlcGUSa_kjUsdBd0_UCBKETCRWlgQs7y_w?purpose=inline)

[linkedin.com](https://www.linkedin.com/top-content/technology/cloud-computing-solutions/microservices-architecture-for-cloud-solutions/?utm_source=chatgpt.com)

**How it works:**

1. A client sends a request to the API Gateway.
    
2. The gateway routes the request to the appropriate service.
    
3. Service discovery helps locate available service instances.
    
4. Load balancing distributes traffic.
    
5. Services communicate using REST or messaging.
    
6. Monitoring and tracing help detect and troubleshoot failures.
    

**Interview answer:** Microservices require infrastructure such as API Gateway, service discovery, centralized configuration, load balancing, message brokers, monitoring, distributed tracing, containerization and orchestration. These components help services communicate, scale and recover from failures.

## **10. What happens if a microservice fails? How do you handle it?**

A microservice failure can affect other services if they depend on it.

For example:

- Order Service calls Payment Service.
    
- Payment Service becomes unavailable.
    
- Order Service requests start failing or waiting.
    
- If requests keep accumulating, Order Service may also become unavailable.
    

This is called **cascading failure**.

**How to handle failures:**

1. **Timeout:** Do not wait indefinitely for another service.
    
2. **Retry:** Retry temporary failures with a limited number of attempts.
    
3. **Circuit Breaker:** Temporarily stop calling an unhealthy service.
    
4. **Fallback:** Provide an alternative response or controlled degradation.
    
5. **Bulkhead:** Isolate resources so one failing dependency does not consume all resources.
    
6. **Monitoring and alerting:** Detect failures using metrics and logs.
    
7. **Message queues:** Use asynchronous processing when appropriate.
    
8. **Idempotency:** Prevent duplicate operations during retries.
    

**Example using Resilience4j:**

```
@Service
public class PaymentClient {

    @CircuitBreaker(
        name = "paymentService",
        fallbackMethod = "paymentFallback"
    )
    public String processPayment() {
        // Call payment service
        return "Payment successful";
    }

    public String paymentFallback(Exception ex) {
        return "Payment service is temporarily unavailable";
    }
}
```

This is a simplified example. A real payment workflow should not report a successful payment just because a fallback method ran. It should return a pending/unknown state or a suitable failure response and reconcile the actual payment status.

**Interview answer:** If a microservice fails, I would prevent cascading failures using timeouts, controlled retries, circuit breakers and bulkheads. I would also use monitoring and logging to identify the failure. For asynchronous workflows, a message broker can help with retries and recovery.

## **11. What is an API Gateway? What features does it support?**

An **API Gateway** is an entry point between clients and backend microservices.

Instead of clients calling multiple microservices directly, they send requests to the gateway, which routes them to the appropriate service.

[System Design was HARD until I Learned these 30 Concepts](https://images.openai.com/static-rsc-4/oI7e6qGM_W-my91dICqrhC5MLkI1kjhi4Fws3jiK0nPhqh9yh4dQZMIAiewbYN9a8MVA4Tf4euswDvFkYksZYoybcumbSvNQ1Le0cty3ifBcbGxQNsWCvtcCIdv5_W28TGtmpfv-xiH1PAZ0f3DdQ07RD6tKMNv00tsPzhepknQ?purpose=inline)

[blog.algomaster.io](https://blog.algomaster.io/p/30-system-design-concepts?utm_source=chatgpt.com)

**Example:**

```
Client
  |
  v
API Gateway
  |
  |----> User Service
  |
  |----> Product Service
  |
  |----> Order Service
  |
  |----> Payment Service
```

**Main features:**

|Feature|Explanation|
|---|---|
|Routing|Routes requests to appropriate services|
|Authentication|Validates user credentials or tokens|
|Authorization|Enforces access rules when configured|
|Rate Limiting|Restricts excessive requests|
|Load Balancing|Distributes requests among service instances|
|CORS|Manages cross-origin access|
|Logging|Records incoming requests|
|Monitoring|Collects metrics|
|Request/Response Transformation|Modifies headers or payloads|
|SSL/TLS Termination|Handles secure client connections|
|Circuit Breaker|Can protect calls to downstream services|
|API Versioning|Can route requests based on API versions|

**Spring Cloud Gateway example:**

```
spring:
  cloud:
    gateway:
      routes:
        - id: employee-service
          uri: http://localhost:8081
          predicates:
            - Path=/api/employees/**
```

Now requests to:

```
http://localhost:8080/api/employees
```

are routed to the Employee Service running on port `8081`.

**Interview answer:** API Gateway acts as a single entry point for microservices. It supports routing, authentication, rate limiting, logging, load balancing, CORS and request transformation. Spring Cloud Gateway is one option in the Spring ecosystem.

## **12. How do you handle authentication? What is the structure of a JWT?**

Authentication verifies the identity of a user.

In a Spring Boot application, JWT-based authentication is commonly implemented using Spring Security.

**JWT stands for JSON Web Token.**

A JWT has three parts:

**1. Header**

Contains token type and signing algorithm.

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**2. Payload**

Contains claims such as subject, issuer and expiration.

```
{
  "sub": "101",
  "role": "USER",
  "exp": 1900000000
}
```

**3. Signature**

Used to verify token integrity and authenticity.

```
HMACSHA256(
  base64Url(header) + "." +
  base64Url(payload),
  secret
)
```

The final token looks like:

```
header.payload.signature
```

**Authentication flow:**

**1. User login**

Client sends username and password.

**2. Credential validation**

Spring Security verifies the credentials.

**3. Token generation**

Backend creates and signs a JWT.

**4. Client stores token**

Client keeps the token securely.

**5. API request**

Client sends Authorization: Bearer <token>.

**6. Token validation**

Security filter validates signature, expiration and claims.

**7. Authorization**

Application checks roles and permissions.

**Example request:**

```
GET /api/employees
Authorization: Bearer eyJhbGciOiJIUzI1NiJ9...
```

**Important security points:**

- JWT payload is Base64URL-encoded, not encrypted by default.
    
- Never put passwords or sensitive information in JWT claims.
    
- Validate signature, issuer, audience and expiration as appropriate.
    
- Use HTTPS.
    
- Keep signing keys secure.
    
- Plan token expiry, refresh and revocation strategies.
    
- Avoid storing long-lived tokens in insecure browser storage.
    

**Interview answer:** I would implement JWT authentication using Spring Security. After validating user credentials, the backend generates a signed JWT. The client sends it in the Authorization header for subsequent requests. A security filter validates the token and establishes the authenticated user context before authorization checks.

## **13. How do you implement a Circuit Breaker? How do you set thresholds?**

A **Circuit Breaker** is a design pattern used to prevent repeated calls to an unhealthy or unavailable service.

It helps prevent cascading failures in distributed systems.

There are three main states:

CLOSED

**Normal operation**

Requests are allowed. Failures are monitored.

OPEN

**Calls blocked**

When the failure threshold is reached, calls are rejected or sent to a fallback without invoking the downstream service.

HALF_OPEN

**Recovery testing**

After a wait period, a limited number of test calls are allowed. Success can close the circuit; failure opens it again.

**Example using Spring Boot and Resilience4j**

Dependency:

```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
</dependency>
```

The exact dependency version should be compatible with your Spring Boot and Spring Cloud release train.

Configuration:

```
resilience4j:
  circuitbreaker:
    instances:
      paymentService:
        slidingWindowType: COUNT_BASED
        slidingWindowSize: 10
        minimumNumberOfCalls: 5
        failureRateThreshold: 50
        waitDurationInOpenState: 10s
        permittedNumberOfCallsInHalfOpenState: 3
```

Service:

```
@Service
public class PaymentService {

    @CircuitBreaker(
        name = "paymentService",
        fallbackMethod = "fallback"
    )
    public String callPaymentService() {
        // Call external payment service
        return "Payment processed";
    }

    public String fallback(Exception ex) {
        return "Payment service unavailable";
    }
}
```

### **How are thresholds configured?**

Thresholds determine when the circuit breaker changes its state.

|Configuration|Meaning|
|---|---|
|`slidingWindowSize: 10`|Evaluate the latest 10 calls|
|`minimumNumberOfCalls: 5`|Wait until at least 5 calls before evaluating failure rate|
|`failureRateThreshold: 50`|Open circuit when failure rate reaches 50%|
|`waitDurationInOpenState: 10s`|Wait 10 seconds before moving to Half-Open|
|`permittedNumberOfCallsInHalfOpenState: 3`|Allow 3 test calls in Half-Open|

Note: A count-based sliding window of 10 does not mean the circuit opens after exactly 10 calls. The minimum-call requirement and configured failure rate determine the decision.

## **14. What is the purpose of a threshold? How does it work?**

A **threshold** is a predefined limit used to decide when a system should take an action.

In a Circuit Breaker, thresholds help identify when a downstream service is unhealthy.

**Example:**

Suppose we configure:

```
failureRateThreshold: 50
minimumNumberOfCalls: 5
```

Assume the first five calls have these results:

|Call|Result|
|---|---|
|1|Success|
|2|Failure|
|3|Failure|
|4|Success|
|5|Failure|

Failure rate:

\[ \text{Failure Rate} = \frac{3}{5} \times 100 = 60\% \]

Since 60% is greater than the configured 50% threshold, the Circuit Breaker opens.

**Why do we need thresholds?**

- To identify unhealthy dependencies.
    
- To prevent repeated failed calls.
    
- To protect application resources.
    
- To give downstream services time to recover.
    
- To control when recovery testing begins.
    

**Important distinction:**

- `failureRateThreshold`: Percentage of failed calls.
    
- `slowCallRateThreshold`: Percentage of calls that exceed the configured slow-call duration.
    
- `slowCallDurationThreshold`: Defines what counts as a slow call.
    
- `minimumNumberOfCalls`: Minimum sample size before evaluating the failure rate.
    

**Interview answer:** A threshold is a configured limit that triggers an action. In Resilience4j Circuit Breaker, the failure-rate threshold determines when the circuit opens based on the percentage of failed calls after the minimum number of calls has been reached.

## **15. What is Kafka Schema Registry?**

**Schema Registry** is a service that stores and manages schemas used to serialize and deserialize Kafka messages.

It is commonly used with formats such as:

- Apache Avro
    
- Protocol Buffers
    
- JSON Schema
    

Without a schema registry, producers and consumers must independently manage compatible message structures.

### **Example without Schema Registry**

Producer sends:

```
{
  "id": 101,
  "name": "Rahul"
}
```

Later, the producer changes the message:

```
{
  "id": 101,
  "name": "Rahul",
  "email": "rahul@example.com"
}
```

An older consumer may not understand the changed structure, depending on how it deserializes messages.

### **Example with Schema Registry**

The producer registers a schema:

```
{
  "type": "record",
  "name": "Employee",
  "fields": [
    {
      "name": "id",
      "type": "int"
    },
    {
      "name": "name",
      "type": "string"
    }
  ]
}
```

Schema Registry assigns a schema ID.

The Kafka serializer uses the schema ID when writing the message. The consumer's deserializer uses that ID to retrieve the correct schema and deserialize the message.

[Schema registry: Decrease payload for Kafka records | by Deepti Mittal | Medium](https://images.openai.com/static-rsc-4/KnJS4Pf404AVBovTdNA1wSVqLM3PTMVa7rFefn3k7dNj8iB416mfO5soopJdsBrBbMlDiFe7CYyK-1sb1t_ysyIFl8beS5DxKIks2MqvIYz-kGz5MtCTRCqu4xiMB_W3M8vZVRRGmTh7ZrJggCNNqCb1qdEd-Tk3iPkTeff6xZ8?purpose=inline)

[deeptimittalblogger.medium.com](https://deeptimittalblogger.medium.com/schema-registry-decrease-payload-for-kafka-records-52019f4f0052?utm_source=chatgpt.com)

**Main benefits:**

1. Schema validation.
    
2. Consistent message structure.
    
3. Schema evolution.
    
4. Compatibility checking.
    
5. Reduced serialization/deserialization errors.
    

**Schema compatibility types:**

|Type|Meaning|
|---|---|
|BACKWARD|New schema can read data written using the previous schema|
|FORWARD|Previous schema can read data written using the new schema|
|FULL|Both backward and forward compatibility|
|NONE|No compatibility checks|

There are also transitive variants, which check compatibility against multiple earlier schema versions rather than only the latest version.

**Interview answer:** Kafka Schema Registry centrally manages message schemas for Kafka producers and consumers. It helps maintain compatibility when message structures change and supports formats such as Avro, Protobuf and JSON Schema.

## **16. Have you worked on microservices? Can you explain the 12-factor principles?**

First, understand the distinction: the **12-Factor App** is a methodology for building maintainable, scalable and deployable applications, particularly cloud-native applications. It is useful for microservices, but it is not a definition of microservices.

The 12 factors are:

|No.|Factor|Explanation|
|---|---|---|
|1|Codebase|One codebase tracked in version control, with multiple deployments|
|2|Dependencies|Explicitly declare and isolate dependencies|
|3|Config|Store configuration outside application code|
|4|Backing Services|Treat databases, message brokers and external services as attached resources|
|5|Build, Release, Run|Separate build, release and execution stages|
|6|Processes|Run the application as stateless processes|
|7|Port Binding|Export services through a port|
|8|Concurrency|Scale by running multiple processes or instances|
|9|Disposability|Start quickly and shut down gracefully|
|10|Dev/Prod Parity|Keep development, testing and production environments as similar as practical|
|11|Logs|Treat logs as event streams|
|12|Admin Processes|Run administrative tasks as separate processes|

**Practical examples in Spring Boot:**

- Factor 3: Use `application.yml`, environment variables and externalized secrets.
    
- Factor 4: Connect to MySQL, Kafka and Redis through configurable endpoints.
    
- Factor 5: Build a JAR, create a Docker image, then deploy that image.
    
- Factor 6: Avoid keeping important application state only in local memory.
    
- Factor 8: Run multiple service instances behind a load balancer.
    
- Factor 9: Implement graceful shutdown and readiness/liveness checks.
    
- Factor 11: Send logs to centralized logging platforms.
    

### **How to answer: "Have you worked on microservices?"**

Do not claim experience that you have not had. You can answer based on your actual work and learning:

**Interview-ready answer:**

"I have worked primarily on Java and Spring Boot backend development. I understand microservice architecture, including service-to-service communication, API Gateway, service discovery, centralized configuration, Kafka messaging and fault-tolerance patterns. I have also been learning and implementing these concepts through practical development. I would be comfortable explaining how these components work together, while distinguishing my hands-on project experience from concepts I have studied."

If you have implemented a specific component in a project, mention exactly what you implemented and how you tested it.

## **17. What is the DRY principle?**

DRY stands for **Don't Repeat Yourself**.

It means that every piece of knowledge or business logic should have a single authoritative representation in a system.

The goal is to reduce duplicate code and make maintenance easier.

**Example: Without DRY**

```
public class EmployeeService {

    public double calculateBonus(double salary) {
        return salary * 0.10;
    }
}

public class ManagerService {

    public double calculateBonus(double salary) {
        return salary * 0.10;
    }
}
```

The same calculation is duplicated.

**With DRY:**

```
public class BonusCalculator {

    public double calculateBonus(double salary) {
        return salary * 0.10;
    }
}
```

Both services can use the shared component:

```
@Service
public class EmployeeService {

    private final BonusCalculator calculator;

    public EmployeeService(BonusCalculator calculator) {
        this.calculator = calculator;
    }

    public double calculateBonus(double salary) {
        return calculator.calculateBonus(salary);
    }
}
```

**Benefits:**

- Less duplicate code.
    
- Easier maintenance.
    
- Fewer inconsistent implementations.
    
- Easier testing.
    
- Improved reusability.
    

**Important:** DRY does not mean forcing unrelated logic into one common method. Avoid unnecessary abstraction and keep code readable.

**Interview answer:** DRY means Don't Repeat Yourself. It encourages keeping business logic in a single reusable place rather than duplicating it across multiple classes. This improves maintainability and reduces inconsistent behavior.

## **18. What is the difference between PUT and QUERY methods in HTTP?**

There is no standard HTTP method named `QUERY` in the commonly used HTTP method set.

You may mean **GET**. GET is used to retrieve information, whereas PUT is used to create or replace a resource at a known URI.

|Feature|GET|PUT|
|---|---|---|
|Purpose|Retrieve data|Create or replace resource|
|Request body|Usually not used|Typically contains resource representation|
|Changes server state|Intended to be safe|Can change server state|
|Idempotent|Yes|Yes|
|Caching|Commonly cacheable|Not normally cached|
|Example|Get employee details|Update employee details|

**GET example:**

```
GET /api/employees/101
```

Response:

```
{
  "id": 101,
  "name": "Rahul"
}
```

**PUT example:**

```
PUT /api/employees/101
Content-Type: application/json

{
  "name": "Rahul Sharma",
  "email": "rahul@example.com"
}
```

**What does idempotent mean?**

If we execute the same request multiple times, the final server state should remain the same as after the first successful request.

**Interview answer:** GET retrieves a resource and should not change server state. PUT creates or replaces a resource at a known URI and is idempotent. There is no standard HTTP `QUERY` method in the usual HTTP methods, so I would clarify whether the interviewer means GET.

## **19. What is parameterized testing in JUnit?**

Parameterized testing allows us to execute the same test method multiple times with different input values.

Instead of writing separate test methods for every input, we write one test method and provide multiple test cases.

JUnit 5 supports parameterized testing through `@ParameterizedTest`.

**Required dependency:** `junit-jupiter-params` (often included through `spring-boot-starter-test`).

### **Example 1: Using** `@ValueSource`

```
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

class NumberTest {

    @ParameterizedTest
    @ValueSource(ints = {2, 4, 6, 8, 10})
    void shouldCheckEvenNumbers(int number) {

        assertEquals(0, number % 2);
    }
}
```

JUnit executes this test five times with different inputs.

### **Example 2: Using** `@CsvSource`

```
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    int add(int a, int b) {
        return a + b;
    }

    @ParameterizedTest
    @CsvSource({
        "10, 20, 30",
        "5, 5, 10",
        "2, 3, 5",
        "100, 200, 300"
    })
    void shouldAddNumbers(int a, int b, int expected) {

        assertEquals(expected, add(a, b));
    }
}
```

### **Common parameter sources**

|Annotation|Purpose|
|---|---|
|`@ValueSource`|Provides simple values|
|`@CsvSource`|Provides comma-separated arguments|
|`@CsvFileSource`|Reads test data from CSV files|
|`@MethodSource`|Provides arguments through a method|
|`@EnumSource`|Provides enum values|
|`@NullSource`|Supplies null|
|`@EmptySource`|Supplies empty values|
|`@NullAndEmptySource`|Supplies null and empty values|

**Benefits:**

- Reduces duplicate test code.
    
- Tests multiple input combinations.
    
- Improves test coverage.
    
- Makes edge-case testing easier.
    

**Interview answer:** Parameterized testing in JUnit 5 allows us to execute the same test method with multiple sets of input data using annotations such as `@ValueSource`, `@CsvSource` and `@MethodSource`. It reduces code duplication and helps test different scenarios efficiently.

## **20. What is the difference between @Mock and @Spy in JUnit?**

The question likely refers to Mockito's `@Mock` and `@Spy` annotations, which are commonly used with JUnit 5.

Both are used in unit testing, but their behavior is different.

|Feature|`@Mock`|`@Spy`|
|---|---|---|
|Object|Creates a mock object|Wraps or creates a spy around a real object|
|Real methods|Not executed by default|Executed by default|
|Method behavior|Returns default values unless stubbed|Executes real implementation unless stubbed|
|Use case|Isolate dependencies|Partially mock real behavior|

### **A. @Mock example**

Suppose we have an EmployeeService that depends on EmployeeRepository.

```
@Service
public class EmployeeService {

    private final EmployeeRepository repository;

    public EmployeeService(EmployeeRepository repository) {
        this.repository = repository;
    }

    public String getEmployeeName(Long id) {
        Employee employee = repository.findById(id)
                .orElseThrow();

        return employee.getName();
    }
}
```

Test using `@Mock`:

```
@ExtendWith(MockitoExtension.class)
class EmployeeServiceTest {

    @Mock
    private EmployeeRepository repository;

    @InjectMocks
    private EmployeeService service;

    @Test
    void shouldReturnEmployeeName() {

        Employee employee = new Employee();
        employee.setName("Rahul");

        when(repository.findById(1L))
                .thenReturn(Optional.of(employee));

        String result = service.getEmployeeName(1L);

        assertEquals("Rahul", result);

        verify(repository).findById(1L);
    }
}
```

Here:

- `@Mock` creates a fake repository.
    
- `@InjectMocks` creates the service and injects the mock dependency.
    
- `when().thenReturn()` defines the mock behavior.
    
- `verify()` checks whether a method was called.
    

### **B. @Spy example**

A Spy allows real methods to execute unless we specifically override their behavior.

```
@ExtendWith(MockitoExtension.class)
class CalculatorTest {

    @Spy
    private Calculator calculator;

    @Test
    void shouldCallRealMethod() {

        int result = calculator.add(10, 20);

        assertEquals(30, result);
    }
}
```

Suppose the Calculator class is:

```
class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }
}
```

We can override one method:

```
doReturn(100)
    .when(calculator)
    .add(10, 20);
```

Now `add(10, 20)` returns 100, while other methods continue using their real implementations.

**Important difference in stubbing:**

With spies, prefer:

```
doReturn(100)
    .when(calculator)
    .add(10, 20);
```

Instead of:

```
when(calculator.add(10, 20))
    .thenReturn(100);
```

The second version may invoke the real method while setting up the stub.

### **C. What is @InjectMocks?**

`@InjectMocks` is another Mockito annotation.

It creates the class under test and attempts to inject its mock or spy dependencies.

Example:

```
@Mock
EmployeeRepository repository;

@InjectMocks
EmployeeService service;
```

Here Mockito injects the mock repository into the EmployeeService.

### **D. What is the difference between @MockBean and @MockitoBean?**

This is another useful Spring Boot interview question:

- `@Mock`: Creates a Mockito mock for a unit test.
    
- `@Spy`: Creates a Mockito spy.
    
- `@MockitoBean`: Overrides a bean in the Spring test application context with a Mockito mock. Available in Spring Framework 6.2+.
    
- `@MockBean`: Older Spring Boot testing annotation; deprecated in Spring Boot 3.4 in favor of `@MockitoBean`.
    

**Interview answer:** `@Mock` creates a mock object where methods return configured values, while `@Spy` wraps a real object and calls real methods by default. We use mocks to isolate dependencies and spies when we want to test real behavior while selectively overriding some methods.