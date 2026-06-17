## Introduction

The **main() method** is the most important method in a Java application because program execution starts from this method.

Whenever you run a Java program, the **Java Virtual Machine (JVM)** looks for the `main()` method and starts executing the code inside it.

Without a valid `main()` method, a Java application cannot run.

---

# Syntax of Main Method

```
public static void main(String[] args){    }
```

---

# Complete Example

```
public class Main {    public static void main(String[] args) {        System.out.println("Hello Java");    }}
```

### Output

```
Hello Java
```

---

# Breakdown of Main Method

```
public static void main(String[] args)
```

Let's understand each keyword one by one.

---

# 1. public

## Definition

`public` is an access modifier.

It makes the `main()` method accessible from anywhere.

### Why public?

The JVM needs to access and call the `main()` method.

If it is not public, JVM may not be able to execute it.

### Example

```
public static void main(String[] args)
```

### Interview Question

**Q: Why is main() declared public?**

**Answer:**  
The main() method is declared public so that JVM can access and execute it from outside the class.

---

# 2. static

## Definition

`static` means the method belongs to the class rather than an object.

### Why static?

JVM calls the main() method before creating any object.

If main() were non-static, JVM would first need to create an object of the class.

To avoid this dependency, main() is declared static.

### Example

```
public static void main(String[] args)
```

### Interview Question

### Why is main() static?

**Answer:**

Because JVM needs to execute the method without creating an object of the class.

If main() were not static:

```
public void main(String[] args){}
```

JVM would need:

```
Main obj = new Main();obj.main(args);
```

This creates a circular dependency because JVM would need an object before the program starts.

Therefore main() is declared static.

---

# 3. void

## Definition

`void` indicates that the method does not return any value.

### Example

```
public static void main(String[] args)
```

The main method performs execution but does not return anything.

### Example

```
public static void main(String[] args) {    System.out.println("Java");}
```

Output:

```
Java
```

No value is returned.

---

### Interview Question

**Q: Why is main() declared void?**

**Answer:**  
Because JVM does not expect any value to be returned after program execution.

---

# 4. main

## Definition

`main` is a special method name recognized by JVM.

### Example

```
public static void main(String[] args)
```

When a Java application starts:

1. JVM loads the class.
2. JVM searches for the main() method.
3. JVM executes it.

### Important

Changing the method name prevents execution.

Wrong:

```
public static void start(String[] args){}
```

Result:

```
Error: Main method not found
```

Correct:

```
public static void main(String[] args){}
```

---

# 5. String[] args

## Definition

`String[] args` is an array used to store command-line arguments.

### Syntax

```
String[] args
```

Alternative syntax:

```
String args[]
```

Both are valid.

---

# What are Command-Line Arguments?

Values supplied when running a Java program.

Example:

```
java Main Java FullStack Developer
```

Arguments received:

```
JavaFullStackDeveloper
```

---

# Example Program

```
public class Main {    public static void main(String[] args) {        System.out.println(args[0]);        System.out.println(args[1]);        System.out.println(args[2]);    }}
```

Run:

```
java Main Java FullStack Developer
```

Output:

```
JavaFullStackDeveloper
```

---

# Accessing Arguments

```
args[0]
```

First argument

```
args[1]
```

Second argument

```
args[2]
```

Third argument

---

# Finding Number of Arguments

```
public class Main {    public static void main(String[] args) {        System.out.println(args.length);    }}
```

Run:

```
java Main A B C D
```

Output:

```
4
```

---

# Execution Flow of main()

```
User Runs Program        │        ▼JVM Starts        │        ▼Loads Class        │        ▼Finds main()        │        ▼Executes Statements        │        ▼Program Ends
```

---

# Memory Representation

```
public static void main(String[] args)public  → Accessible by JVMstatic  → No object requiredvoid    → Returns nothingmain    → Starting pointString[] args → Command-line arguments
```

---

# Common Errors

## Error 1: Missing static

Wrong:

```
public void main(String[] args){}
```

Error:

```
Main method is not static
```

---

## Error 2: Wrong Return Type

Wrong:

```
public static int main(String[] args){    return 0;}
```

Error:

```
Main method must return void
```

---

## Error 3: Wrong Parameter Type

Wrong:

```
public static void main(int args){}
```

Error:

```
Main method not found
```

---

## Error 4: Wrong Method Name

Wrong:

```
public static void start(String[] args){}
```

Error:

```
Main method not found
```

---

# Interview Questions

### Q1. What is the main() method?

The main() method is the entry point of a Java application. Program execution starts from this method.

---

### Q2. Why is main() public?

Because JVM must be able to access and execute it from outside the class.

---

### Q3. Why is main() static?

Because JVM needs to execute it without creating an object of the class.

---

### Q4. Why is main() void?

Because JVM does not expect any return value from the method.

---

### Q5. What is String[] args?

It is an array that stores command-line arguments passed to the Java program.

---

### Q6. Can we write `String args[]` instead of `String[] args`?

Yes. Both are valid.

```
public static void main(String args[]){}
```

---

### Q7. Can we change the name args?

Yes.

```
public static void main(String[] data){}
```

This is valid because only the type matters, not the parameter name.

---

# Quick Revision

```
public static void main(String[] args)public  → JVM can access itstatic  → No object creation requiredvoid    → Returns nothingmain    → Entry point of programString[] args → Stores command-line arguments
```

# Summary

- The `main()` method is the entry point of every Java application.
- JVM starts execution from the `main()` method.
- `public` allows JVM to access it.
- `static` allows execution without object creation.
- `void` means no value is returned.
- `main` is the special method name recognized by JVM.
- `String[] args` stores command-line arguments.
- One of the most frequently asked Java interview questions is: **"Why is main() static?"** → Because JVM must execute it without creating an object.