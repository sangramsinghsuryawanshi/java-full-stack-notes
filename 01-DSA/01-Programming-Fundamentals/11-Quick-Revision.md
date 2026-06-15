### **Phase 1 & 2: The 30-Second Architect Cheat Sheet**

| **Concept**              | **Core Mental Model**           | **Architectural Implication**                                                           |
| ------------------------ | ------------------------------- | --------------------------------------------------------------------------------------- |
| **Programming Language** | Human to Computer Communication | Bridges the semantic gap; translates domain logic into machine code.                    |
| **Procedural**           | Function Based                  | Fast execution, but mutates shared state (hard to scale concurrently).                  |
| **Functional**           | Function + Immutability         | Thread-safe by default; perfect for massive parallel data streams.                      |
| **OOP**                  | Object Based                    | Models real-world domains; encapsulates state to prevent data corruption.               |
| **Java**                 | Statically Typed                | Types locked at compile-time; ensures safe refactoring and prevents runtime crashes.    |
| **Python**               | Dynamically Typed               | Types resolved at runtime; fast to prototype, but risky at enterprise scale.            |
| **Stack Memory**         | Local Variables & References    | Fast, thread-private, LIFO structure. Cleans itself automatically.                      |
| **Heap Memory**          | Objects                         | Massive, globally shared memory pool. Requires active management.                       |
| **Reference**            | Stores Memory Address           | Lightweight pointer. Java passes a _copy_ of this pointer, not the object itself.       |
| **Garbage Collection**   | Removes Unused Objects          | Daemon thread that reclaims Heap memory by deleting unreachable objects (via GC Roots). |


## Section: Module 1 Master Cheat Sheet & Revision Notes

### 1. 5-Minute Revision (The "Elevator Ride" Review)

- **Flowchart:** Visual/graphical representation of an algorithm using standardized geometric shapes.
    
- **Pseudocode:** Human-readable, text-based representation of an algorithm. Ignores strict language syntax (like semicolons) but keeps logical structures (`IF`, `WHILE`).
    
- **Flowchart Symbols:**
    
    - `Oval` $\rightarrow$ Start / Stop
        
    - `Rectangle` $\rightarrow$ Process (Math, Variable Assignment)
        
    - `Parallelogram` $\rightarrow$ Input / Output (Reading data, Printing)
        
    - `Diamond` $\rightarrow$ Decision (True/False branching)
        
- **Prime Number:** A number strictly $> 1$ that is divisible _only_ by $1$ and itself.
    
- **Prime Checking Complexities:**
    
    - Brute Force Check $\rightarrow$ $O(N)$
        
    - Optimized Check $\rightarrow$ $O(\sqrt{N})$
        

### 2. 15-Minute Revision (The "Waiting Room" Review)

**Understanding the _Why_:**

- **Why use Pseudocode?** It bridges the gap between a raw idea and actual code. It allows you to design complex logic (like nested loops) without fighting the compiler, making it perfect for whiteboarding.
    
- **Why optimize Prime Checks to $\sqrt{N}$?** Factors of a number always appear in pairs (e.g., for $36$: $2 \times 18$, $3 \times 12$, $6 \times 6$). If a number $N$ is divisible by a factor greater than $\sqrt{N}$, it must also be divisible by a factor smaller than $\sqrt{N}$. Therefore, checking up to the square root guarantees we find at least one part of the pair.
    

**Code Snippet Memory Refresh:**

Java

```
// Always write this in interviews, NEVER the O(N) approach
public boolean isPrime(int n) {
    if (n <= 1) return false;
    for (int i = 2; i * i <= n; i++) { // i * i <= n prevents Math.sqrt() overhead
        if (n % i == 0) return false;
    }
    return true;
}
```

### 3. Master Cheat Sheet & Comparison Tables

#### Table 1: Visualizing Logic

|**Feature**|**Pseudocode**|**Flowchart**|
|---|---|---|
|**Format**|Text-based (`START`, `IF`, `END`)|Visual (Shapes & Arrows)|
|**Best Used For**|Rapidly drafting code logic|High-level business logic mapping|
|**Modifiability**|Very easy (just edit text)|Difficult (requires redrawing arrows)|
|**Language**|English + Coding Keywords|Standardized Geometric Symbols|

#### Table 2: Prime Number Algorithms

|**Approach**|**Loop Condition**|**Time Complexity**|**When to Use**|
|---|---|---|---|
|**Brute Force**|`i < N`|$O(N)$|Never in production.|
|**Optimized**|`i * i <= N`|$O(\sqrt{N})$|Checking a single number.|
|**Sieve Array**|`i * i < N` (Nested)|$O(N \log \log N)$|Finding _all_ primes up to $N$.|

### 4. Placement Notes & Revision Strategy

**What Interviewers Expect from Basics:**

1. **Do not rush to code:** If an interviewer asks a logic question, do not immediately start writing Java syntax. Start by outlining the steps in pseudocode. This demonstrates architectural thinking and saves you from backing yourself into a logical corner halfway through writing real code.
    
2. **Know your limits:** When writing the prime number check, the interviewer is waiting to see if you will write `i < N`. Writing `i * i <= N` immediately signals that you understand algorithmic optimization and time complexity.
    
3. **Edge Cases:** Always verbalize edge cases. Before writing the loop for a prime check, say out loud: _"I need to handle numbers less than or equal to 1 first, as they cannot be prime by definition."_
    

### 5. Common Mistakes to Avoid on the Whiteboard

- **Mistake:** Using `Math.sqrt(N)` directly in the loop condition (`for(int i=2; i <= Math.sqrt(n); i++)`).
    
    - **Fix:** Calculating a floating-point square root on every iteration is mathematically expensive. Use `i * i <= n` or calculate the root once before the loop and store it in an integer variable.
        
- **Mistake:** Confusing the Process block (Rectangle) and Input/Output block (Parallelogram) when asked to draw a quick architectural flow.
    
    - **Fix:** Remember: Slanted sides (Parallelogram) = Data sliding in and out. Straight sides (Rectangle) = Data being processed inside the machine.