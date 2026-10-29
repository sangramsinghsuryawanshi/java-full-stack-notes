## 1. How do you optimize a REST API?

### What does REST API optimization mean?

REST API optimization means improving an API's **response time, throughput, resource usage, reliability, and scalability** without changing its expected behavior.

For example, suppose a Spring Boot API takes 2 seconds to return invoice details. We want to reduce its response time while supporting more concurrent requests.

### A. Identify the bottleneck first

Before making changes, measure the API.

Useful metrics include:

|Metric|What it tells you|
|---|---|
|Response time|How long an API takes to respond|
|Throughput|How many requests it processes per second|
|Error rate|Percentage of failed requests|
|CPU usage|Whether computation is consuming too much CPU|
|Heap and GC metrics|Whether memory pressure is affecting performance|
|Database query time|Whether database operations are slow|
|Connection pool usage|Whether requests are waiting for database connections|

Useful tools include Spring Boot Actuator, application logs, database query analysis, and load-testing tools such as JMeter.

### B. Optimize database queries

Database queries are often a major source of API latency.

**Problem:** Fetching every invoice when the client needs only 20.

```
List<Invoice> invoices = invoiceRepository.findAll();
```

**Better:** Fetch a limited page of results.

```
@GetMapping("/invoices")
public Page<InvoiceDto> getInvoices(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "20") int size) {

    Pageable pageable = PageRequest.of(page, size);
    return invoiceService.getInvoices(pageable);
}
```

Repository:

```
public interface InvoiceRepository
        extends JpaRepository<Invoice, Long> {
}
```

Service:

```
public Page<InvoiceDto> getInvoices(Pageable pageable) {
    return invoiceRepository.findAll(pageable)
            .map(invoice -> new InvoiceDto(
                    invoice.getId(),
                    invoice.getInvoiceNumber()
            ));
}
```

Here, `InvoiceDto` is assumed to have a constructor accepting the invoice ID and number.

**Benefits:**

- Retrieves a manageable number of records.
- Reduces memory consumption.
- Avoids sending unnecessary data.
- Makes large result sets easier to navigate.

For frequently filtered columns, examine the query plan and consider appropriate indexes. An index can improve reads but adds storage and write overhead, so validate its impact.

### C. Avoid the N+1 query problem

Suppose you retrieve 100 invoices, and accessing each invoice's customer triggers another query. The application may execute 101 queries instead of one or a small number of queries.

Possible solutions include:

- Fetch joins.
- DTO projections.
- Carefully selected entity graphs.
- Batch fetching.

For example:

```
@Query("""
       SELECT i
       FROM Invoice i
       JOIN FETCH i.customer
       WHERE i.id = :id
       """)
Optional<Invoice> findByIdWithCustomer(
        @Param("id") Long id);
```

This assumes `Invoice` has a mapped association named `customer`.

**Important:** Don't add fetch joins indiscriminately. Fetching multiple collections can multiply result rows, and collection fetch joins can complicate pagination.

### D. Return only necessary fields

Avoid serializing large entity graphs when the client needs only a few fields.

```
public record InvoiceDto(
        Long id,
        String invoiceNumber
) {}
```

DTOs can reduce response size and prevent accidental exposure of internal entity fields.

### E. Use caching where appropriate

If frequently requested data changes infrequently, caching may reduce database load.

Spring example:

```
@Cacheable(
        value = "invoice",
        key = "#invoiceId"
)
public InvoiceDto getInvoiceById(Long invoiceId) {
    return invoiceRepository.findById(invoiceId)
            .map(invoice -> new InvoiceDto(
                    invoice.getId(),
                    invoice.getInvoiceNumber()
            ))
            .orElseThrow(
                    () -> new RuntimeException("Invoice not found")
            );
}
```

This is a simplified example. Production code should use an appropriate exception, and the cache must have a deliberate expiration and invalidation strategy.

Caching is not appropriate for every endpoint, especially when data is highly dynamic or user-specific.

### F. Handle expensive operations carefully

Avoid making a user wait for unrelated, slow work.

For example, an invoice API might need to save an invoice and separately trigger a report-generation job. If the report isn't required in the immediate response, asynchronous processing may improve latency.

However, important business operations must not be silently lost. Reliable asynchronous processing may require a durable queue or transactional outbox.

### G. Apply additional optimizations

- Configure database connection pools based on measured workload.
- Set timeouts for external HTTP calls.
- Avoid unnecessary object creation and repeated computations.
- Compress large HTTP responses where appropriate.
- Validate request parameters.
- Use rate limiting and pagination.
- Avoid logging huge payloads on every request.
- Monitor latency percentiles, especially p95 and p99.

### Interview-ready answer

> I optimize REST APIs by first measuring response time, throughput, error rates, database latency, and resource usage. I then identify the actual bottleneck rather than making random changes. Common improvements include optimizing SQL queries, adding appropriate indexes, avoiding N+1 queries, using pagination and DTOs, caching frequently accessed data, and configuring connection pools and timeouts properly. After making changes, I run load tests and compare the results to confirm that performance improved without affecting correctness.

---

## 2. How do you group and partition data using `Collectors.groupingBy()` and `partitioningBy()`?

Both methods belong to Java Streams, but they serve different purposes.

### A. `groupingBy()`

`groupingBy()` groups elements according to a classification function. It can produce **any number of groups**.

Consider this employee class:

```
class Employee {
    private final String name;
    private final String department;
    private final int salary;

    Employee(String name, String department, int salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }

    public String getName() {
        return name;
    }

    public String getDepartment() {
        return department;
    }

    public int getSalary() {
        return salary;
    }
}
```

Sample data:

```
List<Employee> employees = List.of(
        new Employee("Amit", "IT", 60000),
        new Employee("Neha", "HR", 50000),
        new Employee("Rahul", "IT", 70000),
        new Employee("Priya", "HR", 55000),
        new Employee("Sneha", "Finance", 65000)
);
```

**Example 1: Group employees by department**

```
Map<String, List<Employee>> employeesByDepartment =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment
                ));
```

Conceptual result:

```
IT      -> [Amit, Rahul]
HR      -> [Neha, Priya]
Finance -> [Sneha]
```

The map's iteration order is not guaranteed by the default collector.

**Example 2: Count employees in each department**

```
Map<String, Long> employeeCount =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.counting()
                ));
```

Result:

```
IT      -> 2
HR      -> 2
Finance -> 1
```

**Example 3: Calculate average salary by department**

```
Map<String, Double> averageSalary =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.averagingInt(
                                Employee::getSalary
                        )
                ));
```

Result:

```
IT      -> 65000.0
HR      -> 52500.0
Finance -> 65000.0
```

**Example 4: Group by department and collect employee names**

```
Map<String, List<String>> namesByDepartment =
        employees.stream()
                .collect(Collectors.groupingBy(
                        Employee::getDepartment,
                        Collectors.mapping(
                                Employee::getName,
                                Collectors.toList()
                        )
                ));
```

This is useful when the output needs only names rather than complete employee objects.

### B. `partitioningBy()`

`partitioningBy()` divides elements into **exactly two logical groups**, based on a predicate:

- `true`: The condition is satisfied.
- `false`: The condition is not satisfied.

**Example: Partition employees by salary**

```
Map<Boolean, List<Employee>> partitioned =
        employees.stream()
                .collect(Collectors.partitioningBy(
                        employee -> employee.getSalary() >= 60000
                ));
```

Conceptual result:

```
true  -> [Amit, Rahul, Sneha]
false -> [Neha, Priya]
```

The two groups represent employees earning at least ₹60,000 and those earning less.

**Example: Count employees in each partition**

```
Map<Boolean, Long> counts =
        employees.stream()
                .collect(Collectors.partitioningBy(
                        employee -> employee.getSalary() >= 60000,
                        Collectors.counting()
                ));
```

### `groupingBy()` vs `partitioningBy()`

|Feature|`groupingBy()`|`partitioningBy()`|
|---|---|---|
|Purpose|Group by a classification key|Split by a boolean condition|
|Number of groups|Potentially many|Exactly two logical groups|
|Typical result|`Map<K, List<T>>`|`Map<Boolean, List<T>>`|
|Example|Employees by department|Employees above/below salary threshold|
|Downstream collectors|Supported|Supported|

### Interview-ready answer

> `groupingBy()` groups stream elements based on a classifier and can create multiple groups. For example, I can group employees by department or count employees in each department using `Collectors.counting()`. `partitioningBy()` divides elements according to a predicate into true and false groups. For example, I can partition employees into those earning at least ₹60,000 and those earning less.

---

## 3. Sequential Stream vs. Parallel Stream — how do they differ?

### Sequential Stream

A sequential stream processes its operations using sequential stream execution, ordinarily on the calling thread.

```
List<Integer> numbers =
        List.of(1, 2, 3, 4, 5);

int sum = numbers.stream()
        .mapToInt(Integer::intValue)
        .sum();

System.out.println(sum); // 15
```

### Parallel Stream

A parallel stream processes stream operations using parallel execution, typically with multiple worker threads.

```
int sum = numbers.parallelStream()
        .mapToInt(Integer::intValue)
        .sum();

System.out.println(sum); // 15
```

Both produce the same sum. The parallel version is not necessarily faster.

### Key differences

|Feature|Sequential stream|Parallel stream|
|---|---|---|
|Processing|Sequential|Operations may execute concurrently|
|Typical execution|Calling thread|Commonly ForkJoinPool worker threads|
|Overhead|Usually lower|Splitting, scheduling and combining overhead|
|Best suited for|Small tasks, simple operations, ordered workflows|Sufficiently large, parallelizable CPU work|
|Debugging|Usually simpler|More complicated|
|Shared mutable state|Still a concern in general|Especially risky|
|Result order|Depends on the operation and stream characteristics|Some operations preserve encounter order; others do not|

### When might parallel streams help?

Consider a large in-memory collection where every element undergoes substantial CPU-intensive work independent of other elements.

```
long count = numbers.parallelStream()
        .filter(this::expensiveCpuCheck)
        .count();
```

Here, `expensiveCpuCheck()` is illustrative; its implementation and the collection size determine whether parallel execution helps.

Parallel execution is most promising when:

- There is enough work to justify parallelization.
- Each element can be processed independently.
- The source can be split efficiently.
- Shared-state contention is limited.
- The machine has available CPU capacity.

### Important: encounter order

A parallel stream does not automatically mean that the final result is unordered.

For example:

```
List<Integer> result = numbers.parallelStream()
        .map(n -> n * 2)
        .toList();
```

For this ordered source, the resulting list preserves encounter order.

However, operations such as `forEach()` do not guarantee encounter order for parallel streams. If order matters, use an appropriate operation such as `forEachOrdered()`, understanding that enforcing order can reduce parallelism.

### Interview-ready answer

> A sequential stream processes elements sequentially, while a parallel stream can split work across multiple threads. Parallel streams may improve performance for large, CPU-intensive, independent operations, but they introduce scheduling and combination overhead. I would benchmark both approaches using representative data before choosing parallel execution.

---

## 4. How do you optimize a REST API?

This is the same as Question 1. For interviews, remember this **optimization checklist**:

1. **Measure:** Check p95/p99 latency, throughput, error rate, CPU, and memory.
2. **Database:** Analyze slow queries, indexes, joins, and N+1 queries.
3. **Response size:** Use pagination, DTOs, and appropriate fields.
4. **Caching:** Cache suitable, frequently requested data.
5. **Concurrency:** Tune application threads and database connections based on workload.
6. **External calls:** Configure timeouts, retries, and circuit breakers where appropriate.
7. **Reliability:** Use validation, rate limiting, and safe error handling.
8. **Verify:** Load-test before and after changes.

**Interview tip:** Say that you first identify the bottleneck, then optimize and measure again. Avoid claiming that caching, parallel processing, or adding threads always improves performance.

---

## 5. How does Garbage Collection (GC) affect application performance?

### What is Garbage Collection?

In Java, Garbage Collection automatically reclaims heap memory occupied by objects that are no longer reachable.

For example:

```
public void processInvoice() {
    Invoice invoice = new Invoice();
    invoice.setInvoiceNumber("INV-101");
}
```

After the method finishes, the local reference is no longer available. If the object has no other reachable references, it becomes eligible for garbage collection.

**Important:** Becoming eligible for GC does not mean the object will be collected immediately.

### How can GC affect performance?

**1. GC pauses**

Some garbage collection work requires application threads to pause. Long pauses can increase API latency.

**2. High allocation rate**

Creating many short-lived objects can increase garbage collection frequency.

```
for (int i = 0; i < 1_000_000; i++) {
    String result = "Invoice-" + i;
}
```

This illustrates repeated object creation. Modern JVM optimizations may eliminate some allocations, depending on the code.

**3. Memory pressure**

If the heap has insufficient space for the application's live objects, GC may work harder to reclaim memory.

**4. Memory leaks**

Java can have memory leaks when objects are no longer needed but remain reachable, for example through a static collection or an unbounded cache.

### How do you investigate GC problems?

Start with evidence:

- Monitor heap usage and GC activity.
- Examine GC logs.
- Use Java Flight Recorder (JFR) or a profiler.
- Capture a heap dump if retained objects are suspected.
- Inspect allocation rates, object retention, and long pauses.
- Check whether the problem is actually GC-related rather than slow SQL, network calls, or lock contention.

Useful JVM options for enabling GC logging on modern Java versions include:

```
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```

This writes detailed GC logs to `gc.log`.

### How can you improve GC-related performance?

- Avoid unnecessary object creation in hot paths.
- Bound caches and collections.
- Fix memory leaks.
- Choose heap settings based on measurements.
- Select a suitable garbage collector for the application's latency and throughput requirements.
- Upgrade the JVM when appropriate and test the impact.

Do not manually call `System.gc()` as a routine performance fix. It is only a request to the JVM and is not a reliable way to improve application performance.

### Interview-ready answer

> Garbage Collection can affect performance through pauses, allocation overhead, and memory pressure. I would monitor heap usage, GC frequency and pause times, and use GC logs or Java Flight Recorder to identify the cause. Depending on the findings, I might reduce unnecessary allocations, fix an unbounded cache or memory leak, or adjust JVM settings. I would validate the change using latency and memory metrics.

---

## 6. When should you avoid using `parallelStream()`?

Parallel streams are useful in some workloads, but they are not a universal optimization.

### Avoid them in these situations

**1. Small collections**

The overhead of splitting and combining work may be greater than the time saved.

```
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

int sum = numbers.parallelStream()
        .mapToInt(Integer::intValue)
        .sum();
```

For five integers, a sequential stream is generally the simpler choice.

**2. Blocking I/O**

Avoid using parallel streams as a general mechanism for coordinating large numbers of blocking database queries or HTTP requests.

Blocking tasks can occupy shared worker threads and interfere with unrelated work using the same pool.

**3. Shared mutable state**

This is unsafe:

```
List<Integer> results = new ArrayList<>();

numbers.parallelStream()
        .forEach(results::add);
```

Multiple threads can modify the same `ArrayList` concurrently, leading to data races and incorrect results.

A safer alternative is to use a collector:

```
List<Integer> results = numbers.parallelStream()
        .map(n -> n * 2)
        .toList();
```

**4. Order-sensitive operations**

If processing depends on a specific sequence, parallel execution may be inappropriate or require extra coordination.

**5. CPU saturation**

If the machine is already busy, adding more concurrent CPU work may increase contention instead of improving throughput.

**6. Operations with side effects**

Avoid relying on the order or timing of side effects inside stream operations.

### What should you use instead?

- Sequential streams for simple transformations.
- Parallel streams for suitable CPU-bound workloads after benchmarking.
- Dedicated executors for controlled task concurrency.
- Asynchronous HTTP clients for suitable non-blocking network operations.
- Durable queues for reliable background business processing.

### Interview-ready answer

> I avoid parallel streams for small datasets, blocking I/O, shared mutable state, order-sensitive operations, and CPU-saturated workloads. Parallel streams are best considered for sufficiently large, independent CPU-bound tasks. I benchmark them against sequential processing and choose the simpler approach if there is no measurable benefit.

---

## 7. How would you maintain transaction consistency?

### What is transaction consistency?

Transaction consistency means that a business operation preserves the required data rules. A transaction should not leave the database in a partially updated or invalid state.

For example, transferring ₹1,000 from Account A to Account B should not debit Account A without crediting Account B.

This is commonly discussed through the ACID properties:

|Property|Meaning|
|---|---|
|Atomicity|All operations in the transaction succeed or the transaction rolls back|
|Consistency|The transaction preserves defined integrity rules|
|Isolation|Concurrent transactions do not produce unacceptable interference|
|Durability|Committed changes survive according to the database's durability guarantees|

### A. Use `@Transactional`

Consider a simple money transfer.

```
@Service
public class TransferService {

    private final AccountRepository accountRepository;

    public TransferService(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }

    @Transactional
    public void transfer(
            Long fromId,
            Long toId,
            BigDecimal amount) {

        if (amount == null ||
                amount.compareTo(BigDecimal.ZERO) <= 0) {
            throw new IllegalArgumentException(
                    "Amount must be positive"
            );
        }

        Account from = accountRepository.findById(fromId)
                .orElseThrow();

        Account to = accountRepository.findById(toId)
                .orElseThrow();

        if (from.getBalance().compareTo(amount) < 0) {
            throw new IllegalStateException(
                    "Insufficient balance"
            );
        }

        from.setBalance(from.getBalance().subtract(amount));
        to.setBalance(to.getBalance().add(amount));

        accountRepository.save(from);
        accountRepository.save(to);
    }
}
```

This example assumes the accounts are managed by JPA and the repository is configured within the same transaction manager.

**Important limitations:**

- The example does not yet handle simultaneous transfers safely.
- `@Transactional` normally rolls back on unchecked exceptions by default; checked exceptions require appropriate rollback configuration when necessary.
- Spring transaction interception generally applies when the method is invoked through the Spring proxy. Self-invocation can bypass the expected transaction behavior.

### B. Handle concurrent updates

Suppose two requests simultaneously read the same account balance and both attempt to withdraw money. Both might pass a balance check before either transaction commits.

Two common approaches are:

**Optimistic locking**

```
@Version
private Long version;
```

JPA uses the version to detect conflicting updates. A conflict may produce an optimistic locking exception, which the application must handle.

**Pessimistic locking**

A transaction locks the relevant database row while performing its operation. This can prevent certain conflicts but may increase waiting and deadlock risk.

You can also use conditional SQL updates, database constraints, and appropriate transaction isolation levels, depending on the business operation.

### C. Maintain consistency across external services

A database transaction generally cannot atomically commit both a local database update and an external payment-provider operation.

For multi-step workflows, consider:

- Idempotency keys.
- Transactional outbox patterns.
- Reliable event delivery.
- Saga workflows with compensating actions.
- Reconciliation jobs.

These patterns are especially important for payments and distributed systems.

### Interview-ready answer

> I maintain transaction consistency by defining the business invariants, using database transactions for related changes, and applying appropriate locking or isolation when concurrent updates are possible. I also validate data and enforce database constraints. For operations involving external services, such as payments, I use idempotency, reliable event processing, and reconciliation rather than assuming a local `@Transactional` annotation can make the entire distributed operation atomic.

---

## 8. How do you handle database connection pool exhaustion?

### What does connection pool exhaustion mean?

Spring Boot applications commonly use a connection pool such as HikariCP.

Instead of creating a new database connection for every request, the application reuses a managed set of connections.

Pool exhaustion happens when all available connections are in use and new requests cannot obtain one within the configured wait period.

Example:

```
Maximum connections: 20
Connections currently in use: 20
New request: waits for a connection
Wait timeout reached: request fails
```

### Common causes

1. Slow SQL queries.
2. Transactions held open too long.
3. External HTTP calls made inside database transactions.
4. Connections not being released correctly.
5. Too many concurrent requests.
6. The database itself being overloaded.
7. Pool size being inappropriate for the database's capacity.

### How do you investigate it?

Check:

- Active, idle, and pending connections.
- Connection acquisition wait times.
- Database slow-query logs.
- Transaction duration.
- Thread dumps when requests appear blocked.
- Whether connections remain active during external calls.
- Database connection limits and utilization.

HikariCP metrics, where exposed through monitoring, can help identify saturation.

### How do you fix it?

**1. Optimize slow queries.**

An operation holding a connection for 5 seconds occupies it much longer than one taking 50 milliseconds.

**2. Keep transactions short.**

Avoid unnecessary work inside transaction boundaries, especially remote API calls.

**3. Fix connection leaks.**

When using raw JDBC, close resources using try-with-resources:

```
try (Connection connection = dataSource.getConnection();
     PreparedStatement statement =
             connection.prepareStatement(
                     "SELECT id FROM invoice WHERE id = ?")) {

    statement.setLong(1, invoiceId);

    try (ResultSet resultSet = statement.executeQuery()) {
        while (resultSet.next()) {
            System.out.println(resultSet.getLong("id"));
        }
    }
}
```

With Spring Data JPA, follow the framework's resource-management conventions.

**4. Tune the pool carefully.**

For example, in `application.properties`:

```
spring.datasource.hikari.maximum-pool-size=20
spring.datasource.hikari.connection-timeout=30000
```

These are illustrative values, not universal recommendations. Actual values depend on workload, database limits, query duration, and the number of application instances.

**5. Apply backpressure or admission control.**

If the database cannot handle more work, reject, queue, or limit requests appropriately rather than allowing unlimited waiting.

### Important interview point

Increasing the connection pool size is not always the solution. If the database is already saturated, more concurrent connections can make the problem worse.

### Interview-ready answer

> I first inspect active and idle connections, pending requests, acquisition timeouts, slow queries, and transaction duration. Then I identify whether the cause is slow database work, long transactions, a connection leak, or excessive concurrency. I optimize the root cause and tune HikariCP based on measured database capacity. I would not simply increase the pool size without checking the database's limits.

---

## 9. How does thread-pool configuration affect performance?

### What is a thread pool?

A thread pool maintains a set of reusable worker threads to execute tasks without creating a new thread for every task.

Spring Boot web applications often use a configured server thread pool, while background tasks may use separate executors.

### What happens if the pool is too small?

- Requests wait for available threads.
- Throughput can decrease.
- Queues may grow.
- Response times increase.
- Requests may time out.

### What happens if the pool is too large?

- More memory is consumed.
- Context switching may increase.
- CPU contention can increase.
- The application may overwhelm the database or external services.
- More concurrent requests can increase connection-pool pressure.

### CPU-bound vs. I/O-bound work

**CPU-bound tasks:** Most time is spent executing computations.

For CPU-bound workloads, a thread count near the number of available CPU cores is a reasonable starting point for experiments, not a universal rule.

**I/O-bound tasks:** Most time is spent waiting for databases, network calls, or other services.

These workloads may benefit from a different concurrency level, but the actual limit depends on resource capacity and how blocking is handled.

### Example: Configure a Spring executor

```
@Configuration
@EnableAsync
public class AsyncConfig {

    @Bean(name = "applicationExecutor")
    public Executor applicationExecutor() {
        ThreadPoolTaskExecutor executor =
                new ThreadPoolTaskExecutor();

        executor.setCorePoolSize(8);
        executor.setMaxPoolSize(16);
        executor.setQueueCapacity(200);
        executor.setThreadNamePrefix("app-task-");

        executor.initialize();
        return executor;
    }
}
```

These numbers are examples only. They should be chosen through load testing and monitoring.

You can use the executor with:

```
@Async("applicationExecutor")
public void generateReport() {
    // Perform background work.
}
```

`@Async` requires appropriate Spring configuration and proxy invocation. Also, the caller does not automatically receive a reliable record of the background task's completion or failure. Important tasks may need durable queueing and retry mechanisms.

### How should you tune a thread pool?

Monitor:

- Active thread count.
- Queue length.
- Task rejection count.
- Task execution duration.
- CPU utilization.
- Database connection usage.
- Request latency and throughput.

### Interview-ready answer

> Thread-pool configuration controls how much work an application can execute concurrently. A pool that is too small can cause queuing and high latency, while a pool that is too large can increase memory usage, context switching, and pressure on downstream systems. I would distinguish CPU-bound and I/O-bound work, configure bounded queues and sensible limits, and use load tests to tune the pool based on latency, throughput, and resource utilization.

---

# Part 2: Payment Reliability, Java Streams, and URL Shortener Design

## 10. What happens if the payment succeeds but your service crashes before updating the order?

This is a very important production scenario because **a successful payment and a successful order update are two separate events**.

### Scenario

Suppose a customer purchases a product for ₹1,000.

1. The application creates an order with status `PENDING`.
2. The backend requests payment through a payment gateway.
3. The customer completes the payment successfully.
4. The payment gateway processes the payment.
5. The Spring Boot service crashes before updating the order to `PAID`.

The result might be:

```
Payment gateway: SUCCESS
Application database: PENDING
Customer: Money deducted
Order status: Not updated
```

This creates an inconsistent business state.

### Solution 1: Use payment webhooks

Payment providers can send server-to-server notifications about payment events.

A typical workflow is:

```
Customer
   |
   v
Spring Boot Backend
   |
   +----> Create Order: PENDING
   |
   +----> Payment Gateway
                 |
                 v
          Payment Successful
                 |
                 v
          Payment Webhook
                 |
                 v
          Verify Signature
                 |
                 v
          Update Payment and Order
```

The webhook allows the backend to learn about a payment even if the original request failed or the application crashed.

However, a webhook alone does not solve every problem. Notifications may be duplicated, delayed, or delivered out of order.

### Solution 2: Make webhook processing idempotent

**Idempotency** means that processing the same event more than once does not produce duplicate business effects.

For example, suppose the provider sends the same successful-payment event three times. The application must not create three successful payments or fulfill the same order three times.

A payment table could have fields such as:

```
payment_id
provider_payment_id
order_id
amount
status
created_at
updated_at
```

Create a database uniqueness constraint on the appropriate provider payment identifier. Depending on the provider's event model, you may also need a unique event ID.

Simplified processing logic:

```
@Transactional
public void processSuccessfulPayment(
        String providerPaymentId,
        Long orderId) {

    Payment payment = paymentRepository
            .findByProviderPaymentId(providerPaymentId)
            .orElseThrow();

    if (payment.getStatus() == PaymentStatus.SUCCESS) {
        return;
    }

    payment.setStatus(PaymentStatus.SUCCESS);

    Order order = orderRepository.findById(orderId)
            .orElseThrow();

    order.setStatus(OrderStatus.PAID);
}
```

This illustrates the basic idea, but **the status check alone is not sufficient under concurrency**. Production code should enforce uniqueness and use appropriate locking, conditional updates, or other concurrency controls.

It must also verify that the payment belongs to the correct order and that the amount and currency match expectations. Never trust an arbitrary order ID or payment status supplied by the client.

### Solution 3: Store events reliably

For more reliable event processing:

1. Receive the webhook.
2. Verify its authenticity.
3. Persist the event or enqueue it durably.
4. Acknowledge the webhook after durable acceptance, according to the provider's requirements.
5. Process the event.
6. Retry transient failures safely.

A transactional outbox can help when a database change must be coordinated with publishing an event. It does not magically create a distributed transaction with the payment provider.

### Solution 4: Reconcile payment and order statuses

A scheduled reconciliation job can compare internal payment records with the provider's records.

For example:

```
Order status: PENDING
Payment provider status: SUCCESS
```

The reconciliation process identifies this mismatch and safely corrects the local state.

### Interview-ready answer

> If the payment succeeds but the service crashes before updating the order, the payment provider and our database can temporarily have different states. I would use verified payment webhooks, idempotent event processing, database uniqueness constraints, and retry mechanisms. I would also implement reconciliation to identify payments that succeeded but were not reflected in our system. This prevents duplicate processing and helps recover from crashes.

**For your own interview:** If you have not personally implemented a payment gateway, explain this as a design scenario rather than claiming it was a defect you fixed in your project.

---

## 11. How would you find duplicate elements, element frequencies, or the second-highest value using Java Streams?

These are common Java 8 Streams interview questions.

### A. Find duplicate elements in a list

Input:

```
List<Integer> numbers =
        List.of(1, 2, 3, 2, 4, 1, 5, 3);
```

Expected duplicate values:

```
1, 2, 3
```

**Solution using `groupingBy()` and `counting()`:**

```
Map<Integer, Long> frequency = numbers.stream()
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()
        ));

List<Integer> duplicates = frequency.entrySet()
        .stream()
        .filter(entry -> entry.getValue() > 1)
        .map(Map.Entry::getKey)
        .toList();

System.out.println(duplicates);
```

Possible output:

```
[1, 2, 3]
```

The order of the output is not guaranteed because the intermediate map uses the default map implementation.

### B. Find duplicates while preserving first-appearance order

```
Set<Integer> seen = new HashSet<>();

List<Integer> duplicates = numbers.stream()
        .filter(number -> !seen.add(number))
        .distinct()
        .toList();

System.out.println(duplicates);
```

Output:

```
[2, 1, 3]
```

This is an illustrative approach for a sequential stream. The `HashSet` is mutable shared state, so **do not use this pattern with a parallel stream**.

### C. Count the frequency of each element

```
Map<Integer, Long> frequency = numbers.stream()
        .collect(Collectors.groupingBy(
                Function.identity(),
                Collectors.counting()
        ));

System.out.println(frequency);
```

Conceptual result:

```
1 -> 2
2 -> 2
3 -> 2
4 -> 1
5 -> 1
```

`Function.identity()` returns its input unchanged. Here, it means that each integer itself is used as the grouping key.

### D. Find the second-highest distinct number

Input:

```
List<Integer> numbers =
        List.of(10, 20, 30, 30, 40, 50);
```

Solution:

```
Optional<Integer> secondHighest = numbers.stream()
        .distinct()
        .sorted(Comparator.reverseOrder())
        .skip(1)
        .findFirst();

System.out.println(secondHighest.orElse(null));
```

Output:

```
40
```

Why?

1. `distinct()` removes duplicate values.
2. `sorted(Comparator.reverseOrder())` sorts in descending order.
3. `skip(1)` skips the highest distinct value.
4. `findFirst()` retrieves the next value.

For a large dataset, sorting all values may be unnecessarily expensive. A one-pass algorithm or an appropriate database query may be more efficient.

### E. Find the second-highest salary from employee objects

Assume `Employee` has a `getSalary()` method.

```
Optional<Integer> secondHighestSalary =
        employees.stream()
                .map(Employee::getSalary)
                .distinct()
                .sorted(Comparator.reverseOrder())
                .skip(1)
                .findFirst();
```

If the salaries are:

```
50000, 70000, 70000, 60000, 80000
```

The distinct descending salaries are:

```
80000, 70000, 60000, 50000
```

The answer is:

```
70000
```

### F. Find the maximum frequency element

```
Optional<Map.Entry<Integer, Long>> mostFrequent =
        frequency.entrySet()
                .stream()
                .max(Map.Entry.comparingByValue());
```

If multiple values have the same maximum frequency, this expression returns one of the tied entries; it does not define a meaningful tie-breaking rule.

### Interview tip

Always clarify what the interviewer means by **second-highest**:

- Second-highest element, counting duplicates?
- Second-highest distinct value?
- Second-highest salary per department?
- Second-highest value when fewer than two distinct values exist?

The correct implementation depends on that requirement.

---

# 12. How would you generate unique short URLs?

Imagine building a service similar to Bitly.

A user submits:

```
https://example.com/products/electronics/mobile/very-long-product-name
```

The service returns:

```
https://short.example/aB91xZ
```

When someone opens the short URL, the service redirects them to the original URL.

### Step 1: Define the data model

A basic database table could contain:

```
CREATE TABLE short_urls (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    short_code VARCHAR(20) NOT NULL UNIQUE,
    original_url VARCHAR(2048) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NULL
);
```

The unique constraint on `short_code` is essential: application-level checks alone cannot guarantee uniqueness under concurrent requests.

### Step 2: Generate a short code

One common approach is to use a unique numeric ID and encode it using Base62.

Base62 uses:

```
0-9
a-z
A-Z
```

That gives 62 possible characters for each position.

Example mapping:

```
ID  -> Base62 code
125 -> 21
126 -> 22
127 -> 23
```

These mappings are illustrative; the actual representation depends on the encoder.

A simple encoder:

```
public class Base62Encoder {

    private static final String ALPHABET =
            "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";

    public static String encode(long number) {
        if (number < 0) {
            throw new IllegalArgumentException(
                    "Number must be non-negative"
            );
        }

        if (number == 0) {
            return "0";
        }

        StringBuilder result = new StringBuilder();

        while (number > 0) {
            int remainder = (int) (number % 62);
            result.append(ALPHABET.charAt(remainder));
            number /= 62;
        }

        return result.reverse().toString();
    }
}
```

Example:

```
System.out.println(Base62Encoder.encode(125));
```

The numeric ID must first be generated uniquely, for example by a database sequence or another unique-ID mechanism.

### Step 3: Save the mapping

A simplified workflow is:

```
User submits original URL
          |
          v
Validate URL
          |
          v
Generate unique ID
          |
          v
Encode ID using Base62
          |
          v
Save code and original URL
          |
          v
Return short URL
```

If the ID is generated in the database, the application may need to insert the row first and then update the short code. Another option is to generate a unique ID before inserting.

### Step 4: Redirect when the short URL is opened

```
@GetMapping("/{code}")
public ResponseEntity<Void> redirect(
        @PathVariable String code) {

    ShortUrl shortUrl = shortUrlService.findByCode(code);

    return ResponseEntity
            .status(HttpStatus.FOUND)
            .location(URI.create(shortUrl.getOriginalUrl()))
            .build();
}
```

This is a simplified controller. The service must validate the code, handle missing or expired records, and ensure that the stored destination is safe to redirect to.

For a permanent redirect, HTTP `301` may be appropriate; for a temporary redirect, `302` or `307` may be appropriate depending on the desired behavior and method semantics.

### Base62 vs. random codes

|Approach|Advantages|Limitations|
|---|---|---|
|Sequential ID + Base62|Compact, deterministic, no random collision for unique IDs|Codes can be predictable and reveal approximate creation order|
|Random code|Harder to guess when generated securely|Collisions are possible and must be handled|
|Hash of original URL|Can support deterministic mapping|Long hashes need truncation; collisions and canonicalization require care|

**Security point:** Base62 encoding is not encryption. If predictability matters, use a suitable opaque-ID design and enforce authorization and rate limits where required.

---

## 13. How would you handle billions of URLs?

A service storing billions of short URLs needs a different design from a small application.

The main challenges are storage, read latency, code uniqueness, database capacity, traffic distribution, expiration, and operational reliability.

### A. Estimate storage requirements

Suppose the service stores 1 billion URL mappings.

If the average total storage cost per mapping were 500 bytes, the raw data estimate would be:

```
1,000,000,000 × 500 bytes
= 500,000,000,000 bytes
≈ 500 GB (decimal)
```

This is only a rough example. Actual storage depends on URL lengths, indexes, row overhead, metadata, replication, and database format.

Indexes and replicas can significantly increase the total storage requirement.

### B. Use a distributed storage architecture

A high-level design:

```
                  Clients
                     |
                     v
                Load Balancer
                     |
                     v
                API Instances
                 /        \
                v          v
             Cache       Database
                          Cluster
```

Possible components:

- **Load balancer:** Distributes traffic across application instances.
- **Stateless API instances:** Allow horizontal scaling.
- **Distributed cache:** Reduces database reads for frequently requested codes.
- **Partitioned database:** Distributes records across storage nodes.
- **Monitoring:** Tracks latency, error rates, cache hit ratio, and database health.

### C. Choose a partitioning strategy

A common approach is to partition by a hash of the short code.

Conceptually:

```
partition = hash(short_code) % partition_count
```

This distributes keys across partitions, although production partitioning must account for how partition counts and routing evolve.

Avoid naive partitioning that creates hotspots or makes rebalancing prohibitively expensive.

### D. Make ID generation scalable

A single central database sequence may become a bottleneck at very high creation rates.

Possible approaches include:

- Distributed unique-ID generators.
- Allocating ID ranges to application instances.
- Snowflake-style IDs.
- Random opaque codes with collision handling.

The right approach depends on creation rate, code length, predictability requirements, and operational complexity.

### E. Cache popular redirects

A short URL may be requested millions of times.

A cache such as Redis can reduce repeated database lookups:

```
Request short code
       |
       v
Check cache
   /       \
 HIT       MISS
  |          |
  v          v
Redirect   Query database
               |
               v
          Update cache
               |
               v
            Redirect
```

Cache expiration must respect the URL's expiration and any revocation or destination-update requirements.

### F. Plan for failure

At this scale, you should plan for:

- Database node failures.
- Cache outages.
- Network partitions.
- Hot keys.
- Traffic spikes.
- Backups and recovery.
- Replication lag.
- Monitoring and alerting.

**Interview point:** Billions of stored records do not automatically require billions of requests per second. Storage scale and traffic scale are separate capacity-planning problems.

### Interview-ready answer

> For billions of URLs, I would use stateless application instances behind a load balancer, a scalable database with a deliberate partitioning strategy, and caching for popular redirects. I would use a collision-safe unique-code generation strategy, monitor hot partitions and cache effectiveness, and plan for replication, backups, expiration, and failure recovery. I would also estimate storage and read/write traffic before selecting the infrastructure.

---

## 14. SQL vs. NoSQL — what is the difference?

SQL and NoSQL databases serve different data models and workload requirements. Neither is universally better.

### Comparison

|Feature|SQL databases|NoSQL databases|
|---|---|---|
|Data model|Relational tables|Document, key-value, wide-column, graph, or other models|
|Schema|Usually structured and defined|Depends on the database; often more flexible|
|Query language|Commonly SQL|Varies by database|
|Relationships|Supports joins and relational constraints|Depends on the data model; joins may be limited or handled differently|
|Transactions|Strong transaction support in many systems|Varies; some provide multi-record ACID transactions|
|Scaling|Vertical and horizontal options, depending on the system|Many systems are designed for distributed horizontal scaling|
|Typical use cases|Financial records, orders, inventory|Caching, flexible documents, high-volume key-value access, specific distributed workloads|

### When would you use SQL?

Consider an invoicing system containing:

```
Customer
Invoice
Invoice_Item
Payment
```

These records have relationships and business rules.

For example, an invoice must refer to a valid customer, and financial updates may require transactions.

A relational database such as MySQL or PostgreSQL is often a strong fit.

### When would you use NoSQL?

Suppose a system stores URL mappings:

```
{
  "shortCode": "aB91xZ",
  "originalUrl": "https://example.com/products/123",
  "createdAt": "2026-10-29T12:00:00Z",
  "expiresAt": null
}
```

A document or key-value store may be suitable if the access pattern is primarily retrieving a record by short code.

However, a relational database can also handle this use case very effectively. Choosing NoSQL simply because the application is large is not sufficient justification.

### Examples

- **SQL:** MySQL, PostgreSQL
- **NoSQL:** MongoDB, Redis, Apache Cassandra

### Interview-ready answer

> I choose SQL when the application requires relational data, joins, constraints, and transactional consistency, such as an invoicing or payment system. I consider NoSQL when its data model and access patterns fit the workload, such as flexible documents or high-volume key-value lookups. I make the decision based on consistency requirements, query patterns, scale, and operational complexity rather than assuming one database is always faster.

---

## 15. How would you handle expiration?

For a URL shortener, expiration means that a short URL should stop redirecting after its expiry time.

For example:

```
Short URL: https://short.example/aB91xZ
Expires:   2026-11-01 12:00:00 UTC
```

After the expiry time, the application should reject the redirect according to its defined behavior.

### A. Store an expiry timestamp

```
ALTER TABLE short_urls
ADD COLUMN expires_at TIMESTAMP NULL;
```

A `NULL` value can represent a URL that never expires, if that is the chosen policy.

### B. Check expiration during lookup

```
public ShortUrl getActiveUrl(String code) {
    ShortUrl shortUrl = repository.findByShortCode(code)
            .orElseThrow(
                    () -> new RuntimeException("URL not found")
            );

    Instant now = Instant.now();

    if (shortUrl.getExpiresAt() != null &&
            !shortUrl.getExpiresAt().isAfter(now)) {
        throw new RuntimeException("URL has expired");
    }

    return shortUrl;
}
```

This example assumes `expiresAt` is an `Instant`. In production, use application-specific exceptions and return an appropriate HTTP response.

For a redirect endpoint, an expired or unknown code might return `404 Not Found`, depending on the API contract.

### C. Delete expired records asynchronously

Checking expiration on every lookup prevents an expired URL from being used, but it does not remove old records.

A scheduled cleanup job can delete expired mappings:

```
DELETE FROM short_urls
WHERE expires_at IS NOT NULL
  AND expires_at <= CURRENT_TIMESTAMP;
```

For a large table, deleting every expired row in one transaction may cause excessive locking, transaction logs, or replication lag.

A production implementation should consider:

- Deleting records in batches.
- Indexing the expiry column appropriately.
- Partitioning by time when suitable.
- Using database-native TTL features when supported.
- Keeping deletion workloads separate from latency-sensitive redirect requests.

### D. Cache expiration matters too

Suppose Redis contains a cached URL that has expired in the database.

If the cache still returns it, the expired URL may continue working.

Therefore:

- Set cache TTL no later than the URL's expiry.
- Check expiration where necessary.
- Invalidate cached entries when URLs are revoked.
- Account for clock differences and update propagation.

### Interview-ready answer

> I would store an expiry timestamp and check it when resolving the short code. I would ensure cached entries cannot outlive the URL's expiry and use a scheduled, batched cleanup process to remove expired records. For a large system, I would monitor cleanup performance and consider partitioning or database-native TTL features if supported.

---

## 16. How would you prevent duplicate URLs?

First, clarify what “duplicate URL” means. There are at least two different cases.

**Case 1:** The same short code is accidentally assigned to multiple records.

**Case 2:** The same original URL is submitted repeatedly, and the business wants to reuse an existing short link.

These require different solutions.

### A. Prevent duplicate short codes

Use a database uniqueness constraint:

```
CREATE TABLE short_urls (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    short_code VARCHAR(20) NOT NULL UNIQUE,
    original_url VARCHAR(2048) NOT NULL
);
```

If a random short code collides with an existing code, generate another one and retry a bounded number of times.

The database constraint remains essential because two concurrent requests can both check for availability before either inserts.

### B. Reuse a short code for the same original URL

If the business wants one short code per original URL, a deterministic mapping or a unique key on a canonicalized URL may help.

However, this requires a clear definition of URL equivalence.

For example, are these equivalent?

```
https://example.com/products?id=10
https://example.com/products/?id=10
https://EXAMPLE.com/products?id=10
```

The answer depends on URL normalization rules and the origin server's behavior.

Do not blindly remove query parameters or trailing slashes: they may change the destination.

### C. Handle concurrent requests

A simple “check, then insert” approach is not race-safe:

```
Request A: checks URL -> not found
Request B: checks URL -> not found
Request A: inserts record
Request B: inserts duplicate
```

A safer design is to enforce the relevant unique constraint in the database and handle duplicate-key exceptions.

For example, a separate canonical-URL fingerprint table could enforce uniqueness:

```
canonical_url_hash UNIQUE
```

The application would still need to define canonicalization correctly and account for hash collisions. A hash alone is not mathematical proof that two URLs are identical.

### D. Decide whether duplicates are actually undesirable

Some URL shorteners intentionally allow the same original URL to have multiple short codes because different campaigns require separate analytics.

For example:

```
Campaign A -> short code A -> product page
Campaign B -> short code B -> product page
```

These links can track different sources independently.

### Interview-ready answer

> I would distinguish duplicate short codes from repeated submissions of the same original URL. For code uniqueness, I would enforce a database unique constraint and handle collisions safely. If the requirement is to reuse existing links, I would define URL canonicalization rules and enforce uniqueness on a suitable canonical key. I would also consider whether separate short links are required for analytics or campaign tracking.

---

## 17. How would you scale read-heavy traffic?

A read-heavy system receives significantly more read requests than write requests.

A URL shortener is a good example: creating a short URL may happen once, but the redirect endpoint may be called millions of times.

### A. Use caching

Cache frequently accessed URL mappings.

```
Client
  |
  v
Load Balancer
  |
  v
URL Service
  |
  v
Redis Cache
  |
  +---- Cache hit ----> Redirect
  |
  +---- Cache miss ---> Database
                          |
                          v
                      Cache result
                          |
                          v
                       Redirect
```

For a distributed cache, define:

- TTL.
- Cache invalidation behavior.
- Maximum memory usage.
- Eviction policy.
- Behavior during cache outages.
- Protection against cache stampedes.

### B. Scale application instances horizontally

Run multiple stateless Spring Boot instances behind a load balancer.

```
                Load Balancer
                /     |      \
               v      v       v
             App 1  App 2    App 3
                \     |      /
                 Shared Data
```

Because the application instances are stateless, additional instances can serve requests without requiring session affinity for ordinary redirect lookups.

### C. Optimize database reads

Depending on the database and workload, consider:

- Indexing `short_code`.
- Selecting only required columns.
- Avoiding unnecessary joins.
- Using read replicas where appropriate.
- Partitioning when data size and workload justify it.

Read replicas may introduce replication lag. If an operation needs to immediately observe a recent write, reading from a replica may not satisfy that requirement.

### D. Protect the system from hot keys

A popular short code may receive a disproportionate amount of traffic.

Possible techniques include:

- Caching the popular entry.
- Request coalescing on cache misses.
- Local in-memory caching where safe.
- Rate limiting abusive traffic.
- Monitoring per-key request patterns.

### E. Monitor performance

Track:

- Requests per second.
- p95 and p99 latency.
- Cache hit ratio.
- Database query latency.
- Error rate.
- CPU and memory.
- Queue length and connection-pool saturation.

### Interview-ready answer

> For read-heavy traffic, I would cache frequently accessed records, optimize indexed database lookups, and horizontally scale stateless application instances behind a load balancer. I would monitor cache hit ratio and database load, handle hot keys, and consider read replicas if the consistency requirements allow them. I would validate the design with load tests rather than relying on caching alone.

---

# Part 3: Production Debugging and High-Performance APIs

## 18. Explain one major production defect and how you fixed it.

This is a behavioral and technical interview question. The interviewer wants to understand how you investigate problems, make decisions, and prevent recurrence.

Since your experience includes Java backend development, invoice workflows, and third-party CO₂ API integration, you can use a relevant example **only if it accurately reflects something you experienced**.

Do not invent a production incident, its impact, or a performance improvement.

### What should your answer contain?

Use the STAR method:

|Component|What to explain|
|---|---|
|Situation|What was the system doing?|
|Task|What problem were you responsible for resolving?|
|Action|How did you investigate, identify the root cause, and fix it?|
|Result|What changed after the fix, and how did you verify it?|

### Example scenario: Third-party API failure during invoice processing

The following is an illustrative scenario, not a claim about what happened in your project.

**Situation:** An invoice workflow depends on a third-party API to retrieve CO₂ emissions data. Some invoice requests fail when the external service responds slowly or returns an error.

**Task:** Investigate the failure and ensure that the application handles external API errors correctly.

**Action:**

1. Reproduce the failure using an appropriate test request.
2. Check application logs and the external API's HTTP status and response.
3. Trace the request through the controller, service, and external API integration.
4. Identify the actual cause, such as a timeout, unexpected response, or invalid request.
5. Implement the appropriate fix, such as better response validation, timeout handling, or exception handling.
6. Test both successful responses and failure scenarios.
7. Verify that the error is logged appropriately and does not expose sensitive information.

**Result:** Explain the actual outcome you observed. If you did not measure a specific improvement, do not invent a percentage.

### Interview-ready answer template

Use this as a template and replace the bracketed sections with your real experience.

In my Java backend work, one issue I would describe is a defect involving [the actual problem you encountered].

My responsibility was to investigate the issue and identify its root cause. I first reproduced the problem and checked the application logs. Then I traced the request through the controller, service, and repository or external API integration to identify where the failure occurred.

After identifying the cause as [actual root cause], I implemented [actual fix] and tested both the normal flow and relevant failure scenarios.

After the fix, I verified [actual result or validation performed]. This experience reinforced the importance of systematic debugging, meaningful logging, and regression testing.

**Important:** If you haven't personally handled a production defect, say so honestly. You can instead describe a real development or testing defect you investigated. Interviewers value a clear troubleshooting process more than an exaggerated incident.

### Follow-up questions to prepare

- How did you reproduce the defect?
- Which logs did you check?
- How did you identify the root cause?
- How did you test the fix?
- What would you do if the defect occurred again?
- How would you prevent similar defects in the future?

---

## 19. How would you improve an API that receives 10,000 requests per second?

This is a system-design question. The interviewer is testing whether you can reason about throughput, latency, concurrency, database capacity, and reliability.

**First, 10,000 requests per second is a workload requirement, not a solution.** The appropriate architecture depends on what each request does.

An endpoint that reads a cached record is very different from one that updates a bank account or calls a slow external payment provider.

### Step 1: Understand the workload

Before choosing an architecture, ask:

1. Are the requests mostly reads or writes?
2. What is the target p95 and p99 latency?
3. How large are the requests and responses?
4. Does every request access a database?
5. Does the API call external services?
6. How many concurrent requests are expected?
7. What availability and consistency requirements apply?

For example, suppose the target is 10,000 requests per second with a p95 latency below 200 ms. Those are hypothetical requirements, not universal targets.

### Step 2: Measure the current bottleneck

Collect:

- Requests per second.
- p50, p95, and p99 latency.
- Error and timeout rates.
- CPU and memory utilization.
- GC pauses.
- Active application threads.
- Database query latency.
- Connection pool saturation.
- Cache hit ratio.

Use representative load tests to establish a baseline.

Do not assume that the database is the bottleneck before measuring it.

### Step 3: Scale application instances

A typical architecture is:

```
                 Clients
                    |
                    v
               Load Balancer
                    |
          +---------+---------+
          |         |         |
          v         v         v
       App 1      App 2      App 3
          |         |         |
          +---------+---------+
                    |
          +---------+---------+
          |                   |
          v                   v
       Cache                Database
```

Run multiple application instances and distribute incoming requests across them.

If the application is stateless, horizontal scaling is generally easier.

However, adding instances also increases potential demand on shared resources. The database, cache, message broker, and external APIs must be able to handle the resulting load.

### Step 4: Optimize the database

If database queries are slow or connection usage is high:

- Optimize SQL queries.
- Add appropriate indexes.
- Avoid N+1 queries.
- Return only required fields.
- Use pagination for large result sets.
- Keep transactions short.
- Tune the connection pool based on measured database capacity.
- Consider read replicas for suitable read workloads.

For write-heavy endpoints, examine transaction contention, lock waits, and write amplification.

**Important:** A pool of 100 connections per instance across 20 instances could allow up to 2,000 application connections. That might exceed the database's capacity. Pool settings must be considered across the entire deployment.

### Step 5: Add caching for suitable reads

If requests repeatedly retrieve the same data, caching can reduce database load.

```
Request
   |
   v
Application
   |
   v
Cache
  / \
Hit  Miss
 |     |
 v     v
Return Database
          |
          v
       Cache data
          |
          v
        Return
```

Caching is useful when the data's consistency requirements permit it.

Do not cache sensitive or user-specific data without designing appropriate cache keys, authorization checks, and invalidation rules.

### Step 6: Control concurrency

Configure application thread pools, database pools, timeouts, and request limits based on measurements.

For example:

- Bound queues so requests cannot accumulate indefinitely.
- Set appropriate connection acquisition timeouts.
- Limit concurrent calls to downstream services.
- Apply rate limiting or admission control when capacity is exhausted.
- Reject excess work predictably rather than allowing cascading failures.

Increasing the thread count alone may not improve throughput. It can instead overload the database or external services.

### Step 7: Handle slow dependencies

Suppose your API calls a third-party service that takes 3 seconds to respond.

If many requests are waiting for that dependency, application threads and connections can remain occupied for too long.

Potential improvements include:

- Appropriate connect and response timeouts.
- Bounded concurrency for downstream calls.
- Retries only for suitable transient failures.
- Exponential backoff and jitter.
- Circuit breakers where appropriate.
- Asynchronous processing for work that does not need to finish before the response.
- Durable queues for important background work.

Retries must be designed carefully. Retrying non-idempotent payment operations without appropriate protections can cause duplicate business effects.

### Step 8: Load-test and validate

Test the system at progressively higher load levels.

Measure:

|Metric|What to look for|
|---|---|
|Throughput|Can the API sustain the required request rate?|
|p95/p99 latency|Does latency remain within the target?|
|Error rate|Are timeouts or failures increasing?|
|CPU|Are application instances CPU-bound?|
|GC|Are pauses or allocation rates problematic?|
|Database|Are queries, locks, or connections saturated?|
|Cache|Is caching reducing database load?|
|Recovery|Does the service recover after overload or dependency failure?|

Also test a sustained workload, traffic spikes, and relevant failure scenarios—not just a brief burst of requests.

### Interview-ready answer

If an API needs to handle 10,000 requests per second, I would first understand the workload and establish performance metrics, including throughput, p95 and p99 latency, error rate, CPU usage, and database latency.

I would identify the bottleneck before making changes. Depending on the findings, I might horizontally scale stateless Spring Boot instances behind a load balancer, optimize database queries and indexes, introduce caching for frequently accessed data, and tune thread pools and database connections.

I would also configure timeouts, bounded concurrency, rate limiting, and appropriate failure-handling mechanisms to protect downstream services.

Finally, I would load-test the system under realistic conditions and verify that it meets the throughput and latency targets without compromising data consistency or reliability.

---

# Final Revision Sheet

Use this table to revise the 19 questions before an interview.

|No.|Topic|Key concepts to remember|
|---|---|---|
|1|REST API optimization|Metrics, SQL, indexes, pagination, caching|
|2|`groupingBy()` and `partitioningBy()`|Multiple groups vs. boolean partitions|
|3|Sequential vs. parallel streams|Overhead, concurrency, ordering|
|4|REST API optimization|Measure, optimize, benchmark|
|5|Garbage Collection|Heap, allocation, pauses, memory leaks|
|6|Avoiding `parallelStream()`|Small workloads, blocking I/O, shared state|
|7|Transaction consistency|ACID, `@Transactional`, locking, constraints|
|8|Connection pool exhaustion|Slow queries, long transactions, HikariCP|
|9|Thread-pool configuration|Concurrency, queueing, CPU and I/O|
|10|Payment succeeds but order update fails|Webhooks, idempotency, reconciliation|
|11|Java Streams problems|Duplicates, frequency, second-highest|
|12|Short URL generation|Unique IDs, Base62, database constraints|
|13|Billions of URLs|Partitioning, caching, storage, availability|
|14|SQL vs. NoSQL|Data model, access patterns, consistency|
|15|Expiration|Timestamps, cache TTL, cleanup|
|16|Prevent duplicate URLs|Unique constraints, normalization, concurrency|
|17|Read-heavy traffic|Caching, horizontal scaling, read replicas|
|18|Production defect|STAR, root cause, fix, verification|
|19|10,000 requests/second|Capacity planning, bottlenecks, load testing|

## What you should prioritize

For your Java backend interviews, I recommend studying these topics in this order:

**Priority 1 — Core interview questions**

1. Java Streams: duplicates, frequency, grouping, and second-highest value.
2. REST API optimization.
3. `@Transactional`, transaction consistency, and concurrent updates.
4. Connection pool exhaustion and thread pools.
5. Payment failure scenarios and idempotency.

**Priority 2 — Performance and system design**  
6. Garbage Collection.  
7. Sequential versus parallel streams.  
8. Caching and read-heavy traffic.  
9. URL shortener design.  
10. Scaling an API to 10,000 requests per second.

**Priority 3 — Behavioral and architecture**  
11. Production defect troubleshooting.  
12. SQL versus NoSQL.  
13. Expiration and duplicate prevention.  
14. Scaling URL storage to billions of records.

**My recommendation:** Don't memorize all 19 answers word for word. For each question, learn the concept, understand the example, and practice explaining the solution in 60–90 seconds. For scenario-based questions, clearly distinguish what you have personally implemented from what you understand conceptually.