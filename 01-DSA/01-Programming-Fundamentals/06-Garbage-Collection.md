# Garbage Collection in Java

Garbage Collection (GC) is the unsung hero of the Java Virtual Machine. Understanding how it operates under the hood is the ultimate differentiator between a mid-level developer who writes code that "works" and a senior architect who writes code that "scales."

### **The 5 Levels of Understanding**

- **Level 1 – Beginner:** Imagine you are cooking in a kitchen. As you chop vegetables, you throw the peels on the counter. If you never clean up, you'll run out of space to cook. The Garbage Collector is like an automatic robotic assistant that periodically scans your counter, finds the scraps you are no longer using, and throws them in the trash so you can keep cooking.
    
- **Level 2 – Student:** Garbage Collection is an automatic memory management feature of the JVM. It automatically reclaims Heap memory by deleting objects that are no longer reachable by the application, preventing `OutOfMemoryError`s without requiring the programmer to write manual `free()` commands.
    
- **Level 3 – Developer:** The GC works on the principle of "Reachability." It starts from "GC Roots" (like active threads and local stack variables) and traces every connected object. Any object in the Heap that cannot be reached from a GC Root is considered "dead," and its memory is reclaimed during the Mark-and-Sweep process.
    
- **Level 4 – Senior Developer:** GC involves trade-offs between **Throughput** (time spent running app code vs. GC code) and **Latency** (duration of "Stop-The-World" pauses where app threads are frozen). Modern JVMs use the Generational Hypothesis to optimize this, separating short-lived objects from long-lived ones.
    
- **Level 5 – Architect:** At an enterprise scale, unexpected GC pauses can cause SLA breaches, dropped network packets, and cascading microservice timeouts. Architects profile heap dumps and tune JVM flags (choosing algorithms like G1GC, ZGC, or Shenandoah) based on the specific traffic patterns and memory allocation rates of the application.
    

## 1. What is Garbage Collection?

Garbage Collection is a background daemon thread (a low-priority process running inside the JVM) designed to automatically track running Java applications, identify which objects in the Heap are actively in use, and delete those that are not to free up memory.

Instead of requiring the developer to explicitly destroy objects, Java handles this natively. The core algorithm historically used is **Mark, Sweep, and Compact**:

1. **Mark:** The GC traverses the object graph starting from GC Roots and "marks" every object it touches as alive.
    
2. **Sweep:** The GC scans the heap and deletes any object that was not marked.
    
3. **Compact:** The GC moves the surviving objects together, preventing memory fragmentation (so the JVM has large, contiguous blocks of free RAM to assign to new objects).
    

## 2. Need of Garbage Collection

Before Java, languages like C and C++ required developers to allocate and deallocate memory manually. This created massive engineering bottlenecks and critical vulnerabilities:

- **Memory Leaks:** A developer forgets to call `free()` on an object. The server runs for 3 days, runs out of memory, and crashes.
    
- **Dangling Pointers:** A developer deletes an object, but another part of the code still tries to access it, resulting in a severe application crash (Segmentation Fault).
    
- **Security Risks:** Hackers exploit manual memory mismanagement (like buffer overflows) to inject malicious code.
    

Garbage Collection was created to entirely abstract memory management away from the developer. The need for GC is driven by **developer productivity** and **application stability**—allowing engineers to focus purely on business logic rather than playing RAM janitor.

## 3. How JVM Handles Memory (Generational GC)

The JVM does not just scan the whole heap randomly. It uses a highly optimized architecture based on the **Weak Generational Hypothesis**, which states: _Most objects die young._ (e.g., A JSON response object is created, sent to the user, and immediately discarded).

To optimize cleanup, the JVM Heap is physically divided into "Generations":

### The Generational Workflow:

1. **Eden Space (Young Generation):** All new objects are born here. When Eden fills up, a blazing-fast **Minor GC** occurs.
    
2. **Survivor Spaces (S0 and S1):** Live objects from Eden are moved to Survivor 0. On the next Minor GC, surviving objects from Eden and S0 are moved to Survivor 1, and their "age" increments.
    
3. **Old Generation (Tenured):** Once an object survives enough Minor GC cycles (usually 15), the JVM assumes it is a permanent object (like a Database Connection or a Spring Bean). It is promoted to the Old Generation.
    
4. **Major GC (Full GC):** When the Old Generation fills up, the JVM must run a Major GC. This is an expensive operation that triggers a **Stop-The-World (STW)** event, completely freezing all application threads until the cleanup is finished.
    

**ASCII Architecture (Generational Heap):**

Plaintext

```
JVM HEAP MEMORY 
---------------------------------------------------------
|               YOUNG GENERATION                |  OLD  |
|-----------------------------------------------|  GEN  |
|     Eden Space      | Survivor 0 | Survivor 1 |       |
|  (New Objects Born) |    (S0)    |    (S1)    |       |
---------------------------------------------------------
      |                       |           |         ^
      v                       v           v         |
    Minor GC --------->   Minor GC Cycles ----> Promotion
```

## 4. Eligible Objects

An object becomes eligible for Garbage Collection the exact moment it is no longer reachable from any **GC Root**. (GC Roots include local variables on the Stack, active threads, and static variables).

Here are the 4 primary ways an object becomes eligible:

**1. Nullifying a Reference:**

Java

```
Employee emp = new Employee();
emp = null; // The Employee object is now floating in the Heap, unreachable.
```

**2. Reassigning a Reference:**

Java

```
String s1 = new String("Java");
s1 = new String("Python"); // The "Java" object is now abandoned and eligible.
```

**3. Anonymous Objects:**

Java

```
// Used once and immediately lost because no reference variable holds it.
new User().doSomething(); 
```

**4. Island of Isolation:**

Java

```
class Node { Node next; }
Node n1 = new Node();
Node n2 = new Node();
n1.next = n2;
n2.next = n1;
n1 = null; 
n2 = null; 
// n1 and n2 still point to each other, but the main application has lost 
// both of them. The GC is smart enough to collect both.
```

## 5. Advantages

- **Eliminates Manual Deallocation:** Developers write features faster without writing boilerplate memory management code.
    
- **Heap Integrity:** Prevents dangling pointers and prevents applications from reading corrupted memory.
    
- **Automatic Defragmentation:** The Compaction phase ensures the Heap remains organized, making memory allocation for new objects blazing fast (using a simple pointer bump).
    
- **Tuning Flexibility:** You can swap out the GC algorithm (e.g., from Parallel GC to G1GC) via a simple command-line flag without changing a single line of Java code.
    

## 6. Interview Questions

**1. Can we force the Garbage Collector to run?**

- **Answer:** No. You can request it using `System.gc()` or `Runtime.getRuntime().gc()`, but the JVM ultimately decides if and when to execute it based on internal heuristics. Calling it in production is an anti-pattern because it triggers an expensive Full GC.
    

**2. What are "Stop-The-World" (STW) pauses?**

- **Answer:** STW is a phase during Garbage Collection where the JVM entirely halts all running application threads. This is necessary so the GC can safely move objects around in memory and update their physical addresses without the application trying to read them simultaneously.
    

**3. If Garbage Collection is automatic, how do memory leaks happen in Java?**

- **Answer:** Java memory leaks occur when an object is no longer logically needed by the application, but a reference to it is still being held unintentionally. A classic example is adding temporary objects to a `static HashMap` and forgetting to remove them. Because static variables are GC Roots, the objects remain reachable forever, eventually causing an `OutOfMemoryError`.
    

**4. What is the difference between Minor GC and Major GC?**

- **Answer:** Minor GC cleans up the Young Generation (Eden and Survivor spaces). It happens very frequently, is highly optimized, and causes negligible pauses. Major GC cleans up the Old Generation. It happens rarely but takes significantly longer, potentially causing noticeable latency in the application.
    

**5. What is the G1 (Garbage-First) Garbage Collector?**

- **Answer:** G1GC is the default garbage collector in modern Java. Instead of strictly dividing the heap into massive continuous blocks of Young and Old generations, it divides the heap into thousands of smaller equal-sized "Regions". It tracks which regions contain the most garbage and collects those regions _first_, allowing it to meet specific, predictable pause-time goals.