# 1. Creating Your First Java Program

## Introduction

Java is a high-level, object-oriented, platform-independent programming language developed by James Gosling at [Oracle Corporation](https://www.oracle.com?utm_source=chatgpt.com) (originally by Sun Microsystems).

A Java program is written in a **source file** with the `.java` extension and then compiled into **bytecode** (`.class` file), which is executed by the **Java Virtual Machine (JVM)**.

---

# Key Points

### 1. Java Source File Uses `.java` Extension

Every Java program must be saved with the `.java` extension.

**Examples:**

```
Main.javaStudent.javaCalculator.java
```

---

### 2. File Name Should Match the Public Class Name

In Java, if a class is declared as `public`, then:

✅ File name = Public Class Name

**Correct Example**

File Name: `Main.java`

```
public class Main {}
```

---

**Incorrect Example**

File Name: `Program.java`

```
public class Main {}
```

This will generate a compilation error because:

```
class Main is public, should be declared in a file named Main.java
```

---

### 3. Java Program Structure

A basic Java program contains:

```
public class Main {    public static void main(String[] args) {        System.out.println("Hello World");    }}
```

---

# Understanding Each Part

## public

```
public
```

**Access Modifier**

Makes the class accessible from anywhere.

Example:

```
public class Main
```

---

## class

```
class
```

Used to create a class.

A class is a blueprint for creating objects.

Example:

```
class Student{}
```

---

## Main

```
Main
```

Class Name.

Can be any valid identifier.

Examples:

```
StudentEmployeeCalculatorBankAccount
```

---

## main() Method

```
public static void main(String[] args)
```

This is the entry point of a Java application.

Execution starts from this method.

Without `main()`, a normal Java application cannot run.

---

### Breakdown of Main Method

#### public

Accessible by JVM.

#### static

Can be called without creating an object.

#### void

Returns nothing.

#### main

Special method name recognized by JVM.

#### String[] args

Stores command-line arguments.

---

# First Java Program

```
public class Main {    public static void main(String[] args) {        System.out.println("Hello, World!");    }}
```

---

# Output

```
Hello, World!
```

---

# How Java Program Executes

## Step 1: Write Source Code

```
Main.java
```

```
public class Main {    public static void main(String[] args) {        System.out.println("Hello World");    }}
```

↓

## Step 2: Compile

Command:

```
javac Main.java
```

Compiler converts source code into bytecode.

Generated file:

```
Main.class
```

↓

## Step 3: Execute

Command:

```
java Main
```

JVM executes the bytecode.

↓

Output:

```
Hello World
```

---

# Flow Diagram

```
Java Source Code(Main.java)       │       ▼Java Compiler (javac)       │       ▼Bytecode(Main.class)       │       ▼JVM       │       ▼Output
```

---

# Why File Name Must Match Class Name?

Java compiler uses the file name to identify the public class.

Example:

```
public class Student {}
```

File Name:

```
Student.java
```

If the file name is different:

```
Test.java
```

Compilation Error:

```
class Student is public, should be declared in a file named Student.java
```

---

# Rules for Java Class Names

### Valid

```
StudentEmployeeBankAccountJavaProgram
```

### Invalid

```
123Studentclassstudent-name
```

---

# Naming Conventions

Java follows **Pascal Case** for class names.

Examples:

```
StudentBankAccountEmployeeManagementSystem
```

---

# Printing Output

Java uses:

```
System.out.println();
```

Example:

```
System.out.println("Welcome to Java");
```

Output:

```
Welcome to Java
```

---

### Print Multiple Lines

```
public class Main {    public static void main(String[] args) {        System.out.println("Java");        System.out.println("Full Stack");        System.out.println("Developer");    }}
```

Output:

```
JavaFull StackDeveloper
```

---

# Common Errors Beginners Make

### Error 1: Wrong File Name

```
public class Main {}
```

File Name:

```
Program.java
```

❌ Compilation Error

---

### Error 2: Missing Main Method

```
public class Main {}
```

❌ Program Cannot Run

---

### Error 3: Missing Semicolon

```
System.out.println("Hello")
```

❌ Compilation Error

Correct:

```
System.out.println("Hello");
```

---

### Error 4: Wrong Case

```
system.out.println("Hello");
```

❌ Error

Correct:

```
System.out.println("Hello");
```

Java is **Case Sensitive**.

---

# Interview Questions

### Q1. What is a Java source file?

A Java source file is a file containing Java code and has a `.java` extension.

---

### Q2. Why should the file name match the public class name?

Because Java compiler requires the public class name and file name to be identical.

---

### Q3. What is the entry point of a Java application?

```
public static void main(String[] args)
```

---

### Q4. What is the extension of bytecode files?

```
.class
```

---

### Q5. Which command is used to compile a Java program?

```
javac FileName.java
```

---

### Q6. Which command is used to run a Java program?

```
java ClassName
```

---

# Summary

- Java source files use the `.java` extension.
- Public class name and file name must be the same.
- Execution starts from the `main()` method.
- `javac` compiles Java source code into bytecode (`.class`).
- JVM executes the bytecode.
- Java is case-sensitive.
- `System.out.println()` is used to display output.
- Every beginner should understand the structure of a basic Java program before learning variables, data types, and OOP concepts.

### Quick Revision

```
Write Code → Save as .java        ↓Compile using javac        ↓Generate .class file        ↓Run using java command        ↓JVM Executes        ↓Output Displayed
```