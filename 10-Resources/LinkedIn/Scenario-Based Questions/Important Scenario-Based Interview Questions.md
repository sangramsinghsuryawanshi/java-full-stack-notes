## **1. Your Java application suddenly starts throwing** `OutOfMemoryError`**. How will you debug it?**

### **What is** `OutOfMemoryError`**?**

`OutOfMemoryError` is a JVM error that occurs when the JVM cannot allocate memory as required.

It can happen for different reasons, not just because the Java heap is full.

Common examples:

|Error|Meaning|
|---|---|
|`Java heap space`|The JVM cannot allocate an object in the Java heap|
|`GC overhead limit exceeded`|The JVM spends excessive time performing GC while reclaiming very little memory|
|`Metaspace`|Metaspace memory for class metadata is exhausted|
|`Direct buffer memory`|Direct buffer allocation cannot obtain sufficient native memory|
|`unable to create native thread`|The operating system or JVM cannot create another native thread|

### **Step 1: Check application logs**

First, identify the exact error.

Example:

```
java.lang.OutOfMemoryError: Java heap space
```

Check:

- When did the error start?
    
- Which API or background job was running?
    
- Did traffic or data volume increase?
    
- Did a recent deployment introduce a change?
    
- Is the error recurring or happening only once?
    

The exact error message helps determine which memory area to investigate.

### **Step 2: Check JVM memory usage**

Useful JVM tools include:

`jcmd`

```
jcmd <PID> GC.heap_info
```

Displays heap information for the target JVM.

`jstat`

```
jstat -gcutil <PID> 1000
```

Displays GC utilization statistics every 1,000 milliseconds.

`jcmd` **heap dump**

```
jcmd <PID> GC.heap_dump /tmp/heapdump.hprof
```

Captures a heap dump for analysis. Heap dumps can be large and may temporarily affect application performance, so capture them carefully in production.

### **Step 3: Analyze the heap dump**

Use tools such as:

- Eclipse Memory Analyzer (MAT)
    
- VisualVM
    
- Java Mission Control, where appropriate
    

Look for:

1. Objects consuming the most heap memory.
    
2. Collections that grow continuously.
    
3. Objects retained unexpectedly.
    
4. Large byte arrays and strings.
    
5. Static references preventing object collection.
    
6. Dominator trees and paths to GC roots.
    

**Example: Potential memory leak**

```
public class InvoiceCache {

    private static final List<Invoice> CACHE =
            new ArrayList<>();

    public void addInvoice(Invoice invoice) {
        CACHE.add(invoice);
    }
}
```

This collection keeps references to every added invoice. If entries are never removed and the list keeps growing, those objects remain reachable and cannot be garbage-collected.

A possible improvement is to use a bounded cache with a deliberate eviction policy and expiration.

### **Step 4: Check other memory areas**

If the error is not `Java heap space`, investigate the relevant memory area.

For example:

- Metaspace: class loading, classloader leaks, dynamically generated classes.
    
- Direct memory: direct buffers and native libraries.
    
- Native threads: thread count, thread stacks, OS limits.
    
- Container memory: JVM memory settings versus the container's memory limit.
    

### **Step 5: Apply the correct fix**

Possible fixes:

|Root cause|Possible fix|
|---|---|
|Memory leak|Release unnecessary references|
|Unbounded collection|Introduce limits or eviction|
|Large database result|Use pagination or streaming carefully|
|Large file processing|Process data in chunks|
|Excessive object creation|Reduce unnecessary allocations|
|Metaspace growth|Investigate class loading and classloader retention|
|Too many threads|Correct thread-pool configuration|
|Insufficient heap for legitimate workload|Adjust heap after validating the memory budget|

### **Example: Avoid loading all records**

Problem:

```
List<Invoice> invoices = invoiceRepository.findAll();
```

If the database contains millions of invoices, loading them all into memory can create substantial heap pressure.

Better:

```
Pageable pageable = PageRequest.of(0, 500);

Page<Invoice> invoices =
        invoiceRepository.findAll(pageable);
```

Process each page and fetch subsequent pages as needed.

### **Interview-ready answer**

> First, I would check the exact OutOfMemoryError message and application logs to understand which memory area is exhausted. Then I would inspect JVM memory and GC metrics and capture a heap dump if it is safe to do so. Using Eclipse MAT or VisualVM, I would identify objects consuming memory and check whether they are retained unexpectedly. I would investigate causes such as memory leaks, unbounded collections, large database results, or excessive thread creation. After identifying the root cause, I would fix it and monitor heap usage and GC behavior to verify the improvement.

Key interview point

Increasing `-Xmx` may temporarily help, but it does not fix a memory leak.

## **2. You see high CPU usage but low traffic. What could be the reason?**

High CPU usage with low incoming traffic suggests that the application may be spending too much time performing internal work.

It is not necessarily a traffic problem.

### **Possible causes**

|Cause|Explanation|
|---|---|
|Infinite loop|A thread continuously executes without completing|
|Inefficient algorithm|Expensive operations consume CPU|
|Excessive GC|JVM spends CPU time reclaiming memory|
|Busy waiting|Threads repeatedly check a condition|
|Thread contention|Threads repeatedly compete for locks|
|Regex complexity|A problematic regular expression consumes excessive CPU|
|Background jobs|Scheduled tasks or batch processing consume CPU|
|Serialization|Large or repeated serialization work consumes CPU|
|Excessive logging|Heavy formatting or logging can consume resources|

### **Step 1: Identify the process**

On Linux:

```
top
```

Or:

```
ps -p <PID> -o %cpu,%mem,cmd
```

This confirms whether the Java process is consuming the CPU.

### **Step 2: Identify the high-CPU thread**

Get the Java process ID:

```
jps -l
```

Take a thread dump:

```
jcmd <PID> Thread.print
```

On Linux, use:

```
top -H -p <PID>
```

This displays CPU usage by individual threads.

### **Step 3: Match the thread to the thread dump**

Suppose `top -H` shows a high-CPU thread with Linux thread ID `12345`.

Convert the ID to hexadecimal:

```
printf "%x\n" 12345
```

Suppose the result is:

```
3039
```

Search the thread dump for:

```
nid=0x3039
```

The matching thread stack trace helps identify the Java code executing on that thread.

Take multiple thread dumps a few seconds apart. A single snapshot may not reveal whether a thread is continuously consuming CPU.

### **Step 4: Identify the root cause**

Example of an infinite loop:

```
public void process() {
    while (true) {
        // No exit condition
    }
}
```

This can continuously consume CPU.

Another example is inefficient nested-loop processing:

```
for (Invoice invoice : invoices) {
    for (Customer customer : customers) {
        if (invoice.getCustomerId()
                .equals(customer.getId())) {
            // Process match
        }
    }
}
```

For large collections, this can become expensive.

An improvement might be to index customers by ID:

```
Map<Long, Customer> customerMap =
        customers.stream()
                .collect(Collectors.toMap(
                        Customer::getId,
                        Function.identity()
                ));

for (Invoice invoice : invoices) {
    Customer customer =
            customerMap.get(invoice.getCustomerId());

    if (customer != null) {
        // Process match
    }
}
```

This changes repeated linear searching into average constant-time map lookups, assuming suitable hash distribution.

### **Step 5: Check GC and background tasks**

If CPU usage is caused by GC, examine GC logs and allocation rates.

Also check:

- Scheduled jobs.
    
- Batch processing.
    
- Async executors.
    
- Retry loops.
    
- Message consumers.
    
- Health checks and monitoring tasks.
    

### **Interview-ready answer**

> If CPU usage is high despite low traffic, I would identify whether the Java process or another process is responsible. Then I would identify high-CPU threads using OS tools and match their thread IDs to Java thread dumps. I would investigate infinite loops, inefficient algorithms, busy waiting, background jobs, and excessive garbage collection. Once the root cause is identified, I would fix the code or workload and verify CPU utilization under the same conditions.

## **3. A thread is stuck in** `BLOCKED` **state. How will you identify and fix it?**

### **What is** `BLOCKED` **state?**

In Java, a thread enters `BLOCKED` state when it is waiting to acquire a monitor lock that another thread currently holds.

For example:

```
public class SharedResource {

    public synchronized void process() {
        System.out.println(
                Thread.currentThread().getName()
                        + " processing"
        );

        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
}
```

Suppose Thread-1 enters `process()` and holds the object's monitor lock.

Thread-2 calls the same synchronized method on the same object.

Thread-2 must wait until Thread-1 releases the lock. Thread-2 can therefore enter `BLOCKED` state.

**Thread execution**

Thread-1

Acquires monitor lock → executes synchronized method → releases lock

Thread-2

Tries to acquire the same lock → waits in `BLOCKED` state

### **Step 1: Take a thread dump**

```
jcmd <PID> Thread.print
```

Or:

```
jstack <PID>
```

Look for a thread showing:

```
java.lang.Thread.State: BLOCKED
```

You may see details similar to:

```
"Thread-2" BLOCKED
    waiting to lock <0x00000001>
    which is held by "Thread-1"
```

This indicates a monitor-lock contention problem.

### **Step 2: Identify the lock owner**

Check:

- Which thread owns the lock?
    
- Which method is holding it?
    
- How long has it been held?
    
- Is the thread performing a slow database or network operation while holding the lock?
    
- Are multiple threads waiting for the same lock?
    

Take multiple thread dumps to understand whether the condition is temporary or persistent.

### **Step 3: Identify the root cause**

Common reasons include:

1. A synchronized method performs slow operations.
    
2. A thread holds a lock while calling an external API.
    
3. Multiple locks are acquired in an inconsistent order.
    
4. A critical section contains unnecessary processing.
    
5. Lock contention is high because many requests access the same shared object.
    

### **Step 4: Fix the problem**

**Solution A: Reduce the synchronized section**

Problem:

```
public synchronized void processInvoice() {
    // Database call
    // External API call
    // Business calculations
}
```

This holds the monitor for the entire method.

Better approach: perform independent slow operations outside the critical section and synchronize only the shared-state update that actually needs protection.

**Solution B: Use** `ReentrantLock` **when appropriate**

```
private final ReentrantLock lock =
        new ReentrantLock();

public void process() {
    lock.lock();

    try {
        // Critical section
    } finally {
        lock.unlock();
    }
}
```

`ReentrantLock` also supports features such as timed lock acquisition and interruptible acquisition. It does not automatically eliminate contention.

**Solution C: Avoid nested locks**

Consider this situation:

```
Thread-1:
  Lock A → waits for Lock B

Thread-2:
  Lock B → waits for Lock A
```

This can create a deadlock.

Use a consistent lock-acquisition order or redesign the shared-resource access.

### **Important: BLOCKED vs WAITING vs TIMED_WAITING**

|State|Meaning|Example|
|---|---|---|
|`BLOCKED`|Waiting to acquire a monitor lock|Entering a synchronized method|
|`WAITING`|Waiting indefinitely for another thread's action|`Object.wait()`, `Thread.join()` without timeout|
|`TIMED_WAITING`|Waiting for a limited period|`Thread.sleep()`, timed `join()`|

A thread in `BLOCKED` state is not necessarily deadlocked. It may simply be waiting for a lock that will soon be released.

### **Interview-ready answer**

> I would first take a thread dump using `jcmd` or `jstack` and identify threads in BLOCKED state. Then I would inspect the lock they are waiting for and identify which thread owns it. I would examine the owner's stack trace to understand why it is holding the lock. Common causes include long synchronized methods, slow external calls inside critical sections, and nested locks. I would reduce the critical section, avoid unnecessary locking, and establish a consistent lock order. Finally, I would take another thread dump and verify that the blocked threads are progressing.

## **4. Your application slows down after running for a few hours. What will you check?**

This scenario is important because the application initially works correctly but gradually becomes slower.

It often indicates a problem involving memory, resource usage, connection pools, threads, or background processing.

### **Possible causes**

|Potential cause|What happens|
|---|---|
|Memory leak|Objects accumulate and remain reachable|
|Increasing GC activity|JVM spends more time collecting garbage|
|Database connection exhaustion|Requests wait for connections|
|Thread-pool exhaustion|Tasks wait for available worker threads|
|Growing cache|Memory usage increases over time|
|Slow database queries|Queries become slower as data grows|
|Unclosed resources|Connections, files, or other resources remain occupied|
|Message backlog|Consumers cannot keep up with incoming messages|
|Lock contention|Threads spend more time waiting|
|External service slowdown|Requests spend longer waiting on dependencies|

### **Step 1: Compare initial and current performance**

Collect metrics from:

- Application startup.
    
- A period when performance is healthy.
    
- The time when performance starts degrading.
    

Compare:

|Metric|What to investigate|
|---|---|
|CPU usage|Is CPU increasing over time?|
|Heap usage|Is memory steadily increasing?|
|GC pauses|Are pauses becoming longer or more frequent?|
|Thread count|Is the number of threads continuously growing?|
|Active DB connections|Is the pool approaching its limit?|
|Pending DB connections|Are requests waiting?|
|API latency|Which endpoints are slowing down?|
|Queue depth|Is background work accumulating?|

The trend matters more than one isolated metric.

### **Step 2: Investigate memory growth**

Use:

```
jcmd <PID> GC.heap_info
```

Check GC metrics and capture heap dumps at suitable points if necessary.

If heap usage remains high after garbage collection and continues increasing, investigate retained objects.

For example:

```
private static final Map<String, Object> CACHE =
        new HashMap<>();
```

If the map grows indefinitely, memory consumption may gradually increase.

Possible fixes include:

- Bounded caches.
    
- Expiration policies.
    
- Correct cleanup.
    
- Avoiding unnecessary object retention.
    

### **Step 3: Investigate garbage collection**

Enable GC logging:

```
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```

Look for:

- Increasing GC frequency.
    
- Longer pause times.
    
- High allocation rates.
    
- Repeated full GC activity.
    
- Heap occupancy after collection.
    

A high GC frequency may be a symptom of allocation pressure, a small heap relative to the live data, or another memory issue.

### **Step 4: Investigate connection pools**

For Spring Boot applications using HikariCP, check:

- Active connections.
    
- Idle connections.
    
- Pending connection requests.
    
- Connection acquisition timeouts.
    
- Long-running transactions.
    

A database connection pool may become exhausted when transactions remain open too long or queries take longer than expected.

### **Step 5: Investigate threads**

Take a thread dump:

```
jcmd <PID> Thread.print
```

Check:

- Growing thread counts.
    
- Threads stuck in blocking calls.
    
- Deadlocks.
    
- Lock contention.
    
- Executor queue sizes.
    
- Tasks that never complete.
    

A thread dump is a snapshot, so compare multiple dumps if you suspect a persistent issue.

### **Step 6: Investigate the database**

Use database monitoring and query execution plans to identify:

- Slow queries.
    
- Missing or ineffective indexes.
    
- Lock waits.
    
- Increasing table sizes.
    
- Long-running transactions.
    
- Database CPU or I/O pressure.
    

### **Step 7: Check external dependencies**

If your application calls third-party APIs, examine:

- Response times.
    
- Timeout rates.
    
- Retry frequency.
    
- Circuit-breaker state.
    
- Connection reuse.
    
- Whether slow dependencies are occupying application threads.
    

### **Step 8: Verify the fix**

After identifying the cause:

1. Apply the fix.
    
2. Run load tests.
    
3. Monitor for several hours.
    
4. Compare memory, CPU, latency, and pool utilization.
    
5. Verify that the degradation does not return.
    

### **Interview-ready answer**

> If an application slows down after running for a few hours, I would compare its performance metrics from startup and the degraded period. I would check heap growth, GC frequency and pauses, thread counts, database connection pool usage, slow queries, and background queues. I would use GC logs, heap dumps, thread dumps, and application metrics to identify the root cause. After fixing the issue, I would run a sustained load test and monitor the application to ensure performance remains stable over time.

Key interview point

Don't restart the application immediately and assume the problem is fixed. A restart may temporarily clear symptoms while hiding the underlying defect.

## **5. You are facing frequent GC pauses. How will you optimize it?**

### **First, understand what causes GC pauses**

Garbage Collection reclaims memory occupied by unreachable objects.

Some GC operations require application threads to pause. These are often called Stop-The-World (STW) pauses.

Frequent or long pauses can affect:

- API response time.
    
- p95 and p99 latency.
    
- Throughput.
    
- Request timeouts.
    
- Overall application responsiveness.
    

### **Step 1: Check GC logs**

Enable GC logging on Java 21:

```
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```

Look for:

- How frequently GC runs.
    
- Pause duration.
    
- Young GC and old-generation activity.
    
- Heap occupancy before and after collection.
    
- Allocation rate.
    
- Full GC occurrences.
    

For example, these are hypothetical observations:

```
GC pause: 10 ms
GC pause: 15 ms
GC pause: 20 ms
GC pause: 850 ms
```

The 850 ms pause deserves investigation, especially if it correlates with API latency spikes.

### **Step 2: Identify excessive object allocation**

Consider:

```
public void processInvoices(List<Invoice> invoices) {

    for (Invoice invoice : invoices) {

        String message =
                "Processing invoice: "
                        + invoice.getInvoiceNumber();

        System.out.println(message);
    }
}
```

This is a simple illustration of object creation. In real applications, larger allocation sources may include:

- Repeated DTO conversions.
    
- Large JSON serialization/deserialization.
    
- Temporary collections.
    
- Large strings.
    
- Excessive object mapping.
    
- Unnecessary intermediate Stream operations.
    

Use Java Flight Recorder or a profiler to identify allocation hotspots rather than guessing.

### **Step 3: Reduce unnecessary object allocation**

For example, avoid loading huge datasets into memory:

```
List<Invoice> invoices = repository.findAll();
```

Instead, process smaller batches.

```
Pageable pageable = PageRequest.of(0, 500);

Page<Invoice> page =
        repository.findAll(pageable);
```

For a complete batch-processing implementation, iterate through pages and process each page before loading the next.

### **Step 4: Fix memory leaks**

A memory leak can cause the live heap to grow over time.

Investigate:

- Static collections.
    
- Unbounded caches.
    
- Unreleased references.
    
- ThreadLocal values that are not cleaned up.
    
- Classloader retention.
    
- Listener registrations that are never removed.
    

Use heap dumps and GC-root analysis to identify why objects remain reachable.

### **Step 5: Configure heap size appropriately**

Example:

```
java -Xms2g -Xmx2g -jar application.jar
```

Here:

- `-Xms2g` sets the initial heap size.
    
- `-Xmx2g` sets the maximum heap size.
    

These values are examples, not recommendations for every application.

Also consider container memory limits, native memory, thread stacks, and other JVM overhead. The heap must fit within the total memory budget.

Increasing the heap can reduce some collection pressure, but it may also increase the amount of work involved in certain collections and does not fix a memory leak.

### **Step 6: Select an appropriate garbage collector**

Java 21 provides multiple garbage collectors.

|Garbage collector|General characteristics|
|---|---|
|Serial GC|Simple, typically suited to smaller workloads|
|Parallel GC|Focuses on throughput|
|G1 GC|Balanced throughput and pause-time goals; commonly used|
|ZGC|Low-latency collector designed to keep pauses very short|

For example, to select G1 explicitly:

```
java -XX:+UseG1GC -jar application.jar
```

To select ZGC on Java 21:

```
java -XX:+UseZGC -jar application.jar
```

Collector selection depends on the application's latency targets, heap size, workload, and deployment environment. Benchmark with realistic workloads before changing production settings.

### **Step 7: Monitor after optimization**

Compare before and after:

- GC pause duration.
    
- GC frequency.
    
- Allocation rate.
    
- Heap occupancy after collection.
    
- API p95 and p99 latency.
    
- CPU usage.
    
- Throughput.
    

The goal is not to eliminate all GC activity. It is to keep memory management within acceptable performance limits.

### **Interview-ready answer**

> First, I would enable GC logging and analyze pause duration, collection frequency, heap occupancy, and allocation rates. Then I would use Java Flight Recorder or a profiler to identify excessive object allocation and investigate memory leaks using heap dumps if necessary. I would optimize unnecessary allocations, use pagination or batch processing for large datasets, and configure heap size based on the available memory. If required, I would evaluate G1 or ZGC based on the application's latency requirements and verify the improvement through load testing.

# **Practical Java troubleshooting commands — quick reference**

These are useful commands to practice with your JDK 21 installation.

|Purpose|Command|
|---|---|
|Find Java processes|`jps -l`|
|Heap information|`jcmd <PID> GC.heap_info`|
|Capture heap dump|`jcmd <PID> GC.heap_dump heapdump.hprof`|
|Capture thread dump|`jcmd <PID> Thread.print`|
|GC statistics|`jstat -gcutil <PID> 1000`|
|JVM flags|`jcmd <PID> VM.flags`|
|JVM command line|`jcmd <PID> VM.command_line`|

Run these commands from a terminal where the JDK bin directory is available. Replace PID with the actual Java process ID. Some commands may require appropriate permissions.

## **6. A HashMap is causing performance issues under heavy load. Why?**

**Question:** Your Java application uses a `HashMap`, but performance degrades when many operations happen concurrently. What could be the reason?

### **Possible reasons**

1. **Hash collisions:** Multiple keys map to the same bucket, increasing lookup cost.
    
2. **Poor** `hashCode()` **implementation:** Many objects produce the same or similar hash values.
    
3. **Frequent resizing:** The map repeatedly grows as elements are added.
    
4. **Concurrent access:** `HashMap` is not thread-safe. Concurrent modification can cause inconsistent behavior or data corruption.
    
5. **Large map size:** Excessive memory usage and resizing can affect performance.
    

### **Important: HashMap vs ConcurrentHashMap**

|Feature|HashMap|ConcurrentHashMap|
|---|---|---|
|Thread-safe|No|Yes|
|Concurrent reads|Not guaranteed safe during concurrent writes|Supported|
|Concurrent updates|Unsafe|Supported|
|Null keys|One|Not allowed|
|Null values|Allowed|Not allowed|

### **Example: Unsafe shared HashMap**

```
private static final Map<String, Integer> counter =
        new HashMap<>();

public void increment(String key) {
    counter.put(key, counter.getOrDefault(key, 0) + 1);
}
```

Two threads can read the same old value and overwrite each other's updates.

### **Solution**

For concurrent access, use `ConcurrentHashMap`:

```
private static final Map<String, Integer> counter =
        new ConcurrentHashMap<>();

public void increment(String key) {
    counter.merge(key, 1, Integer::sum);
}
```

`merge()` performs the update atomically for that key.

**Interview answer:**

"I would first identify whether the problem is caused by hash collisions, frequent resizing, excessive map size, or concurrent access. I would examine the key's `hashCode()` and `equals()` implementations and profile the application. If multiple threads need to access the map, I would consider `ConcurrentHashMap` and atomic operations such as `merge()` rather than using a regular `HashMap`."

## **7. Multiple threads are updating shared data incorrectly. How will you fix it?**

**Question:** Multiple threads update the same variable, but the final result is incorrect. Why does this happen?

### **Root cause: Race condition**

A race condition occurs when multiple threads access shared mutable data and the result depends on the timing of their operations.

Example:

```
public class Counter {

    private int count = 0;

    public void increment() {
        count++;
    }
}
```

If two threads execute `count++` simultaneously, an update can be lost.

Why?

`count++` is not one indivisible operation. Conceptually, it performs:

1. Read the current value.
    
2. Add one.
    
3. Write the new value.
    

### **Solutions**

**Option 1: synchronized**

```
public class Counter {

    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

Only one thread at a time can execute a synchronized instance method on the same object.

**Option 2: AtomicInteger**

```
import java.util.concurrent.atomic.AtomicInteger;

public class Counter {

    private final AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }

    public int getCount() {
        return count.get();
    }
}
```

**Option 3: ReentrantLock**

Use it when you need explicit lock control, such as timed lock acquisition or interruptible locking.

### **Which should you choose?**

|Approach|Suitable situation|
|---|---|
|`synchronized`|Simple critical sections|
|`AtomicInteger`|Atomic updates to a single variable|
|`ReentrantLock`|Advanced lock-control requirements|
|Concurrent collections|Shared collection access|

**Interview answer:**

"I would identify the shared mutable state and check whether multiple threads are updating it without synchronization. Depending on the requirement, I would use `synchronized`, atomic classes such as `AtomicInteger`, or appropriate concurrent collections. I would also avoid unnecessary shared mutable state."

## **8. Your API works fine locally but fails in production. What will you investigate?**

**Question:** An API works correctly on your local machine but returns errors or behaves differently after deployment.

### **Possible reasons**

- Different Java or dependency versions.
    
- Incorrect environment variables or configuration.
    
- Database connection or credential issues.
    
- Production network restrictions or firewall rules.
    
- Incorrect API URLs or third-party credentials.
    
- Different database schema or data.
    
- Insufficient memory or CPU.
    
- Port, proxy, DNS, or load-balancer configuration.
    
- File permissions or missing files.
    
- Production traffic exposing concurrency or timeout issues.
    

### **Investigation approach**

1. **Check the exact error**
    
    Inspect application logs, HTTP status codes, stack traces and timestamps.
    
2. **Compare environments**
    
    Compare Java version, Spring profiles, environment variables, dependencies and configuration.
    
3. **Check connectivity**
    
    Verify database, external API, DNS, network, firewall and credentials.
    
4. **Inspect production resources**
    
    Check CPU, memory, disk, thread pools, database connections and container limits.
    
5. **Reproduce safely**
    
    Use production-like configuration and test data in a controlled environment.
    
6. **Apply and verify a fix**
    
    Deploy a controlled change, monitor error rates and latency, and keep rollback available.
    

### **Spring Boot example: Environment-specific configuration**

```
spring:
  profiles:
    active: prod
```

You might maintain separate configurations such as:

```
application-dev.yml
application-test.yml
application-prod.yml
```

Sensitive values should be supplied securely through environment variables or a secrets manager, not hardcoded.

**Interview answer:**

"I would compare local and production environments, starting with logs and the exact exception. I would verify application configuration, Java and dependency versions, database connectivity, external services, network access and resource limits. Once I identify the root cause, I would reproduce it in a production-like environment, apply a controlled fix and monitor the deployment."

## **9. You suspect a memory leak. How will you confirm it?**

**Question:** Your application memory usage continuously increases, even when traffic is relatively stable.

### **What is a memory leak?**

A memory leak occurs when an application retains references to objects that are no longer needed, preventing the garbage collector from reclaiming them.

### **Common causes**

- Static collections that grow continuously.
    
- Unbounded caches.
    
- Unremoved `ThreadLocal` values.
    
- Listeners or callbacks that are never unregistered.
    
- Incorrect object lifecycle management.
    
- Classloader leaks in application servers.
    
- Objects retained by long-lived references.
    

### **Example**

```
public class UserCache {

    private static final List<User> users = new ArrayList<>();

    public void addUser(User user) {
        users.add(user);
    }
}
```

If users are continuously added and never removed, the list can grow indefinitely.

### **How to confirm the leak**

**Step 1: Monitor memory**

```
jcmd <PID> GC.heap_info
```

**Step 2: Monitor garbage collection**

```
jstat -gcutil <PID> 1000
```

Observe whether heap usage after garbage collection keeps increasing.

**Step 3: Capture heap dump**

```
jcmd <PID> GC.heap_dump heapdump.hprof
```

**Step 4: Analyze the heap dump**

Use Eclipse Memory Analyzer (MAT), VisualVM or Java Mission Control.

Look for:

- Objects consuming large amounts of memory.
    
- Dominator tree.
    
- Retained heap.
    
- Paths to GC roots.
    
- Collections growing unexpectedly.
    

[Basic Concepts of Java Heap Dump Analysis with MAT | by Isuru Perera | Medium](https://images.openai.com/static-rsc-4/RvSRacKZ4LFX80AIvSysvR9ZlqKYFtAiRM105VrZv_gC1PlWFaDboBCMv7skA49ZgJ6aNEpgsTRWzX1KfTiZebyGMz0FmvVMpKqyEnz-PSayHezb6WeFpXiXCdJnUBmTBIxwH2ZsdHiUg4p1UK-0idfED18SZ_hCPV-YwowiVt0?purpose=inline)

[java 自带的监控工具 VisualVM - 别再闹了 - 博客园](https://images.openai.com/static-rsc-4/ZB60iJFaxbuvlLf_SzAIIFFplbvqG_mNZ5Y_7xFmKtrFXZG2l7B2eJtddVtLX2nOigChU39bxtaytS62Fojr3Hyvgy90KdyBK83ScV5CyON1Vpe4MsVPsEloGeB3qM9MlfdOCC_ojiLGZPRWiRlOGqxFw8WpR34gs2PKo4Qf_L0?purpose=inline)

[10 Tips for using the Eclipse Memory Analyzer](https://images.openai.com/static-rsc-4/Cpm2ccP-fDkQUIG8cG-1syPKl4wjg5Db3fK368eaatFlbN1ZfLVNMVmlC_PISlDTiIkI5cOn0V0iRQK8Oe0jsbvG-WvlGWT8FRe7IX-KnNPi6KyXXIun0yRv7dZLy13iZzfvLiyzG-M0UHf9k-nF5pW_iFFGmgIlEkrwzmCFibU?purpose=inline)

8

### **Memory leak vs high memory usage**

|Memory leak|High memory usage|
|---|---|
|Objects remain retained unnecessarily|Objects may be legitimately needed|
|Post-GC heap usage may trend upward|Heap may stabilize after GC|
|Often worsens over time|May be normal under workload|
|Requires fixing object retention|May require capacity or allocation tuning|

**Interview answer:**

"I would monitor heap usage and garbage collection over time. If memory usage after GC continuously increases, I would capture a heap dump and analyze it using Eclipse MAT. I would inspect retained objects, dominator trees and GC roots to identify which references are preventing objects from being collected. Then I would fix the retention issue and monitor memory again."

## **10. A service becomes unresponsive randomly. What could be happening?**

**Question:** Your Spring Boot service works normally, but sometimes it stops responding to API requests.

### **Possible causes**

1. **Thread pool exhaustion:** All request-handling threads are busy.
    
2. **Database connection pool exhaustion:** Requests are waiting for database connections.
    
3. **Deadlock:** Two or more threads are waiting indefinitely for each other.
    
4. **Slow external API:** Requests are blocked while waiting for another service.
    
5. **Memory pressure:** Frequent GC pauses or memory exhaustion.
    
6. **CPU saturation:** Application threads cannot get enough CPU time.
    
7. **Queue backlog:** Background tasks or message consumers cannot process incoming work quickly enough.
    
8. **Lock contention:** Many threads are waiting for the same lock.
    

### **How will you investigate?**

**Step 1: Check API health and latency**

Monitor:

- Request count.
    
- Error rate.
    
- Response time (p95 and p99).
    
- Active requests.
    
- CPU and memory usage.
    

**Step 2: Capture a thread dump**

```
jcmd <PID> Thread.print
```

Check for:

- Many `BLOCKED` threads.
    
- Threads waiting for database connections.
    
- Threads stuck in socket reads.
    
- Long-running application methods.
    
- Deadlocked threads.
    

**Step 3: Check database connection pool**

For HikariCP, inspect:

- Active connections.
    
- Idle connections.
    
- Pending threads waiting for connections.
    
- Connection acquisition timeouts.
    

**Step 4: Check external dependencies**

Investigate external API response times, network timeouts, retries and circuit-breaker behavior.

### **Example: External API without timeout**

```
// Illustrative: configure explicit connection and response timeouts.
```

A service that waits indefinitely for an external dependency can consume request threads and eventually become unresponsive.

**Interview answer:**

"I would first check application metrics such as CPU, memory, API latency and active threads. Then I would capture thread dumps to identify blocked or waiting threads. I would also inspect database connection pools, external API timeouts, GC pauses and background task queues. After identifying the bottleneck, I would fix it and verify the service under load."

## **11. You see deadlock in your system. How will you detect and resolve it?**

**Question:** Two threads are waiting for locks held by each other, and neither can proceed.

### **What is deadlock?**

Deadlock is a situation in which two or more threads wait indefinitely for resources locked by each other.

### **Example**

**Deadlock scenario**

**Thread 1**

Holds Lock A

Waits for Lock B

**Thread 2**

Holds Lock B

Waits for Lock A

Neither thread can proceed

### **Java example**

```
public class DeadlockExample {

    private static final Object lockA = new Object();
    private static final Object lockB = new Object();

    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            synchronized (lockA) {
                synchronized (lockB) {
                    System.out.println("Thread 1");
                }
            }
        });

        Thread t2 = new Thread(() -> {
            synchronized (lockB) {
                synchronized (lockA) {
                    System.out.println("Thread 2");
                }
            }
        });

        t1.start();
        t2.start();
    }
}
```

The two threads acquire locks in opposite orders. Depending on timing, they can deadlock.

### **How to detect it**

Capture a thread dump:

```
jcmd <PID> Thread.print
```

Look for:

- `Found one Java-level deadlock`.
    
- Threads waiting for locks.
    
- Lock ownership information.
    
- Repeated blocked states across thread dumps.
    

### **How to resolve it**

**1. Maintain consistent lock ordering**

Always acquire Lock A before Lock B.

**2. Reduce synchronized sections**

Avoid holding locks while performing slow database or network operations.

**3. Use timed lock acquisition when appropriate**

```
ReentrantLock lock = new ReentrantLock();

try {
    if (lock.tryLock(2, TimeUnit.SECONDS)) {
        try {
            // Critical section
        } finally {
            lock.unlock();
        }
    } else {
        // Handle failure to acquire lock
    }
} catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

**Interview answer:**

"I would capture a thread dump using `jcmd` or `jstack` and check whether Java reports a deadlock. I would identify the threads and locks involved. To prevent recurrence, I would maintain a consistent lock acquisition order, reduce critical sections and avoid holding locks during slow operations."

## **12. Your logs show inconsistent behavior across requests. Why?**

**Question:** The same API sometimes produces different results, and logs are difficult to correlate.

### **Possible reasons**

- Shared mutable state between requests.
    
- Race conditions.
    
- Incorrect use of static variables.
    
- ThreadLocal values not cleared.
    
- Missing request identifiers.
    
- Asynchronous processing.
    
- Different application instances using different configurations.
    
- Timezone or date-format differences.
    
- Requests reaching different service versions.
    

### **Example: Unsafe shared state**

```
public class RequestService {

    private String currentUser;

    public void process(String username) {
        currentUser = username;
        // Process request
    }
}
```

Spring singleton beans are shared by multiple request threads by default. This mutable field can be overwritten by another request.

### **Better approach**

Keep request-specific data local to the method:

```
@Service
public class RequestService {

    public void process(String username) {
        String currentUser = username;
        // Process request
    }
}
```

### **Use correlation IDs**

A correlation ID helps trace all logs belonging to one request.

Example log:

```
2026-11-23 INFO  [correlationId=REQ-101]
Request received

2026-11-23 INFO  [correlationId=REQ-101]
Customer validated

2026-11-23 INFO  [correlationId=REQ-101]
Invoice created
```

In a Spring Boot application, you can use a filter or an observability tracing library to propagate trace context.

**Interview answer:**

"I would check for shared mutable fields in singleton Spring beans, race conditions and incorrect ThreadLocal usage. I would also introduce correlation IDs and structured logging so I can trace each request across application layers. For distributed applications, I would use distributed tracing to identify where inconsistent behavior starts."

## **13. Your application crashes without any clear error. How will you debug it?**

**Question:** Your Java application unexpectedly stops, but you cannot immediately identify the reason.

### **Possible causes**

- `OutOfMemoryError`.
    
- JVM crash.
    
- Container or operating system killing the process.
    
- Unhandled exception in a critical execution path.
    
- Native library failure.
    
- Disk exhaustion.
    
- Deployment or configuration issue.
    
- Process termination by an external supervisor.
    

### **Debugging approach**

**Step 1: Check application logs**

Look at the last messages before the process stopped. Check exceptions and stack traces.

**Step 2: Check operating system/container logs**

For Linux:

```
dmesg -T
```

For Docker:

```
docker logs <container-name>
```

Look for out-of-memory kills, exit codes and termination signals.

**Step 3: Check JVM crash files**

Look for files such as:

```
hs_err_pid1234.log
```

These can contain JVM crash details, thread information and native stack traces.

**Step 4: Enable diagnostic options**

For suitable JVM deployments:

```
-XX:+HeapDumpOnOutOfMemoryError
-XX:HeapDumpPath=/logs/
-XX:ErrorFile=/logs/hs_err_pid%p.log
```

**Step 5: Check infrastructure**

Inspect:

- Container memory and CPU limits.
    
- Disk space.
    
- Restart history.
    
- Deployment events.
    
- Health checks.
    
- Exit code and termination reason.
    

### **Important distinction**

|Situation|What to investigate|
|---|---|
|Java exception|Application stack trace|
|Heap OOM|Heap dump and GC|
|JVM fatal crash|`hs_err_pid` file|
|Container OOM kill|Container/orchestrator events|
|External termination|Process signals and deployment logs|

**Interview answer:**

"I would establish whether the application exited because of a Java exception, JVM crash, operating system termination or container resource limit. I would inspect application and system logs, exit codes, JVM crash reports and container events. If an OutOfMemoryError is involved, I would analyze the heap dump to find the root cause."

## **14. A database call is slowing down your Java service. How will you optimize it?**

**Question:** An API is taking several seconds because a database operation is slow.

### **Possible reasons**

1. Missing database indexes.
    
2. Inefficient SQL queries.
    
3. Fetching unnecessary columns.
    
4. N+1 query problem in Hibernate.
    
5. Fetching too many records.
    
6. Missing pagination.
    
7. Connection pool exhaustion.
    
8. Long-running transactions.
    
9. Lock contention in the database.
    
10. Network latency between application and database.
    

### **How to investigate**

**Step 1: Identify the slow query**

Use application logs, APM tools or database monitoring.

**Step 2: Analyze the execution plan**

For MySQL:

```
EXPLAIN
SELECT *
FROM orders
WHERE customer_id = 101;
```

Check whether the query uses an appropriate index and whether it scans too many rows.

**Step 3: Add an index when justified**

```
CREATE INDEX idx_orders_customer_id
ON orders(customer_id);
```

Indexes improve many read queries, but they also consume storage and add overhead to writes. Validate the change with an execution plan and realistic workload.

**Step 4: Avoid fetching unnecessary records**

Instead of:

```
List<Order> orders = orderRepository.findAll();
```

Use pagination:

```
Page<Order> orders =
        orderRepository.findAll(PageRequest.of(0, 50));
```

**Step 5: Investigate the N+1 query problem**

Suppose you fetch 100 orders and access each order's customer lazily. Hibernate might execute one query for orders and 100 additional queries for customers.

Possible approaches:

- Fetch joins.
    
- Entity graphs.
    
- DTO projections.
    
- Batch fetching.
    

Example:

```
@Query("""
    SELECT o
    FROM Order o
    JOIN FETCH o.customer
    WHERE o.status = :status
    """)
List<Order> findOrdersWithCustomer(
        @Param("status") String status);
```

Use fetch joins carefully, especially with pagination and collection relationships.

**Step 6: Check connection pooling**

Inspect HikariCP active, idle and pending connection metrics.

**Interview answer:**

"I would first identify the slow SQL query and measure its execution time. Then I would analyze its execution plan, check indexes, investigate N+1 queries and avoid fetching unnecessary data. I would also use pagination, DTO projections where appropriate, and monitor connection pool utilization. Finally, I would compare query latency before and after the optimization."

## **15. Your thread pool gets exhausted under load. What's your approach?**

**Question:** Your application receives more requests than its available worker threads can process.

### **What is thread pool exhaustion?**

Thread pool exhaustion occurs when all worker threads are occupied and new tasks cannot be processed immediately.

### **Common reasons**

- Slow database queries.
    
- Blocking external API calls.
    
- Too many long-running tasks.
    
- Insufficient thread pool capacity.
    
- Tasks waiting on locks.
    
- Missing timeouts.
    
- Unbounded task queues.
    
- Thread leaks or tasks that never complete.
    

### **Example: Executor configuration**

```
@Bean
public ExecutorService taskExecutor() {
    return new ThreadPoolExecutor(
            10,
            20,
            60,
            TimeUnit.SECONDS,
            new ArrayBlockingQueue<>(100),
            new ThreadPoolExecutor.CallerRunsPolicy()
    );
}
```

Here:

|Parameter|Value|Meaning|
|---|---|---|
|Core pool size|10|Normally maintained threads|
|Maximum pool size|20|Maximum worker threads|
|Keep alive|60 seconds|Extra idle thread timeout|
|Queue capacity|100|Tasks waiting in queue|
|Rejection policy|CallerRunsPolicy|Submitting thread executes rejected task|

### **How to fix it**

1. Identify why tasks are taking too long.
    
2. Optimize database and external service calls.
    
3. Configure suitable connection and response timeouts.
    
4. Set bounded thread pools and queues.
    
5. Choose an appropriate rejection or backpressure strategy.
    
6. Monitor active threads, queue size and task execution time.
    
7. Tune thread counts based on CPU, blocking behavior and load testing.
    

**Important:** Increasing thread count blindly can increase context switching, memory usage and database contention.

**Interview answer:**

"I would inspect thread pool metrics, queue size, active threads and task execution time. I would identify whether threads are blocked on database calls, external APIs or locks. Then I would optimize the bottleneck, configure bounded queues and suitable pool sizes, introduce timeouts and use backpressure or rejection handling where required."

## **16. You need to handle high concurrency safely. What will you use?**

**Question:** Thousands of users access your application simultaneously. How will you make sure shared data is handled correctly?

### **Important techniques**

|Technique|Purpose|
|---|---|
|`synchronized`|Protect critical sections|
|`AtomicInteger`|Atomic variable updates|
|`ConcurrentHashMap`|Thread-safe map operations|
|`ExecutorService`|Manage worker threads|
|`CompletableFuture`|Asynchronous task composition|
|Database transactions|Maintain data consistency|
|Optimistic locking|Prevent conflicting database updates|
|Pessimistic locking|Lock records during critical operations|
|Message queues|Decouple processing and handle bursts|

### **Example 1: Atomic counter**

```
private final AtomicInteger count = new AtomicInteger();

public void increment() {
    count.incrementAndGet();
}
```

### **Example 2: Optimistic locking with JPA**

Suppose two users update the same product simultaneously.

You can use `@Version`:

```
@Entity
public class Product {

    @Id
    private Long id;

    private String name;

    private Integer quantity;

    @Version
    private Long version;
}
```

JPA uses the version to detect conflicting updates. If another transaction updates the record first, the stale transaction can fail with an optimistic locking exception.

### **Example 3: Database transaction**

```
@Transactional
public void updateProduct(Long id, int quantity) {
    Product product = repository.findById(id)
            .orElseThrow();

    product.setQuantity(quantity);
}
```

A transaction provides atomicity for the database operations within its scope, but it does not automatically make arbitrary Java shared state thread-safe.

### **Interview answer**

"I would first identify whether concurrency affects in-memory data, database records or background processing. For shared Java data, I would use atomic classes and concurrent collections. For database consistency, I would use transactions and optimistic or pessimistic locking depending on the use case. I would also use bounded thread pools and load testing to validate the application under concurrent requests."

## **17. Your system processes duplicate requests. How will you handle it?**

**Question:** A user clicks the payment button twice, or a client retries a request because it did not receive a response. The same operation is processed multiple times.

### **What is idempotency?**

An operation is idempotent when executing it multiple times with the same input produces the same intended result as executing it once.

For example:

- Setting an order status to `CANCELLED` repeatedly can be idempotent.
    
- Creating a payment repeatedly without duplicate protection is not idempotent.
    

### **Solution: Idempotency key**

The client sends a unique key with the request:

```
POST /api/payments
Idempotency-Key: payment-12345
```

The backend:

1. Receives the request.
    
2. Checks whether the idempotency key already exists.
    
3. If it exists, returns the previously stored result.
    
4. If it does not exist, processes the payment.
    
5. Stores the result against the key.
    
6. Returns the response.
    

### **Database example**

```
CREATE TABLE payment_requests (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    idempotency_key VARCHAR(100) NOT NULL UNIQUE,
    status VARCHAR(30),
    response_data JSON
);
```

The unique constraint is important. An application-level check alone is not sufficient because two concurrent requests can both pass the check before either inserts a record.

### **Other techniques**

- Unique constraints on business identifiers.
    
- Transaction boundaries.
    
- Duplicate message detection.
    
- Atomic state transitions.
    
- Idempotent message consumers.
    
- Outbox/inbox patterns for reliable event processing.
    

### **Real-world payment example**

Suppose a customer submits a payment request twice because the first response timed out.

Without protection, the system might charge the customer twice.

With idempotency, both requests use the same key, and the backend returns the original payment result instead of creating a second payment.

**Interview answer:**

"I would implement idempotency using a unique request key. The backend would store the key and processing result, with a database unique constraint to handle concurrent duplicate requests safely. For message-based processing, I would also maintain processed message identifiers so that retries don't repeat business operations."

## **18. A cache is giving stale data. How will you fix it?**

**Question:** A customer updates their profile, but the API continues returning the old information.

### **What is stale data?**

Stale data occurs when a cache contains an outdated value that no longer matches the latest source data.

### **Common reasons**

- Cache is not invalidated after database updates.
    
- TTL is too long.
    
- Multiple application instances maintain separate caches.
    
- Cache refresh fails.
    
- Concurrent updates overwrite newer values with older ones.
    
- Cache and database updates are not coordinated.
    

### **Cache-aside pattern**

This is a commonly used caching strategy.

**Reading data**

API Request

Check Cache

**Cache hit**

Return cached value

**Cache miss**

Read database, populate cache

### **Spring Boot example**

With Spring Cache:

```
@Cacheable(value = "customers", key = "#id")
public Customer getCustomer(Long id) {
    return customerRepository.findById(id)
            .orElseThrow();
}
```

After updating a customer:

```
@CacheEvict(value = "customers", key = "#id")
public void updateCustomer(Long id, CustomerRequest request) {
    Customer customer = customerRepository.findById(id)
            .orElseThrow();

    customer.setName(request.getName());

    customerRepository.save(customer);
}
```

The cache entry is evicted after the update method completes successfully by default.

### **Other solutions**

- Configure appropriate TTL.
    
- Use Redis for shared caching across instances.
    
- Apply cache invalidation after successful database updates.
    
- Use versioning or event-based invalidation where appropriate.
    
- Monitor cache hit/miss rates.
    
- Avoid caching highly volatile data without a clear consistency strategy.
    

**Interview answer:**

"I would identify how the cache is populated and invalidated. I would make sure cache entries are evicted or refreshed after successful database updates. For multiple application instances, I would consider a shared cache such as Redis. I would also configure suitable TTL values and monitor cache consistency."

## **19. Your application is not scaling even after adding instances. Why?**

**Question:** You have deployed more application instances, but overall performance has not improved.

### **What is horizontal scaling?**

Horizontal scaling means adding more application instances to handle increased traffic.

However, simply adding instances does not guarantee better performance.

### **Possible reasons**

1. **Database bottleneck:** All application instances share the same database, which becomes overloaded.
    
2. **Shared external dependency:** A third-party API cannot handle increased traffic.
    
3. **Load balancer issue:** Traffic is not distributed properly.
    
4. **Sticky sessions:** Requests remain tied to particular instances, creating uneven load.
    
5. **Shared state:** Application relies on local memory or session data.
    
6. **Connection pool multiplication:** Each instance creates too many database connections.
    
7. **Synchronized bottleneck:** All instances compete for the same lock or shared resource.
    
8. **Insufficient downstream capacity:** Kafka, Redis or another service becomes the bottleneck.
    

### **Example: Database connection problem**

Suppose:

- 5 application instances.
    
- Each instance has a maximum of 30 database connections.
    

Total potential connections:

\[ 5 \times 30 = 150 \]

If the database supports only 100 application connections, adding more instances can make the problem worse.

### **How to investigate**

|Component|What to monitor|
|---|---|
|Load balancer|Traffic distribution|
|Application|CPU, memory, latency, errors|
|Database|Active connections, slow queries, CPU|
|Redis|Latency, connections, memory|
|Kafka|Consumer lag, throughput|
|External APIs|Latency, rate limits, failures|

### **How to improve scalability**

- Optimize database queries and indexes.
    
- Configure connection pools according to database capacity.
    
- Use distributed caching where appropriate.
    
- Keep application instances stateless where possible.
    
- Use asynchronous processing for suitable workloads.
    
- Apply load balancing.
    
- Introduce rate limiting and backpressure.
    
- Scale bottleneck components independently.
    

### **Interview answer**

"I would identify the actual bottleneck before adding more instances. I would monitor load balancer distribution, application resources, database connections, query performance and downstream services. If the database is the bottleneck, adding application instances may increase database contention. I would optimize the bottleneck and then validate horizontal scaling through load testing."

## **20. You need to trace a request across multiple layers. How will you do it?**

**Question:** A request passes through a controller, service, repository, database and external API. How can you identify where it failed or became slow?

### **Solution: Logging, correlation IDs and distributed tracing**

You need to track one request across all the components it touches.

### **Example request flow**

**Client**

Request ID: REQ-101

**API Gateway / Filter**

**Controller**

**Service Layer**

**Repository**

Database query

**External API**

HTTP request

### **1. Use correlation IDs**

Every request gets a unique identifier.

Example:

```
[REQ-101] Request received
[REQ-101] Controller started
[REQ-101] Service started
[REQ-101] Database query completed
[REQ-101] External API completed
[REQ-101] Response returned
```

This makes it easier to find all logs related to a particular request.

### **2. Use Spring Boot filters**

A filter can read or generate a request ID and put it into the logging context.

Illustrative example:

```
@Component
public class CorrelationIdFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        String requestId = request.getHeader("X-Request-ID");

        if (requestId == null || requestId.isBlank()) {
            requestId = UUID.randomUUID().toString();
        }

        try {
            MDC.put("requestId", requestId);
            response.setHeader("X-Request-ID", requestId);

            filterChain.doFilter(request, response);
        } finally {
            MDC.remove("requestId");
        }
    }
}
```

`MDC` (Mapped Diagnostic Context) adds request-specific information to log messages. Always clean it up in `finally`, because servlet threads are reused.

### **3. Use distributed tracing**

For distributed applications, use tracing tools such as:

- OpenTelemetry.
    
- Micrometer Tracing.
    
- Jaeger.
    
- Zipkin.
    
- Grafana Tempo.
    

A trace contains multiple spans representing work performed by different services.

[Distributed tracing with Linkerd | Linkerd](https://images.openai.com/static-rsc-4/rSVzKUDN3fDy8gg0qkUyGiqtRxrTEzMHoWrEsCvsLSoRjxEXKWb_soAXYg2Jt3BiHX9hiZgZPmtdNHTjUJcuKeArck30VKh320q4fNLiKegyBJNcHgsmkntyWrHmYquselRDstCi4OGWAKPqY_WrB3V_F_gOJ00WwSxgHbzrYx4?purpose=inline)

[Microservices: Distributed Tracing mit Grafana Tempo im Test | heise online](https://images.openai.com/static-rsc-4/X8Bm-sgDbxJy4oBLpL9KDZ6aClUi3LgiRsbeieRp2U-A_fmwS_e4RpDvAm88RmiXAaBvsheukVaX_5TvRATs3iWwj1WArgZSThCP7xpZG3GtaBoMBG7qlpJZdO4vZQRWBPL8BlBgJuRG80tqnjsRUh0WZPWBiL4HFV-gK6KhjkA?purpose=inline)

[Query tracing data | Grafana Cloud documentation](https://images.openai.com/static-rsc-4/rb1fJZafU47WwKwnGzElsnDuFuwN1tscAuuh7saCgi5Nz_2tRer0RZ0wjF8f3_C8HWzz-o_27oBMntOW7HS4GCFHDlDieuw9KXXn3Frzr9jy9QdHaj3ga1RIuZdp6LzJNKVXc63i85U3JxX8JZRE0MpeYOWApN4iTXI5V37fwZ4?purpose=inline)

7

### **Logging vs tracing**

|Logging|Distributed tracing|
|---|---|
|Records individual events|Tracks an entire request flow|
|Provides error details|Shows service-to-service latency|
|Useful for debugging exceptions|Useful for locating bottlenecks|
|Can include correlation IDs|Uses trace IDs and span IDs|

### **Interview answer**

"I would implement structured logging with a correlation ID so that all log entries belonging to one request can be identified. For a microservices environment, I would use distributed tracing with OpenTelemetry and tools such as Jaeger or Grafana Tempo. This would help me identify which service or operation failed and where the latency occurred."

# **Quick revision: Questions 6–20**

|No.|Scenario|Key concept / solution|
|---|---|---|
|6|HashMap performance|Hash collisions, `ConcurrentHashMap`|
|7|Incorrect shared updates|Race condition, synchronization, atomic classes|
|8|Local works, production fails|Environment, configuration, connectivity|
|9|Suspected memory leak|Heap dump, Eclipse MAT, GC|
|10|Randomly unresponsive service|Thread dumps, pools, timeouts|
|11|Deadlock|Lock ordering, thread dump|
|12|Inconsistent request logs|Shared state, correlation IDs|
|13|Unexpected application crash|JVM logs, OS logs, crash files|
|14|Slow database calls|Indexes, execution plan, N+1, pagination|
|15|Thread pool exhausted|Bounded queues, timeouts, backpressure|
|16|High concurrency|Atomic classes, locks, transactions|
|17|Duplicate requests|Idempotency keys, unique constraints|
|18|Stale cache|Invalidation, TTL, Redis|
|19|Not scaling|Database/downstream bottlenecks|
|20|Trace across layers|Correlation IDs, OpenTelemetry|

# **Part 1: Core Java**

## **1. HashMap has 1 million entries. Performance is degrading. Why, and how would you resolve it?**

**First, an important point:** A `HashMap` containing one million entries is not automatically slow. With good hash distribution, lookup and insertion are expected to be approximately O(1).

### **A. How HashMap works internally**

A HashMap stores data in an array of buckets.

Each bucket can contain:

- No entry.
    
- A linked list of entries.
    
- A balanced tree (when treeification conditions are satisfied).
    

[How HashMap Works Internally?. Principle of Hashing | by Ramjeet Mahto | Medium](https://images.openai.com/static-rsc-4/kDclvBOyq88CKNltcOH24pSyk3L50dlUycKjdnA4I_b6VcdWd_aFKP5W014PQsltMSmdy8l4WHvIuVtoXU7iNxb4sayitfQLXCNi6uKCv239F4ft_qQ2JJIKFpEio2ZHVjxU7wQUVO5ETwnbElvvHoFMh6sWvol0dqKD1I9z7nQ?purpose=inline)

[medium.com](https://medium.com/%40ramjeet.mahto77/how-hashmap-works-internally-be7ada869d51?utm_source=chatgpt.com)

When you execute:

```
map.put("101", "Sangram");
map.get("101");
```

Java uses the key's `hashCode()` to determine a bucket, then uses `equals()` to identify the correct key.

### **B. Why performance might degrade**

|Cause|Explanation|
|---|---|
|Hash collisions|Many keys are assigned to the same bucket|
|Poor `hashCode()`|Keys have a poor hash distribution|
|Frequent resizing|The map needs to grow and redistribute entries|
|Incorrect mutable keys|Changing a key after insertion can make it difficult to retrieve|
|Memory pressure|A large map consumes significant heap memory|
|Concurrent access|`HashMap` is not thread-safe|
|Expensive `equals()`|Comparing complex keys takes longer|

### **C. Example: Poor hashCode implementation**

```
class Employee {

    private int id;

    Employee(int id) {
        this.id = id;
    }

    @Override
    public int hashCode() {
        return 1; // Poor implementation
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Employee)) return false;

        Employee other = (Employee) obj;
        return this.id == other.id;
    }
}
```

All employee keys produce the same hash code. This causes heavy collisions.

A better implementation:

```
@Override
public int hashCode() {
    return Integer.hashCode(id);
}
```

Also ensure that fields used by `equals()` and `hashCode()` do not change while the object is being used as a map key.

### **D. How would you optimize it?**

**1. Set a suitable initial capacity**

```
Map<Integer, String> map = new HashMap<>(1_500_000);
```

This can reduce resizing when you know the expected size. The actual capacity is rounded to a supported table size.

**2. Implement correct** `hashCode()` **and** `equals()`

Avoid poor hash distribution and expensive comparisons.

**3. Use an appropriate data structure**

- `HashMap`: Single-threaded or externally synchronized access.
    
- `ConcurrentHashMap`: Concurrent access.
    
- `LinkedHashMap`: Predictable insertion/access order.
    
- Database: Large persistent datasets that should not all live in application memory.
    

**4. Profile memory and performance**

Use Java Flight Recorder (JFR), a profiler and heap analysis to identify whether the map itself or something else is causing the slowdown.

### **Interview-ready answer**

"A HashMap with one million entries is not necessarily a performance problem because average lookup complexity is O(1). I would investigate hash collisions, key hashCode and equals implementations, resizing, memory pressure and concurrent access. I would optimize the initial capacity if the expected size is known, correct poor hash distribution and use ConcurrentHashMap if multiple threads need concurrent access."

## **2. Your code uses synchronized everywhere. The application is slow. What is causing the bottleneck?**

### **Main concept: Lock contention**

When multiple threads attempt to enter synchronized sections protected by the same monitor, only one thread can hold that monitor at a time.

Other threads must wait.

This can reduce concurrency and throughput.

### **Example**

```
public class OrderService {

    public synchronized void processOrder() {

        // Database call
        // External API call
        // Business logic
    }
}
```

Imagine 100 threads calling `processOrder()` on the same object.

Only one thread can execute the synchronized method at a time.

The remaining threads wait.

[What is Thread Synchronization?. What is Thread Synchronization? | by Ömer MEMES | Medium](https://images.openai.com/static-rsc-4/4Hfr9J10yelqXk0AfGtl2vlsn-tb8qVUDZ8Fiz1xqtQsstiGCzoqrCmkXpIDsS9nFbc-_6yatZC74vZrcmGKb7dQGCV7HTJqQ68slOpsP8ImsbpT-Oc_114Aa3zLAfKFLkUc-4unMhbb7JSqMAr7hu7Ie-3HlRLGdv0pjOB7vp4?purpose=inline)

[medium.com](https://medium.com/%40omermemes83/what-is-thread-synchronization-b8ba58df855b?utm_source=chatgpt.com)

### **Why does performance degrade?**

1. Threads spend time waiting for locks.
    
2. Critical sections are too large.
    
3. Slow database or network calls happen while holding locks.
    
4. Multiple methods synchronize on the same object.
    
5. Excessive synchronization reduces parallel execution.
    

### **How to fix it?**

**Bad approach:**

```
public synchronized void process() {

    validateOrder();

    callExternalPaymentApi();

    saveOrder();

    sendNotification();
}
```

The entire method is locked, including potentially slow operations.

**Better approach:**

```
public void process() {

    validateOrder();

    callExternalPaymentApi();

    synchronized (this) {
        updateSharedState();
    }

    saveOrder();

    sendNotification();
}
```

Here, only the operation that needs protection is synchronized.

This is illustrative: if `saveOrder()` and `updateSharedState()` must be atomic together, the design needs a different consistency strategy.

### **Other optimization techniques**

- Use local variables instead of shared mutable fields.
    
- Use `AtomicInteger` for simple atomic counters.
    
- Use `ConcurrentHashMap` for concurrent map operations.
    
- Use immutable objects wherever possible.
    
- Consider `ReentrantLock` when timed or interruptible locking is needed.
    
- Avoid nested locks unless necessary.
    

### **How to investigate?**

```
jcmd <PID> Thread.print
```

Check for:

- Many threads in `BLOCKED` state.
    
- Lock owner information.
    
- Long-running synchronized methods.
    
- Repeated lock contention in thread dumps.
    

### **Interview-ready answer**

"Excessive synchronization causes lock contention. Multiple threads wait for the same monitor, reducing throughput. I would capture thread dumps and identify heavily contended locks. Then I would minimize critical sections, avoid network and database calls while holding locks, and use atomic classes or concurrent collections where appropriate."

## **3. Double-checked locking in a Singleton. Why is volatile required?**

This is a frequently asked Java concurrency question.

### **First: What is Singleton?**

Singleton is a design pattern that ensures a class has one shared instance within its intended scope.

### **Double-checked locking example**

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

### **Why do we check twice?**

**First check:**

```
if (instance == null)
```

Avoids acquiring the lock every time after the instance has been created.

**Second check:**

```
if (instance == null)
```

Ensures another thread has not created the instance while the current thread was waiting to acquire the lock.

### **Why is volatile required?**

The statement:

```
instance = new Singleton();
```

is not necessarily one indivisible operation.

Conceptually, object creation involves:

1. Allocate memory.
    
2. Initialize the object.
    
3. Assign the reference to `instance`.
    

Without `volatile`, the JVM and CPU may reorder operations in ways that allow another thread to observe a non-null reference before initialization is safely visible.

That thread could access an incompletely initialized object.

`volatile` provides the required visibility and ordering guarantees under the Java Memory Model.

[How To Make Your Double-Checked Locking Singleton Bulletproof In Java — The Two-Line Fix You Need | by The Stateless Samurai | Medium](https://images.openai.com/static-rsc-4/ngDpGvWSufPSkNP5UFKPI6ozUJh0zNQ1ziLqNjqJ_VUfl8_v7PjVWFPUnTt_JcNMpZP2XIuVBxo_KeBjU8GhIX3fqOyA-_NFs05Q-UGjEaY41YsHcKiRGNXKvx5-lQplrD1sHb0rbCEPaiaHsZx08a03nIrtaT9fRdtJQaOWA28?purpose=inline)

[medium.com](https://medium.com/%40samurai.stateless.coder/how-to-make-your-double-checked-locking-singleton-bulletproof-in-java-the-two-line-fix-you-need-e2b40b7bb509?utm_source=chatgpt.com)

### **What happens if volatile is removed?**

The synchronized block still prevents multiple threads from constructing the instance simultaneously, but the unsynchronized first read does not have the necessary visibility and ordering guarantees.

Therefore, the implementation is not safely published.

### **Alternative: Static holder pattern**

For a simple lazy Singleton, this is often easier:

```
public class Singleton {

    private Singleton() {
    }

    private static class Holder {
        private static final Singleton INSTANCE =
                new Singleton();
    }

    public static Singleton getInstance() {
        return Holder.INSTANCE;
    }
}
```

The JVM initializes the holder class when it is first actively used, providing thread-safe lazy initialization.

### **Interview-ready answer**

"Volatile is required in double-checked locking because it prevents unsafe publication caused by instruction reordering and ensures visibility between threads. Without volatile, another thread could observe a non-null reference before the object is safely initialized. The synchronized block prevents multiple initializations, while volatile makes the instance safely visible."

## **4. A ThreadLocal variable is causing a memory leak. How does it happen?**

### **First: What is ThreadLocal?**

`ThreadLocal` provides a separate variable value for each thread.

Each thread can access its own value without sharing it with other threads.

Example:

```
private static final ThreadLocal<String> userContext =
        new ThreadLocal<>();

public void processRequest() {
    userContext.set("USER-101");

    System.out.println(userContext.get());
}
```

Thread A and Thread B can store different values in the same `ThreadLocal` variable.

### **How can it cause a memory leak?**

The important point is that application servers commonly use thread pools.

A thread pool reuses worker threads instead of creating a new thread for every request.

Consider this scenario:

1. Request A uses Thread 1.
    
2. Request A stores a large object in a `ThreadLocal`.
    
3. Request A finishes.
    
4. The application does not call `remove()`.
    
5. Thread 1 returns to the pool.
    
6. Request B later reuses Thread 1.
    
7. The old ThreadLocal value may remain attached to that thread.
    

As long as the worker thread remains alive and retains the value, the object may remain reachable and cannot be garbage collected.

### **Example: Incorrect implementation**

```
public class RequestContext {

    private static final ThreadLocal<byte[]> context =
            new ThreadLocal<>();

    public void process() {
        context.set(new byte[10 * 1024 * 1024]);

        // Business logic
    }
}
```

The application stores a 10 MB array but never removes it.

If multiple long-lived worker threads retain large objects, memory consumption can increase significantly.

### **Correct implementation**

```
public class RequestContext {

    private static final ThreadLocal<byte[]> context =
            new ThreadLocal<>();

    public void process() {

        try {
            context.set(new byte[10 * 1024 * 1024]);

            // Business logic

        } finally {
            context.remove();
        }
    }
}
```

Always clean up ThreadLocal values in a `finally` block.

### **Why doesn't Java automatically remove everything?**

A ThreadLocal value is associated with a thread's internal ThreadLocal map.

The map uses weak references for its keys, but values are not necessarily weakly referenced. If the key becomes unreachable, stale entries can remain until the map performs cleanup.

A long-lived thread can therefore retain values longer than expected.

### **How to investigate?**

- Check ThreadLocal usage in filters, interceptors and executors.
    
- Inspect heap dumps for large objects retained by worker threads.
    
- Look for `ThreadLocalMap` and its entries in heap analysis.
    
- Compare memory usage after requests complete.
    
- Review cleanup logic, especially in exception paths.
    

### **Interview-ready answer**

"ThreadLocal can cause memory leaks when values are stored in long-lived pooled threads and are not removed after request processing. The worker thread retains the value even after the request finishes. I would inspect heap dumps and ThreadLocal usage, and ensure `remove()` is called in a finally block after processing."

## **5. wait vs sleep. How can each contribute to deadlock scenarios?**

This question checks your understanding of Java thread coordination and monitor locks.

### **A. Difference between wait() and sleep()**

|Feature|`wait()`|`sleep()`|
|---|---|---|
|Defined in|Object|Thread|
|Purpose|Thread coordination|Pause execution|
|Releases monitor lock?|Yes, when called on that object's monitor|No|
|Must be called inside synchronized context?|Yes, for the relevant object's monitor|No|
|Wakes up|`notify()`, `notifyAll()`, timeout or interruption|Timeout or interruption|
|InterruptedException|Yes|Yes|

### **B. Example of wait()**

```
public class SharedResource {

    public synchronized void consume()
            throws InterruptedException {

        System.out.println("Waiting...");

        wait();

        System.out.println("Resumed");
    }

    public synchronized void produce() {

        System.out.println("Notifying...");

        notify();
    }
}
```

When a thread calls `wait()` while holding this object's monitor:

1. It releases that object's monitor.
    
2. It enters the waiting state.
    
3. Another thread can acquire the same monitor.
    
4. The other thread can call `notify()` or `notifyAll()`.
    
5. The waiting thread becomes eligible to resume, but must reacquire the monitor before continuing.
    

**Important:** Always use `wait()` inside a condition-checking loop, not a simple `if`, because spurious wakeups are permitted.

```
synchronized (lock) {
    while (!condition) {
        lock.wait();
    }

    // Continue when condition is satisfied
}
```

### **C. Example of sleep()**

```
public class SleepExample {

    public static void main(String[] args)
            throws InterruptedException {

        synchronized (SleepExample.class) {

            System.out.println("Thread sleeping");

            Thread.sleep(5000);

            System.out.println("Thread resumed");
        }
    }
}
```

The thread sleeps for five seconds but continues holding the `SleepExample.class` monitor.

Another thread trying to enter a synchronized block using that same monitor must wait.

### **D. How can they contribute to deadlock?**

**Example 1: Deadlock caused by incorrect wait/notify design**

Thread A:

- Acquires Lock A.
    
- Calls `wait()` on Lock B without owning Lock B's monitor.
    
- This actually throws `IllegalMonitorStateException`.
    

A correct deadlock example needs the threads to hold different locks while waiting for each other's locks.

**Example 2: Deadlock involving sleep**

```
Object lockA = new Object();
Object lockB = new Object();
```

Thread A:

```
synchronized (lockA) {
    Thread.sleep(1000);

    synchronized (lockB) {
        // Work
    }
}
```

Thread B:

```
synchronized (lockB) {
    Thread.sleep(1000);

    synchronized (lockA) {
        // Work
    }
}
```

If Thread A holds Lock A and waits for Lock B, while Thread B holds Lock B and waits for Lock A, both can become deadlocked.

### **How to avoid it?**

- Use a consistent lock acquisition order.
    
- Avoid sleeping while holding locks.
    
- Keep synchronized sections short.
    
- Use `wait()` and `notifyAll()` only with a clear shared condition.
    
- Consider higher-level concurrency utilities such as `BlockingQueue`, `CountDownLatch` and `Semaphore`.
    

### **Interview-ready answer**

"`wait()` releases the object's monitor and is used for thread coordination, whereas `sleep()` pauses a thread without releasing any monitor it holds. Deadlock can occur when threads hold locks while waiting for other locks. I would avoid sleeping inside synchronized sections, use consistent lock ordering and use higher-level concurrency utilities where possible."

# **Part 2: JVM and Memory**

## **6. The application ran fine for 30 days, then crashed with OutOfMemoryError. No code changes. What happened?**

### **Important concept**

No code changes does not mean the application's runtime conditions remained unchanged.

An application can run for weeks before a memory problem becomes visible.

### **Possible reasons**

|Cause|Explanation|
|---|---|
|Memory leak|Objects accumulate and remain reachable|
|Unbounded cache|Cache continuously stores new entries|
|Increasing traffic|More concurrent requests require more memory|
|Large data processing|Application loads huge datasets|
|ThreadLocal leak|Long-lived threads retain objects|
|Metaspace growth|Classes or classloaders accumulate|
|Direct memory growth|Off-heap buffers consume memory|
|Native thread exhaustion|Too many threads consume native resources|

### **Example: Gradually growing cache**

```
@Component
public class CustomerCache {

    private final Map<Long, Customer> cache =
            new ConcurrentHashMap<>();

    public void add(Customer customer) {
        cache.put(customer.getId(), customer);
    }
}
```

This is thread-safe, but it is not necessarily memory-safe.

If millions of unique customers are added and never evicted, the cache grows indefinitely.

### **How would you investigate?**

**Step 1: Identify the exact OutOfMemoryError**

Different messages point to different memory areas.

|Error|Possible cause|
|---|---|
|`Java heap space`|Heap exhausted|
|`GC overhead limit exceeded`|GC spends excessive time reclaiming very little memory|
|`Metaspace`|Class metadata memory exhausted|
|`Direct buffer memory`|Direct/off-heap buffer allocation failure|
|`unable to create native thread`|Operating system or native thread resource limit|

**Step 2: Check heap usage**

```
jcmd <PID> GC.heap_info
```

**Step 3: Check GC activity**

```
jstat -gcutil <PID> 1000
```

Observe:

- Old generation usage.
    
- Full GC frequency.
    
- Heap usage after GC.
    
- Whether memory continues growing.
    

**Step 4: Capture a heap dump**

```
jcmd <PID> GC.heap_dump heapdump.hprof
```

Use Eclipse MAT to inspect:

- Dominator tree.
    
- Retained heap.
    
- Paths to GC roots.
    
- Growing collections.
    
- Large arrays and objects.
    

**Step 5: Compare the application over time**

Compare memory, traffic, cache size, thread count and deployment history across the 30-day period.

### **What should you fix?**

- Put limits and eviction policies on caches.
    
- Process large datasets in batches.
    
- Release unnecessary references.
    
- Clean up ThreadLocal values.
    
- Investigate classloader and direct-buffer retention.
    
- Correct thread leaks.
    
- Adjust heap only when the workload genuinely requires more memory and the machine/container has sufficient capacity.
    

### **Important interview point**

Increasing `-Xmx` may delay the crash, but it does not fix a memory leak.

### **Interview-ready answer**

"I would first identify the type of OutOfMemoryError. Then I would monitor heap and GC metrics, capture a heap dump and analyze it using Eclipse MAT. Since the application ran for 30 days, I would investigate gradual memory retention, unbounded caches, ThreadLocal values, traffic growth and background jobs. I would fix the underlying retention or capacity issue and monitor memory over time."

## **7. GC runs every 30 seconds and pauses the application for 2 seconds. How would you optimize it?**

### **First: What is Garbage Collection?**

Garbage Collection automatically reclaims heap memory occupied by objects that are no longer reachable.

Example:

```
public void process() {

    List<String> names = new ArrayList<>();

    names.add("Java");
    names.add("Spring Boot");
}
```

After the method completes, if the list and its contents are no longer reachable, the garbage collector can reclaim the associated memory.

### **What is a GC pause?**

Some GC operations require application threads to stop temporarily.

This is called a Stop-The-World (STW) pause.

If the application pauses for two seconds, API requests can experience significant latency.

[JVM Memory & Garbage Collection: What Senior Java Developers Actually Care About | Pranavi L posted on the topic | LinkedIn](https://images.openai.com/static-rsc-4/qFOybXrxGiwdXm9BGr1jD5x7qhmWStaLmT3KawJC-PR4shu5YCdhQm1A-OFQFo568bd5Oh6gXrUev1MD4r9892tdiSDYK7n6xh4NK3Ap_AhEbHjTv60I1tdT9kreCPXjEAKAq4R8aI303DJu8KUhSnvMmaFmE-0eIzE5jp5B6Mk?purpose=inline)

[linkedin.com](https://www.linkedin.com/posts/pranavi-l-311245317_java-jvm-garbagecollection-activity-7421954148721651713-u11j?utm_source=chatgpt.com)

### **Step 1: Understand the actual GC behavior**

Don't change GC settings immediately.

Enable GC logging:

```
-Xlog:gc*:file=gc.log:time,uptime,level,tags
```

Inspect:

- GC type.
    
- Pause duration.
    
- Frequency.
    
- Heap occupancy before and after GC.
    
- Young and old generation usage.
    
- Full GC occurrences.
    
- Allocation rate.
    

### **Step 2: Identify why GC is happening frequently**

Possible reasons:

1. High object allocation rate.
    
2. Large temporary collections.
    
3. Memory leaks.
    
4. Heap size too small for the workload.
    
5. Large object allocations.
    
6. Inefficient data processing.
    
7. Unbounded caches.
    

### **Step 3: Optimize application allocations**

Example of unnecessary large allocations:

```
public List<String> process() {

    List<String> result = new ArrayList<>();

    for (int i = 0; i < 1_000_000; i++) {
        result.add("Java");
    }

    return result;
}
```

If the business requirement only needs a count, allocating one million strings in a list is unnecessary.

A more appropriate implementation could be:

```
public int process() {

    int count = 0;

    for (int i = 0; i < 1_000_000; i++) {
        count++;
    }

    return count;
}
```

Other improvements:

- Use pagination.
    
- Process records in batches.
    
- Avoid unnecessary object creation.
    
- Avoid retaining objects after use.
    
- Bound caches and queues.
    
- Optimize large JSON transformations.
    

### **Step 4: Evaluate heap configuration**

For example:

```
-Xms2g
-Xmx2g
```

This is only an illustrative configuration, not a universal recommendation.

Ensure the total memory budget also accommodates:

- Metaspace.
    
- Thread stacks.
    
- Direct memory.
    
- Native libraries.
    
- JVM overhead.
    
- Container and operating system requirements.
    

### **Step 5: Evaluate the garbage collector**

Java 21 supports several collectors, including:

|Collector|Typical focus|
|---|---|
|Serial GC|Small heaps and simple workloads|
|Parallel GC|Throughput|
|G1 GC|Balanced throughput and pause-time goals|
|ZGC|Very low pause times|

### **Step 6: Verify improvements**

Compare before and after:

- GC pause duration.
    
- GC frequency.
    
- Allocation rate.
    
- Heap after GC.
    
- API p95/p99 latency.
    
- Throughput.
    
- CPU utilization.
    

### **Interview-ready answer**

"I would first analyze GC logs to identify the collector, pause frequency, pause duration and heap behavior. Then I would check allocation rates, memory leaks and large object creation using JFR or a profiler. I would optimize unnecessary allocations and review heap sizing. If pauses are still too high for the application's latency requirements, I would benchmark G1GC and ZGC using a representative workload."

## **8. When would you choose G1GC over ZGC in a production environment?**

This is an important Java 17/21 JVM interview question.

Both G1GC and ZGC are garbage collectors, but they have different optimization goals.

### **A. G1GC (Garbage First Garbage Collector)**

G1GC divides the Java heap into multiple regions and prioritizes collecting regions containing the most reclaimable garbage.

It aims to balance:

- Application throughput.
    
- Garbage collection pause times.
    
- Efficient heap utilization.
    

It is the default garbage collector for most server-class configurations in modern Java releases, including Java 21.

Enable it explicitly:

```
-XX:+UseG1GC
```

### **B. ZGC (Z Garbage Collector)**

ZGC is designed for very low pause times, including when working with large heaps.

Most of its garbage collection work happens concurrently while application threads continue running.

Enable it in Java 21:

```
-XX:+UseZGC
```

Java 21 also supports Generational ZGC:

```
-XX:+UseZGC
-XX:+ZGenerational
```

Generational ZGC separates young and old objects to improve collection efficiency.

### **C. G1GC vs ZGC**

|Feature|G1GC|ZGC|
|---|---|---|
|Main goal|Balanced performance|Very low latency|
|Collection approach|Partially concurrent|Mostly concurrent|
|Pause behavior|Predictable, but can have longer pauses|Designed for very short pauses|
|Throughput|Often a good general-purpose choice|May have different CPU overhead|
|Heap size|Small to large workloads|Particularly useful for large heaps|
|CPU overhead|Depends on workload|Concurrent GC work consumes CPU|
|Best fit|General backend applications|Latency-sensitive applications|

[A deep dive into Java garbage collectors | Datadog](https://images.openai.com/static-rsc-4/1Upu2vfGEQE8jP6DoDRZc9yUOEV_HD96jN7iatFu_i99j-DFlmkm5av3vgOvkK1HTh_Cj24k1SCFspqS9uHpcDPzCH7KyoUusoKiK4DHqNfJoxKwbX7qVL129mJV7Hk4oVKrzxsHsSgb-wJNs9rkjbEzHHKyl1iRTr-wze-NcDU?purpose=inline)

[datadoghq.com](https://www.datadoghq.com/blog/understanding-java-gc/?utm_source=chatgpt.com)

### **D. When would you choose G1GC?**

Consider G1GC when:

1. Your application needs a good balance between throughput and latency.
    
2. GC pauses are acceptable within your latency requirements.
    
3. You want a general-purpose collector for a typical Spring Boot backend.
    
4. Your workload benefits from G1's region-based collection and pause-time goal.
    
5. Benchmarking shows G1 gives better overall throughput or resource efficiency.
    

Example:

An internal employee management application where a moderate GC pause is acceptable and overall throughput matters.

### **E. When would you choose ZGC?**

Consider ZGC when:

1. Your application has strict latency requirements.
    
2. Long GC pauses are unacceptable.
    
3. You have a large heap.
    
4. Your application handles latency-sensitive requests.
    
5. Benchmarking confirms ZGC meets the latency target within your CPU and memory budget.
    

Example:

A financial transaction processing service where long JVM pauses could cause unacceptable API latency.

**Important:** ZGC is not automatically faster than G1GC. It is designed to minimize GC pauses. It can use additional CPU resources, so compare throughput, CPU consumption and tail latency as well.

### **F. How would you compare them?**

Run the same workload with both collectors.

G1GC:

```
java -XX:+UseG1GC \
     -Xms4g -Xmx4g \
     -Xlog:gc*:file=g1-gc.log \
     -jar app.jar
```

ZGC:

```
java -XX:+UseZGC \
     -XX:+ZGenerational \
     -Xms4g -Xmx4g \
     -Xlog:gc*:file=zgc.log \
     -jar app.jar
```

Compare:

- GC pause time.
    
- API p95 and p99 latency.
    
- Throughput.
    
- CPU utilization.
    
- Memory consumption.
    
- Application stability under sustained load.
    

### **Interview-ready answer**

"I would choose G1GC when I need a balanced general-purpose garbage collector with good throughput and manageable pause times. I would choose ZGC when the application has strict latency requirements and even short GC pauses matter. Before selecting one, I would analyze GC logs and benchmark both collectors under realistic production workloads. I would consider CPU usage, memory consumption, throughput and p99 latency rather than selecting a collector based only on its name."


# **Part 1: Spring Boot**

## **9.** `@Transactional` **is applied to a private method. Why does it silently have no effect?**

### **First: What does** `@Transactional` **do?**

`@Transactional` tells Spring to manage a database transaction around a method.

For example:

```
@Service
public class PaymentService {

    @Transactional
    public void makePayment() {
        // Update payment
        // Update order
    }
}
```

Spring generally implements declarative transactions using AOP proxies.

### **Why does a private method cause problems?**

Spring's standard proxy-based transaction management intercepts calls that pass through the Spring proxy.

A private method cannot be overridden by a subclass-based proxy, and a normal external caller cannot invoke it through the proxy.

Example:

```
@Service
public class PaymentService {

    public void processPayment() {
        savePayment();
    }

    @Transactional
    private void savePayment() {
        // Database operation
    }
}
```

Here, `savePayment()` is called internally from the same class. It does not pass through the Spring proxy.

Therefore, the transaction advice is not applied.

### **Important: Self-invocation problem**

Even changing the private method to public does not solve the problem if it is still called through `this` or directly inside the same class.

```
public void processPayment() {
    savePayment(); // Self-invocation
}

@Transactional
public void savePayment() {
    // Database operation
}
```

This still bypasses the proxy.

### **Correct solution: Move the transactional method to another Spring bean**

```
@Service
public class PaymentTransactionService {

    @Transactional
    public void savePayment() {
        // Database operation
    }
}
```

```
@Service
public class PaymentService {

    private final PaymentTransactionService transactionService;

    public PaymentService(
            PaymentTransactionService transactionService) {
        this.transactionService = transactionService;
    }

    public void processPayment() {
        transactionService.savePayment();
    }
}
```

Now the call goes through the Spring-managed proxy.

### **Interview-ready answer**

"`@Transactional` works through Spring AOP proxies. A private method cannot be intercepted by the usual proxy-based approach. Also, self-invocation bypasses the proxy, even when the method is public. I would move the transactional operation to another Spring-managed bean and invoke it through dependency injection."

**Remember:** By default, Spring rolls back on unchecked exceptions (`RuntimeException` and `Error`), not every checked exception. You can configure `rollbackFor` when checked exceptions should also trigger rollback.

## **10. Two beans have the same type. How does Spring determine which one to inject?**

### **Example**

Suppose you have two implementations of the same interface.

```
public interface PaymentService {
    void pay();
}
```

Implementation 1:

```
@Service
public class RazorpayService implements PaymentService {

    public void pay() {
        System.out.println("Razorpay payment");
    }
}
```

Implementation 2:

```
@Service
public class StripeService implements PaymentService {

    public void pay() {
        System.out.println("Stripe payment");
    }
}
```

Now inject the interface:

```
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

Spring finds two matching beans and cannot determine which one to inject.

You generally get `NoUniqueBeanDefinitionException`.

### **Solution 1:** `@Qualifier`

```
@Service
public class OrderService {

    private final PaymentService paymentService;

    public OrderService(
            @Qualifier("razorpayService")
            PaymentService paymentService) {
        this.paymentService = paymentService;
    }
}
```

This explicitly selects the bean.

### **Solution 2:** `@Primary`

```
@Service
@Primary
public class RazorpayService implements PaymentService {

    public void pay() {
        System.out.println("Razorpay payment");
    }
}
```

When multiple candidates exist, Spring prefers the primary bean for a single-valued injection point.

### **Solution 3: Inject all implementations**

If you need to choose dynamically:

```
@Service
public class OrderService {

    private final Map<String, PaymentService> paymentServices;

    public OrderService(
            Map<String, PaymentService> paymentServices) {
        this.paymentServices = paymentServices;
    }

    public void process(String provider) {
        PaymentService service = paymentServices.get(provider);
        service.pay();
    }
}
```

Spring can inject all matching beans into a collection or map.

### **Comparison**

|Annotation/approach|Purpose|
|---|---|
|`@Qualifier`|Explicitly selects a bean|
|`@Primary`|Marks the preferred default|
|`List<Interface>`|Injects all matching beans|
|`Map<String, Interface>`|Injects beans indexed by bean name|

### **Interview-ready answer**

"When Spring finds multiple beans of the same type, it first considers the available candidates and their qualifiers. If one candidate is marked `@Primary`, it is preferred. Otherwise, I can use `@Qualifier` to select the exact bean. If I need all implementations, I can inject a List or Map."

## **11. An** `@Async` **method throws an exception. Nobody catches it. What happens?**

### **First: What is** `@Async`**?**

`@Async` allows a method to execute asynchronously using a Spring-managed task executor.

Instead of waiting for the method to complete, the caller can continue execution.

### **Example**

```
@Service
public class NotificationService {

    @Async
    public void sendNotification() {

        System.out.println("Sending notification");

        throw new RuntimeException("Email failed");
    }
}
```

Enable async processing:

```
@Configuration
@EnableAsync
public class AsyncConfig {
}
```

### **What happens to the exception?**

It depends on the return type.

|Return type|Exception behavior|
|---|---|
|`void`|Cannot be returned to the original caller; handled through `AsyncUncaughtExceptionHandler`|
|`Future`|Available when the future is inspected|
|`CompletableFuture`|Can be handled using methods such as `exceptionally()` or `handle()`|

For `void` methods, the caller has already returned. An exception handler can log or report the failure, but cannot directly propagate it back to the original caller.

### **Solution 1: Use CompletableFuture**

```
@Service
public class NotificationService {

    @Async
    public CompletableFuture<String> sendNotification() {

        try {
            // Send email

            return CompletableFuture.completedFuture("SUCCESS");

        } catch (Exception e) {
            return CompletableFuture.failedFuture(e);
        }
    }
}
```

Caller:

```
notificationService.sendNotification()
    .exceptionally(ex -> {
        System.out.println("Failed: " + ex.getMessage());
        return "FAILED";
    });
```

### **Solution 2: Configure AsyncUncaughtExceptionHandler**

For `void` asynchronous methods, configure a handler to record uncaught exceptions.

### **Important production considerations**

- Configure a suitable thread pool.
    
- Handle exceptions explicitly.
    
- Configure timeouts where applicable.
    
- Avoid unbounded task queues.
    
- Monitor rejected tasks and task failures.
    
- Do not assume asynchronous execution means reliable delivery.
    

**Interview-ready answer**

"When an `@Async` void method throws an exception, the original caller generally cannot catch it because the method executes on another thread. Spring can route it to an AsyncUncaughtExceptionHandler. For better error handling, I would use CompletableFuture and handle failures explicitly. For reliable business events, I would consider a message broker rather than relying only on in-memory async execution."

## **12. Application startup takes 45 seconds. How would you diagnose and optimize it?**

### **Possible reasons**

1. Too many Spring beans.
    
2. Expensive bean initialization.
    
3. Database connectivity delays.
    
4. External API calls during startup.
    
5. Component scanning across unnecessarily large packages.
    
6. Hibernate schema validation or initialization.
    
7. Large classpath or dependency loading.
    
8. Flyway/Liquibase migrations.
    
9. Heavy static initialization.
    
10. Excessive application startup tasks.
    

### **Step 1: Identify where the time is spent**

Use Spring Boot startup information.

```
spring.main.log-startup-info=true
```

Spring Boot also supports startup metrics through an `ApplicationStartup` implementation.

For example:

```
@SpringBootApplication
public class Application {

    public static void main(String[] args) {

        SpringApplication app =
                new SpringApplication(Application.class);

        app.setApplicationStartup(
                new BufferingApplicationStartup(2048));

        app.run(args);
    }
}
```

You can expose startup steps using the appropriate Actuator startup endpoint in supported Spring Boot versions.

### **Step 2: Check common bottlenecks**

|Component|What to investigate|
|---|---|
|Spring beans|Expensive constructors and initialization|
|Database|Connection delays and schema operations|
|Hibernate|Entity scanning and metadata initialization|
|External APIs|Network calls during bean creation|
|Component scanning|Unnecessary packages|
|Migrations|Long-running schema changes|
|JVM|Class loading and JVM initialization|

### **Step 3: Optimize**

**Avoid network calls inside constructors**

Bad:

```
@Component
public class ExternalService {

    public ExternalService() {
        // Expensive external API call during startup
    }
}
```

Prefer performing external work only when needed, or use controlled startup initialization if it is genuinely required.

Other improvements:

- Remove unused dependencies and beans.
    
- Restrict component scanning appropriately.
    
- Avoid unnecessary eager initialization.
    
- Optimize migrations.
    
- Use lazy initialization selectively.
    
- Investigate connection timeout configuration.
    
- Profile startup with JFR.
    

### **Interview-ready answer**

"I would first measure startup phases rather than randomly changing configurations. I would use Spring Boot startup information, Actuator where supported, and Java Flight Recorder to identify slow bean initialization, database connectivity, migrations or external API calls. Then I would optimize the actual bottleneck and compare startup times before and after."

# **Part 2: Database and JPA**

## **13. LazyInitializationException occurs in production but not in development. Why?**

### **First: What is lazy loading?**

Hibernate supports lazy loading, which means associated data is fetched only when it is accessed.

Example:

```
@Entity
public class Order {

    @Id
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    private Customer customer;
}
```

When you retrieve an order, Hibernate may initially fetch only the order data.

The customer is fetched later when you access:

```
order.getCustomer().getName();
```

### **What causes LazyInitializationException?**

It occurs when Hibernate tries to initialize a lazy association after the persistence context or session has been closed.

Example:

```
public Order getOrder(Long id) {
    return orderRepository.findById(id).orElseThrow();
}
```

Controller:

```
@GetMapping("/{id}")
public String getOrder(@PathVariable Long id) {

    Order order = orderService.getOrder(id);

    return order.getCustomer().getName();
}
```

If the persistence context is closed before the customer is accessed, Hibernate cannot load the lazy association.

### **Why does it work in development?**

Possible reasons:

- Open EntityManager in View is enabled locally.
    
- Development and production profiles differ.
    
- The relationship was already initialized in development.
    
- Different transaction boundaries exist.
    
- Different test data or execution paths expose the problem.
    

Spring Boot web applications commonly enable Open EntityManager in View by default unless configured otherwise. This can make lazy loading appear to work outside the service transaction.

### **Solution 1: Fetch Join**

```
@Query("""
    SELECT o
    FROM Order o
    JOIN FETCH o.customer
    WHERE o.id = :id
    """)
Optional<Order> findOrderWithCustomer(@Param("id") Long id);
```

### **Solution 2: DTO projection**

Fetch only the fields required by the API.

```
public record OrderResponse(
        Long orderId,
        String customerName) {
}
```

```
@Query("""
    SELECT new com.example.dto.OrderResponse(
        o.id, c.name
    )
    FROM Order o
    JOIN o.customer c
    WHERE o.id = :id
    """)
Optional<OrderResponse> findOrderResponse(
        @Param("id") Long id);
```

### **Solution 3: Transactional service layer**

```
@Transactional(readOnly = true)
public OrderResponse getOrder(Long id) {

    Order order = repository.findOrderWithCustomer(id)
            .orElseThrow();

    return new OrderResponse(
            order.getId(),
            order.getCustomer().getName());
}
```

### **How to prevent it?**

- Fetch required relationships inside the transaction.
    
- Use DTO projections for API responses.
    
- Avoid exposing JPA entities directly from controllers.
    
- Do not blindly change every relationship to EAGER.
    
- Consider disabling Open EntityManager in View to expose incorrect fetch boundaries earlier.
    

**Interview-ready answer:**

"LazyInitializationException occurs when Hibernate tries to access an uninitialized lazy relationship after the persistence context is closed. It may not appear in development if Open EntityManager in View is enabled. I would define the required fetch plan using fetch joins or DTO projections and map the response inside the service transaction."

## **14. A JPA query performs well at 100 rows but takes 45 seconds at 1 million. What is wrong?**

### **First: What is happening?**

A query that performs well on a small dataset may become inefficient when the dataset grows.

The problem may be in SQL execution, fetching strategy, pagination, indexing or application-side processing.

### **Common causes**

|Problem|Explanation|
|---|---|
|Missing index|Database scans many rows|
|N+1 queries|Additional SQL queries execute for each entity|
|Fetching all records|Large amount of data transferred|
|Inefficient joins|Expensive database execution plan|
|SELECT *|Fetches unnecessary columns|
|Large offset pagination|Database skips many rows|
|Application-side filtering|Too much data is loaded into Java|
|Missing batch processing|Excessive memory usage|

### **Step 1: Inspect the SQL query**

Enable SQL logging temporarily in a controlled environment:

```
logging.level.org.hibernate.SQL=DEBUG
```

### **Step 2: Analyze the execution plan**

MySQL:

```
EXPLAIN ANALYZE
SELECT *
FROM orders
WHERE customer_id = 101;
```

Check:

- Full table scans.
    
- Index usage.
    
- Rows examined.
    
- Join strategy.
    
- Sorting operations.
    
- Actual execution time.
    

### **Step 3: Add appropriate indexes**

```
CREATE INDEX idx_orders_customer
ON orders(customer_id);
```

For multiple filtering columns, consider a composite index based on actual query patterns:

```
CREATE INDEX idx_orders_status_date
ON orders(status, created_at);
```

Index column order matters. Verify the execution plan after creating the index.

### **Step 4: Use pagination**

Avoid:

```
List<Order> orders = repository.findAll();
```

Use:

```
Page<Order> orders =
        repository.findAll(PageRequest.of(0, 100));
```

For very large datasets, keyset pagination can be more efficient than repeatedly using large offsets.

Example:

```
SELECT *
FROM orders
WHERE id > 500000
ORDER BY id
LIMIT 100;
```

### **Step 5: Fix N+1 queries**

Use fetch joins, entity graphs, batch fetching or DTO projections depending on the relationship and result size.

### **Step 6: Optimize large-data processing**

For bulk processing, use controlled batches and avoid keeping millions of managed entities in the persistence context.

**Interview-ready answer:**

"I would inspect the generated SQL and execution plan first. I would check for missing indexes, full table scans, N+1 queries and inefficient joins. I would avoid loading all records, introduce pagination or keyset pagination, and use DTO projections when full entities are unnecessary. I would also measure database execution time separately from application processing time."

## **15. Two transactions update the same row simultaneously. One silently overwrites the other. How would you prevent it?**

### **Concept: Lost update problem**

A lost update occurs when two transactions read the same existing value, independently modify it and then overwrite each other's changes.

### **Example**

Initial account balance: ₹1,000.

**Two concurrent transactions**

**Transaction A**

Reads ₹1,000

Adds ₹200

Writes ₹1,200

**Transaction B**

Reads ₹1,000

Subtracts ₹100

Writes ₹900

Possible final balance: ₹900 or ₹1,200 instead of ₹1,100

The final value depends on the transaction ordering. One update can be lost.

### **Solution 1: Optimistic locking**

Use JPA's `@Version`.

```
@Entity
public class Account {

    @Id
    private Long id;

    private BigDecimal balance;

    @Version
    private Long version;
}
```

Hibernate checks the version when updating the row.

Conceptually, the update becomes:

```
UPDATE account
SET balance = 1200, version = 2
WHERE id = 1 AND version = 1;
```

If another transaction already changed the version, the update affects zero rows. Hibernate reports an optimistic locking failure.

You can then reject or retry the operation according to business rules.

### **Solution 2: Pessimistic locking**

```
@Lock(LockModeType.PESSIMISTIC_WRITE)
@Query("SELECT a FROM Account a WHERE a.id = :id")
Optional<Account> findByIdForUpdate(@Param("id") Long id);
```

This requests a database lock on the selected row, preventing competing transactions from updating it until the lock is released.

### **Which should you use?**

|Optimistic locking|Pessimistic locking|
|---|---|
|Detects conflicting updates|Locks data to prevent concurrent updates|
|Suitable when conflicts are relatively rare|Suitable when contention is high|
|Uses `@Version`|Uses database locks|
|Can require retry handling|Can cause lock waits or deadlocks|

### **Alternative: Atomic database update**

For simple balance adjustments, an atomic SQL operation can avoid the read-modify-write race:

```
UPDATE account
SET balance = balance + 200
WHERE id = 1;
```

For financial operations, also enforce business rules such as sufficient balance, transaction boundaries and idempotency.

### **Interview-ready answer**

"This is a lost update problem. I would use optimistic locking with JPA's `@Version` when conflicts are relatively rare. If strong locking is needed, I would use pessimistic locking. For simple numeric changes, I would consider an atomic database update rather than reading and writing the value separately."

# **Part 3: System Design and Production**

## **16. Your payment API receives the same request twice due to a network retry. How do you prevent a double charge?**

### **Problem**

A customer submits a payment request. The payment provider processes it, but the response gets lost due to a network issue.

The client retries.

Without duplicate protection, the customer might be charged twice.

### **Solution: Idempotency**

An operation is idempotent when repeating the same logical request does not repeat its business effect.

### **Step 1: Client sends an idempotency key**

```
POST /api/payments
Idempotency-Key: PAY-12345
```

### **Step 2: Backend checks the key**

Store the idempotency key with a unique constraint.

```
CREATE TABLE payment_requests (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    idempotency_key VARCHAR(100) NOT NULL UNIQUE,
    status VARCHAR(30),
    payment_reference VARCHAR(100),
    response_data JSON
);
```

### **Step 3: Process safely**

A robust flow:

1. Validate the request.
    
2. Atomically reserve the idempotency key.
    
3. If the key is already completed, return the stored result.
    
4. If the same key is currently processing, return an appropriate processing response.
    
5. Call the payment provider using the same idempotency key if its API supports it.
    
6. Store the final result.
    
7. Return the result to the client.
    

### **Important: What if the payment succeeds but your application crashes?**

This is a critical distributed-systems scenario.

The payment provider might have successfully charged the customer, but your application may not have saved the success status.

On retry, you must not blindly issue a second charge.

You should:

- Use the provider's idempotency support.
    
- Query the provider using the payment reference or idempotency key.
    
- Reconcile the payment state.
    
- Update the local payment record safely.
    

### **Additional protections**

- Unique payment reference.
    
- Database transaction.
    
- Atomic state transitions.
    
- Duplicate event detection.
    
- Reconciliation jobs.
    
- Transactional outbox where events must be reliably published.
    

### **Interview-ready answer**

"I would implement an idempotency key and enforce uniqueness at the database level. Repeated requests with the same key would return the previously recorded result rather than creating a second payment. I would also use the payment provider's idempotency mechanism, where available, and reconcile the payment state if the provider succeeds but the application fails before recording the response."

## **17. Service A calls B. B is down. A keeps retrying, preventing B from recovering. What pattern fixes this?**

Key concept: Circuit Breaker

### **Problem: Retry storm**

Service A calls Service B.

Service B becomes unavailable.

Service A repeatedly retries failed requests.

This can:

- Increase traffic to B.
    
- Consume A's request threads.
    
- Increase network usage.
    
- Delay B's recovery.
    
- Cause failures in other dependent services.
    

### **Solution: Circuit Breaker Pattern**

A circuit breaker prevents repeated calls to an unhealthy service.

It has three main states.

CLOSED

**Normal operation**

Requests are allowed through. Failures are monitored.

OPEN

**Service unhealthy**

Calls are rejected quickly without contacting B.

HALF-OPEN

**Recovery test**

A limited number of trial requests are allowed.

Success can close the circuit; continued failure opens it again.

### **How does it work?**

**1. Closed state**

Requests go to Service B normally.

**2. Open state**

After the configured failure threshold is reached, the circuit opens.

New calls fail fast or use a fallback.

**3. Half-open state**

After a configured wait duration, limited trial requests are allowed.

If B recovers, the circuit closes. If not, it opens again.

### **Spring Boot example using Resilience4j**

Illustrative dependency:

```
<dependency>
    <groupId>io.github.resilience4j</groupId>
    <artifactId>resilience4j-spring-boot3</artifactId>
</dependency>
```

Example:

```
@Service
public class PaymentClient {

    @CircuitBreaker(
        name = "paymentService",
        fallbackMethod = "fallback"
    )
    public String callPaymentService() {
        return restClient.get()
                .uri("/payments/status")
                .retrieve()
                .body(String.class);
    }

    public String fallback(Exception ex) {
        return "Payment service temporarily unavailable";
    }
}
```

Actual dependency versions and Spring Boot compatibility should be checked when configuring a project.

### **Circuit breaker vs retry**

|Retry|Circuit breaker|
|---|---|
|Attempts an operation again|Stops calls to an unhealthy dependency|
|Useful for transient failures|Useful during sustained failures|
|Can increase load if misconfigured|Protects the failing service|
|Should be bounded|Uses state and thresholds|

### **Important production point**

Use:

- Limited retries.
    
- Exponential backoff.
    
- Randomized jitter.
    
- Request timeouts.
    
- Circuit breakers.
    
- Bulkheads to isolate resources.
    

Do not retry non-idempotent payment operations blindly.

### **Interview-ready answer**

"I would use the Circuit Breaker pattern. After a configured failure threshold, the circuit opens and prevents additional calls to the unhealthy service. After a wait period, it moves to half-open and tests whether the service has recovered. I would combine it with timeouts, bounded retries, exponential backoff and bulkheads."

## **18. Redis caching is working, but database load remains at 100%. What could be wrong?**

### **First: What is Redis?**

Redis is an in-memory data store commonly used for caching, distributed coordination and other fast data-access use cases.

Even when Redis is running, it does not guarantee that database load will decrease.

### **Possible reasons**

|Problem|Explanation|
|---|---|
|Low cache hit rate|Most requests still reach the database|
|Wrong cache key|Requests create different keys for the same logical data|
|Short TTL|Entries expire too frequently|
|Cache not populated|Application does not store database results|
|Cache invalidation issue|Entries are removed too early|
|Cache bypass|Some APIs directly query the database|
|Cache stampede|Many requests load the same expired key|
|Large query workload|Some queries are not suitable for caching|
|Database write-heavy workload|Cache does not eliminate required writes|

### **Step 1: Check cache hit ratio**

Suppose:

- Cache hits: 20,000.
    
- Cache misses: 80,000.
    

\[ Hit\ Ratio = \frac{20,000}{100,000}\times100 = 20\% \]

A 20% hit rate means most requests are still reaching the database.

### **Step 2: Check cache keys**

For example:

```
customer:101
customer:101
customer:101
```

These should ideally refer to the same cached record.

But inconsistent key generation can create:

```
customer:101
Customer:101
customer_101
```

All three may represent the same logical customer.

### **Step 3: Inspect cache configuration**

Check:

- TTL.
    
- Eviction policy.
    
- Cache size.
    
- Serialization.
    
- Key design.
    
- Cache hit/miss ratio.
    

### **Step 4: Check database access patterns**

Some application code may still call the repository directly:

```
public Customer getCustomer(Long id) {
    return customerRepository.findById(id).orElseThrow();
}
```

A caching layer needs to be applied consistently to the relevant read path.

### **Step 5: Prevent cache stampede**

A cache stampede occurs when many requests try to repopulate the same expired cache entry simultaneously.

Possible solutions:

- Refresh before expiration.
    
- Per-key locking or request coalescing.
    
- Randomized TTL.
    
- Preloading frequently accessed data.
    
- Stale-while-revalidate where business consistency allows it.
    

### **Interview-ready answer**

"I would check the Redis hit/miss ratio first. A low hit rate could mean incorrect keys, short TTLs or missing cache population. I would inspect cache invalidation, direct database calls and cache stampedes. I would also separate read load from write load because caching cannot eliminate database writes required by the business."

## **19. Response time is 20ms normally but reaches 8 seconds under load. No errors appear in logs. Where do you start?**

### **Important concept: Latency under load**

An application may work perfectly at low traffic but experience severe latency when concurrency increases.

The absence of exceptions does not mean the application is healthy.

### **Possible reasons**

1. Thread pool exhaustion.
    
2. Database connection pool exhaustion.
    
3. Slow SQL queries.
    
4. Lock contention.
    
5. External API latency.
    
6. GC pauses.
    
7. CPU saturation.
    
8. Queue backlog.
    
9. Load balancer imbalance.
    
10. Network latency.
    

### **Step 1: Check response time percentiles**

Do not depend only on average latency.

|Metric|Meaning|
|---|---|
|p50|50% of requests complete within this time|
|p90|90% complete within this time|
|p95|95% complete within this time|
|p99|99% complete within this time|

For example:

Illustrative request latency under load, not measured production data.

0 ms2.5K ms5K ms7.5K ms10K msNormalUnder load

### **Step 2: Break down the request time**

For example:

|Operation|Time|
|---|---|
|API gateway|100 ms|
|Controller and service|100 ms|
|Database|5,000 ms|
|External API|2,000 ms|
|Serialization|800 ms|
|Total|8,000 ms|

This example shows why measuring individual spans is more useful than guessing.

### **Step 3: Check application metrics**

- Active request threads.
    
- Thread pool queue size.
    
- CPU usage.
    
- Heap usage.
    
- GC pause time.
    
- HikariCP active and pending connections.
    
- External service latency.
    

### **Step 4: Capture thread dumps**

```
jcmd <PID> Thread.print
```

Check for:

- Threads blocked on locks.
    
- Threads waiting for DB connections.
    
- Threads stuck in network reads.
    
- Excessive waiting.
    
- Deadlocks.
    

### **Step 5: Load test**

Use tools such as:

- JMeter.
    
- Gatling.
    
- k6.
    

Gradually increase concurrent users and observe when latency starts increasing.

### **Interview-ready answer**

"I would start by comparing p50, p95 and p99 latency under normal and heavy load. Then I would break down request duration using tracing and inspect thread pools, database connections, slow queries, external dependencies, CPU and GC. I would reproduce the problem using load testing and optimize the actual bottleneck rather than increasing resources blindly."