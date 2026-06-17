
## Introduction

Every Java program follows a specific structure. Understanding the structure of a Java file is important because every Java application is built using classes and methods.

A basic Java file contains:

```
public class Main {    public static void main(String[] args) {    }}
```

---

# General Structure of a Java File

```
// Package Declaration (Optional)
package com.example;
// Import Statements (Optional)import 
java.util.Scanner;// Class Declaration

public class Main {    
// Variables    
// Constructors    
// Methods    
// Main Method   

 public static void main(String[] args) {       
  // Program Execution Starts Here    
  }
  }
```

---

# Components of Java File

## 1. public

### Definition

`public` is an **access modifier**.

It specifies the visibility of a class, method, variable, or constructor.

### Syntax

```
public class Main {}
```

### Purpose

Makes the class accessible from anywhere in the program.

### Example

```
public class Student {}
```

### Key Points

- Accessible from any package.
- Most commonly used access modifier.
- Required when a class needs to be used globally.

### Interview Question

**Q: What is public in Java?**

**Answer:**  
Public is an access modifier that allows a class, method, or variable to be accessed from anywhere.

---

## 2. class

### Definition

A class is a blueprint or template used to create objects.

It contains:

- Variables
- Methods
- Constructors
- Business Logic

### Syntax

```
class Student {}
```

### Example

```
class Employee {}
```

### Real-Life Example

Think of a class as a blueprint of a house.

Blueprint → Class

Actual House → Object

### Key Points

- Class is the basic building block of Java.
- Objects are created from classes.
- Java is a class-based language.

### Interview Question

**Q: What is a class?**

**Answer:**  
A class is a blueprint used to create objects. It defines properties and behaviors of an object.

---

## 3. Main

### Definition

`Main` is the name of the class.

### Syntax

```
public class Main {}
```

### Rules

When a class is public:

```
Class Name = File Name
```

Example:

```
public class Main {}
```

File Name:

```
Main.java
```

### Incorrect Example

```
public class Main {}
```

File Name:

```
Program.java
```

Error:

```
class Main is public, should be declared in a file named Main.java
```

### Naming Convention

Use Pascal Case:

```
StudentEmployeeBankAccountCollegeManagementSystem
```

### Invalid Names

```
123Studentstudent-nameclass
```

---

# 4. main() Method

## Definition

The `main()` method is the entry point of a Java application.

Execution starts from this method.

### Syntax

```
public static void main(String[] args) {}
```

---

# Breakdown of main() Method

## public

```
public static void main(String[] args)
```

Allows JVM to access the method.

---

## static

```
public static void main(String[] args)
```

Allows method execution without creating an object.

Example:

```
Main.main(args);
```

No object required.

---

## void

```
public static void main(String[] args)
```

Indicates that the method returns nothing.

---

## main

```
public static void main(String[] args)
```

Special method name recognized by JVM.

Execution always starts here.

---

## String[] args

```
public static void main(String[] args)
```

Stores command-line arguments.

Example:

```
java Main Java FullStack
```

Arguments:

```
args[0] = "Java"args[1] = "FullStack"
```

Program:

```
public class Main {    public static void main(String[] args) {        System.out.println(args[0]);        System.out.println(args[1]);    }}
```

Output:

```
JavaFullStack
```

---

# Complete Example

```
public class Main {    public static void main(String[] args) {        System.out.println("Welcome to Java");    }}
```

Output:

```
Welcome to Java
```

---

# Memory Diagram

```
Main.java    │    ▼public class Main    │    ▼main() Method    │    ▼Statements    │    ▼Output
```

---

# Structure Explained Visually

```
public class Main {              // Class Start    public static void main(String[] args) {        System.out.println("Hello Java");    }}                                // Class End
```

### Explanation

```
public           → Access Modifierclass            → KeywordMain             → Class Namemain()           → Starting PointString[] args    → Command Line ArgumentsSystem.out.println() → Print Output
```

---

# Common Beginner Mistakes

## Mistake 1: File Name Different from Class Name

```
public class Main {}
```

File Name:

```
Program.java
```

❌ Compilation Error

---

## Mistake 2: Missing Curly Braces

```
public class Main    public static void main(String[] args)}
```

❌ Syntax Error

Correct:

```
public class Main {    public static void main(String[] args) {    }}
```

---

## Mistake 3: Missing Main Method

```
public class Main {}
```

❌ Program Cannot Run

---

## Mistake 4: Wrong Method Signature

Wrong:

```
public void main(String[] args)
```

Correct:

```
public static void main(String[] args)
```

---

# Interview Questions

### Q1. What is the structure of a Java program?

A Java program generally contains:

- Package declaration (optional)
- Import statements (optional)
- Class declaration
- Main method
- Statements

---

### Q2. Why is the main() method important?

Because execution of a Java application starts from the main() method.

---

### Q3. What is the purpose of static in main()?

It allows JVM to call the method without creating an object of the class.

---

### Q4. Can we change the class name Main?

Yes.

Example:

```
public class Student {    public static void main(String[] args) {    }}
```

File Name:

```
Student.java
```

---

### Q5. Can a Java file have multiple classes?

Yes.

Example:

```
class A {}class B {}public class Main {}
```

Only one class can be public, and the file name must match that public class.

---

# Quick Revision

```
Java File Structurepublic class Main {    public static void main(String[] args) {        // Code    }}public       → Access Modifierclass        → BlueprintMain         → Class Namemain()       → Entry PointString[] args→ Command Line Arguments
```

## Summary

- `public` makes the class accessible from anywhere.
- `class` is a blueprint for creating objects.
- Class name must match the file name if the class is public.
- `main()` is the starting point of program execution.
- `String[] args` stores command-line arguments.
- Every Java application is built using classes and methods.
- Understanding the Java file structure is the first step toward learning OOP, Java Collections, JDBC, Spring Boot, and Full Stack Development.