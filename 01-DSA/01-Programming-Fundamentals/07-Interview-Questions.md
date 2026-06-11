Here is the architect-level cheat sheet for these eight fundamental concepts. When answering these in an interview, focus on the "why" and the internal memory mechanics, as that is what separates senior engineers from juniors.

### **1. What is a Programming Language?**

At its core, a programming language is an **abstraction layer**. Computers only understand machine code (binary $1$s and $0$s). A programming language provides a human-readable vocabulary and syntax to express complex logic, which a compiler or interpreter then translates down into that machine-executable code. It bridges the gap between human cognitive problem-solving and CPU instruction sets.

### **2. Difference between Procedural and OOP?**

- **Procedural (Imperative):** Focuses on **functions**. It is a top-down approach where you write step-by-step procedures that operate on shared, global data structures. It is highly memory-efficient but can lead to "spaghetti code" and concurrency issues as the application scales (e.g., C, Pascal).
    
- **Object-Oriented (OOP):** Focuses on **data domains**. It bundles data (state) and the functions that manipulate it (behavior) into secure, self-contained units called **Objects**. It relies on Encapsulation, Inheritance, and Polymorphism to build modular, maintainable enterprise systems (e.g., Java, C++).
    

### **3. What is Functional Programming?**

Functional Programming (FP) is a **declarative paradigm** that treats computation as the evaluation of mathematical functions. Its defining rule is the absolute avoidance of mutable state and side-effects.

- **Key Concept:** Instead of modifying an existing object, you pass it through a "pure function" that returns a brand new, updated copy.
    
- **Architectural Value:** Because data is immutable, FP is inherently thread-safe. You can run massive data streams concurrently without writing complex locking mechanisms.
    

### **4. Static vs Dynamic Typing?**

This is a matter of **when** the system checks your data types:

- **Static Typing (Java, TypeScript):** Types are checked at **compile-time**. The data type is bound to the _variable_ itself. If you declare `int age`, it can never hold a String. This prevents runtime crashes and makes large-scale refactoring exceptionally safe.
    
- **Dynamic Typing (Python, JavaScript):** Types are checked at **run-time**. The data type is bound to the _value_, not the variable. A variable can hold a number, and later be reassigned to hold a string. It allows for rapid prototyping but requires heavy unit testing to prevent runtime `TypeError` crashes.
    

### **5. Difference between Stack and Heap Memory?**

- **Stack:** A strict LIFO (Last-In-First-Out) memory structure dedicated to thread execution. It stores method frames, local primitive variables, and reference pointers. It is private to each thread, extremely fast, and cleans itself automatically the millisecond a method returns.
    
- **Heap:** A massive, dynamically allocated memory pool shared globally across all threads. It is where all **Objects** (`new MyClass()`) physically reside. Because it is unordered and shared, it is slower to access and requires the Garbage Collector to clean it up.
    

### **6. What is a Reference Variable?**

A Reference Variable is essentially a lightweight handle or pointer (typically stored on the Stack) that contains the **memory address** of an Object.

- **The Trap:** In Java, you never interact with an Object directly. When you pass an object to a method, Java passes a _copy of the reference variable_, not the actual object (Java is strictly pass-by-value).
    

### **7. How does Garbage Collection work?**

Garbage Collection (GC) is an automated background process in the JVM that reclaims Heap memory to prevent `OutOfMemoryError`s.

- **The Mechanism:** It uses a **Mark-and-Sweep** algorithm based on "Reachability." The GC pauses the application momentarily, traces paths starting from **GC Roots** (like active stack variables and static fields), and _marks_ every object it can reach as alive. It then _sweeps_ through the Heap and permanently deletes any object that was not marked.
    

### **8. When does an object become eligible for GC?**

An object becomes eligible for Garbage Collection the exact millisecond it is **no longer reachable by any active chain of references** originating from a GC Root.

Common scenarios include:

- **Nullifying:** `myObject = null;`
    
- **Reassignment:** Pointing the reference variable to a completely new object.
    
- **Out of Scope:** The method that created the local reference variable finishes executing and is popped off the Stack.
    
- **Island of Isolation:** Two objects point to each other (like a cyclic linked list), but the main program has lost all references to both of them.