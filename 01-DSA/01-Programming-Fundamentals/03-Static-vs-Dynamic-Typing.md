# Static vs. Dynamic Typing

Understanding the difference between static and dynamic typing is a critical milestone for any developer. It fundamentally changes how you design systems, handle data boundaries, and architect for scale.

## 1. Static Typing

### Definition

In a **Statically Typed** language, the type of a variable is known and checked at **compile-time**. This means the developer must explicitly declare the data type (or the compiler must infer it) before the program runs. Once a variable is assigned a type, it can never hold a value of a different type.

### Examples

- **Languages:** Java, C++, C#, Go, Rust, TypeScript.
    
- **Java Example:**
    
    Java
    
    ```
    int age = 25;
    // age = "Twenty Five"; // COMPILER ERROR: Incompatible types
    
    // Modern Static Typing (Type Inference):
    var name = "Alice"; // The compiler locks 'name' as a String at compile-time.
    // name = 100;      // COMPILER ERROR
    ```
    

### Advantages

- **Early Error Detection:** Type mismatches are caught by the compiler before the code is ever allowed to run or deploy to production.
    
- **Refactoring Safety:** In massive enterprise codebases, if you change a method signature, the compiler instantly flags every place in the system that needs to be updated.
    
- **Performance:** Because types are known before execution, the compiler optimizes memory allocation and generates highly efficient machine code. There is no runtime overhead for checking types.
    
- **Developer Experience:** IDEs (like IntelliJ or VS Code) provide flawless autocomplete and intellisense because they know exactly what type of object they are dealing with.
    

### Disadvantages

- **Slower Prototyping:** Writing strict types, interfaces, and boilerplate can slow down the initial development of a Minimum Viable Product (MVP).
    
- **Verbosity:** Historically required more lines of code (though modern features like Java's `var` and record classes have drastically reduced this).
    

## 2. Dynamic Typing

### Definition

In a **Dynamically Typed** language, the type of a variable is known and checked at **run-time**. Variables themselves do not have types; only the _values_ they point to have types. A single variable can be reassigned to hold a completely different data type during execution.

### Examples

- **Languages:** JavaScript, Python, Ruby, PHP.
    
- **JavaScript Example:**
    
    JavaScript
    
    ```
    let data = 25;      // Currently holds a Number
    data = "Hello";     // Perfectly fine. Now holds a String.
    data = [1, 2, 3];   // Still fine. Now holds an Array.
    ```
    

### Advantages

- **Rapid Prototyping:** Extremely fast to write. You can focus purely on logic without worrying about satisfying a strict compiler or designing rigid class hierarchies.
    
- **Flexibility:** Handling unpredictable data structures (like messy JSON payloads from a third-party API) is much easier.
    
- **Less Boilerplate:** Code is often much shorter and cleaner to read at a glance.
    

### Disadvantages

- **Runtime Crashes:** A typo or passing the wrong data type (e.g., passing a String to a math function) will not be caught until that specific line of code executes, potentially crashing a production server.
    
- **Harder to Maintain at Scale:** Without strict type definitions, it becomes very difficult for a new developer to know what properties exist on a deeply nested object being passed through 10 different functions.
    
- **Slower Execution:** The interpreter must spend CPU cycles constantly checking the type of values in memory before performing operations on them.
    

## Comparison Table

|**Feature**|**Static Typing**|**Dynamic Typing**|
|---|---|---|
|**When are types checked?**|Compile-Time|Run-Time|
|**Variable Binding**|Type is bound to the **Variable**|Type is bound to the **Value**|
|**Execution Speed**|Faster (Compiler optimized)|Slower (Interpreter checks types)|
|**Development Speed**|Slower initial setup|Faster initial prototyping|
|**Refactoring Safety**|Extremely High (IDE assisted)|Low (Relies heavily on Unit Tests)|
|**Ideal Use Case**|Large Enterprise Systems, Banking, OS|Startups, Scripts, Data Science (Python)|

## Interview Questions

**1. What is the difference between Static/Dynamic typing and Strong/Weak typing?**

- **Answer:** Static vs. Dynamic refers to _WHEN_ types are checked (Compile-time vs. Run-time). Strong vs. Weak refers to _HOW STRICTLY_ types are enforced regarding implicit conversions.
    
- **Explanation:** Java is Static and Strong. JavaScript is Dynamic and Weak (e.g., `"2" + 2 = "22"` without crashing). Python is Dynamic but Strong (e.g., `"2" + 2` throws a `TypeError` because it refuses to implicitly mix types).
    

**2. Does Java's `var` keyword make it dynamically typed?**

- **Answer:** Absolutely not. `var` is simply **Local Variable Type Inference**. The compiler looks at the assigned value on the right side of the equation and permanently locks the variable to that type at compile-time. It is still 100% statically typed.
    

**3. If Dynamic languages are faster to write, why is the industry shifting heavily toward Static typing for large applications (e.g., migrating from JavaScript to TypeScript)?**

- **Answer:** While dynamic languages are great for small teams and MVPs, they introduce massive technical debt at scale. In a microservices architecture with hundreds of developers, strict data contracts are required. Static typing acts as an automated communication layer—guaranteeing that Service A is sending the exact data shape that Service B expects, drastically reducing runtime production bugs.
    

**4. How do dynamically typed languages handle memory allocation differently than statically typed ones?**

- **Answer:** In a statically typed language like Java/C++, the compiler knows exactly how many bytes an integer or double requires and can allocate fixed space on the Stack. In dynamic languages like Python or JS, variables are generally just pointers referencing heavily wrapped objects in Heap memory, which allows them to change shapes but uses significantly more RAM.