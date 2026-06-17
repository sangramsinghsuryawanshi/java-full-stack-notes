## Introduction

Output means displaying data, messages, or results on the screen.

Java provides several methods to print output to the console.

The most commonly used output statement is:

```
System.out.println("Hello World");
```

---

# What is System.out.println()?

```
System.out.println("Hello World");
```

### Output

```
Hello World
```

---

# Breakdown of System.out.println()

```
System.out.println("Hello World");
```

### System

```
System
```

- A predefined class in the `java.lang` package.
- Provides access to system resources.

Example:

```
System.currentTimeMillis();System.exit(0);System.out.println();
```

---

### out

```
System.out
```

- A static object of type `PrintStream`.
- Represents the standard output stream (console).

Example:

```
System.out.print("Java");
```

---

### println

```
println()
```

- Method of `PrintStream` class.
- Prints data and moves the cursor to the next line.

Example:

```
System.out.println("Hello");System.out.println("World");
```

Output:

```
HelloWorld
```

---

# Types of Output Methods

Java provides three commonly used output methods:

```
System.out.print();System.out.println();System.out.printf();
```

---

# 1. print()

## Definition

Prints data on the same line.

### Syntax

```
System.out.print(data);
```

### Example

```
System.out.print("Hello ");System.out.print("Java");
```

Output:

```
Hello Java
```

Notice that the cursor remains on the same line.

---

# 2. println()

## Definition

Prints data and moves the cursor to the next line.

### Syntax

```
System.out.println(data);
```

### Example

```
System.out.println("Hello");System.out.println("Java");
```

Output:

```
HelloJava
```

---

# 3. printf()

## Definition

Used for formatted output.

### Syntax

```
System.out.printf(format, values);
```

### Example

```
String name = "Sangram";System.out.printf("Name: %s", name);
```

Output:

```
Name: Sangram
```

---

# Common Format Specifiers

|Specifier|Data Type|
|---|---|
|%s|String|
|%d|Integer|
|%f|Float/Double|
|%c|Character|
|%b|Boolean|

---

## Example 1

```
int age = 23;System.out.printf("Age: %d", age);
```

Output:

```
Age: 23
```

---

## Example 2

```
double salary = 45000.75;System.out.printf("Salary: %.2f", salary);
```

Output:

```
Salary: 45000.75
```

---

## Example 3

```
String name = "Sangram";int age = 23;System.out.printf("Name: %s Age: %d", name, age);
```

Output:

```
Name: Sangram Age: 23
```

---

# Difference Between print(), println(), and printf()

|Method|Behavior|
|---|---|
|print()|Prints on same line|
|println()|Prints and moves to next line|
|printf()|Prints formatted output|

---

# Example Comparison

```
public class Main {    public static void main(String[] args) {        System.out.print("Java ");        System.out.print("Developer");        System.out.println();        System.out.println("Full Stack");        System.out.printf("Age: %d", 23);    }}
```

Output:

```
Java DeveloperFull StackAge: 23
```

---

# Interview Questions

### Q1. What is System in Java?

**Answer:**  
System is a predefined class in the `java.lang` package that provides access to system resources.

---

### Q2. What is System.out?

**Answer:**  
`out` is a static object of the `PrintStream` class used for console output.

---

### Q3. Difference between print() and println()?

**Answer:**

- `print()` prints on the same line.
- `println()` prints and moves the cursor to the next line.

---

### Q4. Why do we use printf()?

**Answer:**  
`printf()` is used to display formatted output using format specifiers such as `%s`, `%d`, and `%f`.

---

# Summary

- Output is displayed using `System.out`.
- `print()` prints on the same line.
- `println()` prints and moves to the next line.
- `printf()` provides formatted output.
- `System` is a predefined Java class.
- `out` is an output stream object.
- `println()` is one of the most frequently used methods in Java programs.

---

# 2. Input in Java

## Introduction

Input means receiving data from the user during program execution.

Java provides several ways to take input, but the most commonly used class is:

```
Scanner
```

The `Scanner` class belongs to the `java.util` package.

---

# Scanner Class

## Import Statement

```
import java.util.Scanner;
```

---

# Creating Scanner Object

## Syntax

```
Scanner sc = new Scanner(System.in);
```

### Explanation

```
Scanner
```

Class name

```
sc
```

Object name

```
new Scanner(System.in)
```

Creates Scanner object connected to keyboard input.

---

# Complete Example

```
import java.util.Scanner;public class Main {    public static void main(String[] args) {        Scanner sc = new Scanner(System.in);        System.out.print("Enter Name: ");        String name = sc.next();        System.out.println("Hello " + name);    }}
```

Output:

```
Enter Name: SangramHello Sangram
```

---

# Integer Input

## Syntax

```
int age = sc.nextInt();
```

### Example

```
import java.util.Scanner;public class Main {    public static void main(String[] args) {        Scanner sc = new Scanner(System.in);        System.out.print("Enter Age: ");        int age = sc.nextInt();        System.out.println("Age = " + age);    }}
```

Output:

```
Enter Age: 23Age = 23
```

---

# String Input Using next()

## Syntax

```
String name = sc.next();
```

### Purpose

Reads a single word.

Stops reading at space.

### Example

```
String name = sc.next();
```

Input:

```
Sangram
```

Output:

```
Sangram
```

---

### Limitation

Input:

```
Sangram Singh
```

Output:

```
Sangram
```

Only the first word is read.

---

# Full Line Input Using nextLine()

## Syntax

```
String name = sc.nextLine();
```

### Purpose

Reads the entire line including spaces.

### Example

```
Scanner sc = new Scanner(System.in);String name = sc.nextLine();System.out.println(name);
```

Input:

```
Sangram Singh Suryawanshi
```

Output:

```
Sangram Singh Suryawanshi
```

---

# Difference Between next() and nextLine()

|Method|Reads|
|---|---|
|next()|Single word|
|nextLine()|Complete line|

---

# Float Input

## Syntax

```
float salary = sc.nextFloat();
```

### Example

```
Scanner sc = new Scanner(System.in);System.out.print("Enter Salary: ");float salary = sc.nextFloat();System.out.println("Salary = " + salary);
```

Output:

```
Enter Salary: 45000.50Salary = 45000.5
```

---

# Other Scanner Methods

|Method|Data Type|
|---|---|
|nextByte()|byte|
|nextShort()|short|
|nextInt()|int|
|nextLong()|long|
|nextFloat()|float|
|nextDouble()|double|
|nextBoolean()|boolean|
|next()|String (single word)|
|nextLine()|String (full line)|

---

# Complete Input Example

```
import java.util.Scanner;public class Main {    public static void main(String[] args) {        Scanner sc = new Scanner(System.in);        System.out.print("Enter Name: ");        String name = sc.nextLine();        System.out.print("Enter Age: ");        int age = sc.nextInt();        System.out.print("Enter Salary: ");        float salary = sc.nextFloat();        System.out.println("Name = " + name);        System.out.println("Age = " + age);        System.out.println("Salary = " + salary);    }}
```

---

# Common Beginner Mistake

```
int age = sc.nextInt();String name = sc.nextLine();
```

Problem:

`nextInt()` leaves a newline character in the input buffer.

### Correct Solution

```
int age = sc.nextInt();sc.nextLine(); // consume leftover newlineString name = sc.nextLine();
```

---

# Interview Questions

### Q1. Which package contains Scanner?

**Answer:**

```
java.util
```

---

### Q2. How do you create a Scanner object?

```
Scanner sc = new Scanner(System.in);
```

---

### Q3. Difference between next() and nextLine()?

**Answer:**

- `next()` reads one word.
- `nextLine()` reads an entire line.

---

### Q4. Which method is used to read an integer?

```
sc.nextInt();
```

---

### Q5. Which method is used to read a float?

```
sc.nextFloat();
```

---

# Quick Revision

```
Scanner Class      │      ▼Scanner sc = new Scanner(System.in);Integer Input → nextInt()Float Input → nextFloat()Single Word → next()Full Line → nextLine()
```

# Summary

- `Scanner` is used for user input.
- It belongs to the `java.util` package.
- `nextInt()` reads integers.
- `nextFloat()` reads float values.
- `next()` reads a single word.
- `nextLine()` reads a complete line.