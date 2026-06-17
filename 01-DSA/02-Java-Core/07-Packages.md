## Introduction

As Java applications grow, managing hundreds or thousands of classes becomes difficult.

To organize classes logically and avoid naming conflicts, Java provides **Packages**.

A package is similar to a folder (directory) in an operating system.

Just as folders organize files, packages organize Java classes and interfaces.

---

# Definition

A **Package** is a collection of related classes, interfaces, enums, and sub-packages grouped together under a common namespace.

### Syntax

```
package package_name;
```

### Example

```
package com.company.student;
```

Here:

```
com └── company      └── student
```

is the package hierarchy.

---

# Real-Life Example

Consider a college management system.

Without packages:

```
Student.javaTeacher.javaCourse.javaAdmin.javaFee.javaExam.java
```

All files are in one location, making management difficult.

Using packages:

```
com.college.studentcom.college.teachercom.college.examcom.college.fee
```

Code becomes organized and easier to maintain.

---

# Why Do We Need Packages?

Suppose two developers create classes with the same name.

Developer A:

```
class Student {}
```

Developer B:

```
class Student {}
```

Without packages, Java cannot distinguish between them.

Packages solve this problem.

Example:

```
com.college.student.Student
```

```
com.training.student.Student
```

Now both classes can exist without conflict.

---

# Package Declaration

A package declaration must be the first statement in a Java file.

### Syntax

```
package com.company.student;public class Student {}
```

---

### Incorrect

```
import java.util.*;package com.company.student;
```

This causes a compilation error because the package statement must come first.

---

### Correct

```
package com.company.student;import java.util.*;public class Student {}
```

---

# Example Program

### Student.java

```
package com.company.student;public class Student {    public void display() {        System.out.println("Student Information");    }}
```

---

# Package Directory Structure

```
src │ └── com      │      └── company            │            └── student                  │                  └── Student.java
```

---

# Compiling a Package

Assume:

```
package com.company.student;public class Student {}
```

Compile:

```
javac -d . Student.java
```

Generated Structure:

```
com └── company      └── student           └── Student.class
```

---

# Running a Packaged Class

If the class contains a main method:

```
package com.company.student;public class Student {    public static void main(String[] args) {        System.out.println("Hello Package");    }}
```

Run:

```
java com.company.student.Student
```

Output:

```
Hello Package
```

---

# Types of Packages

Java packages are mainly divided into two categories:

## 1. Built-in Packages

Packages provided by Java.

Examples:

```
java.langjava.utiljava.iojava.sqljava.netjava.time
```

---

## 2. User-Defined Packages

Packages created by programmers.

Example:

```
package com.company.employee;
```

---

# Common Built-in Packages

## java.lang

Imported automatically.

Contains:

```
StringSystemMathObjectInteger
```

Example:

```
String name = "Java";System.out.println(name);
```

No import required.

---

## java.util

Contains utility classes.

Examples:

```
ArrayListLinkedListHashMapScannerDate
```

Import:

```
import java.util.*;
```

---

## java.io

Used for file handling.

Examples:

```
FileFileReaderBufferedReaderPrintWriter
```

---

## java.sql

Used for database connectivity.

Examples:

```
ConnectionStatementPreparedStatementResultSet
```

---

# Package Naming Conventions

Java follows a standard naming convention.

### Rules

- Use lowercase letters.
- Use reverse domain names.
- Avoid spaces and special characters.

---

### Good Examples

```
com.company.studentcom.google.mailorg.apache.tomcat
```

---

### Bad Examples

```
StudentPackageMy PackageCOM.COMPANY
```

---

# Importing Packages

To use classes from another package, import them.

### Syntax

```
import package_name.ClassName;
```

Example:

```
import java.util.Scanner;
```

---

### Using Wildcard

```
import java.util.*;
```

Imports all classes from `java.util`.

---

# Example Using Scanner

```
import java.util.Scanner;public class Main {    public static void main(String[] args) {        Scanner sc = new Scanner(System.in);        System.out.println("Enter Name:");        String name = sc.nextLine();        System.out.println(name);    }}
```

---

# Advantages of Packages

## 1. Organize Code

Packages group related classes together.

Example:

```
studentteacherexamfee
```

Each module has its own package.

---

## 2. Avoid Naming Conflicts

Two classes can have the same name if they belong to different packages.

Example:

```
com.company.student.Student
```

```
com.company.training.Student
```

---

## 3. Better Maintainability

Large applications become easier to manage.

Developers can quickly locate classes.

---

## 4. Access Protection

Packages provide controlled access using access modifiers.

Example:

```
publicprotecteddefaultprivate
```

---

## 5. Reusability

Classes can be reused across multiple projects.

---

# Package Hierarchy Example

```
com └── company      ├── student      │     ├── Student.java      │     └── Address.java      │      ├── employee      │     ├── Employee.java      │     └── Salary.java      │      └── admin            └── Admin.java
```

---

# Common Beginner Mistakes

## Mistake 1: Package Name Not Matching Folder Structure

Package:

```
package com.company.student;
```

File stored in:

```
src/student
```

❌ Incorrect

Should be:

```
src/com/company/student
```

---

## Mistake 2: Package Statement Not First

Wrong:

```
import java.util.*;package com.company;
```

❌ Compilation Error

---

## Mistake 3: Using Uppercase Package Names

Wrong:

```
package COM.COMPANY.STUDENT;
```

❌ Not recommended

Correct:

```
package com.company.student;
```

---

# Interview Questions

### Q1. What is a package in Java?

**Answer:**  
A package is a collection of related classes, interfaces, and sub-packages used to organize code and avoid naming conflicts.

---

### Q2. Why are packages used?

**Answer:**

- Organize code
- Avoid naming conflicts
- Improve maintainability
- Provide access protection
- Support reusability

---

### Q3. What are the two types of packages?

**Answer:**

1. Built-in Packages
2. User-Defined Packages

---

### Q4. Which package is imported automatically?

**Answer:**

```
java.lang
```

---

### Q5. Can two classes have the same name in Java?

**Answer:**  
Yes, if they belong to different packages.

Example:

```
com.company.student.Student
```

```
com.company.training.Student
```

---

### Q6. Where should the package statement be written?

**Answer:**  
The package statement must be the first statement in the Java source file.

---

# Quick Revision

```
Package   │   ▼Collection of Related ClassesAdvantages:✔ Organize Code✔ Avoid Naming Conflicts✔ Better Maintainability✔ Reusability✔ Access Protection
```

### Example

```
package com.company.student;
```

### Types

```
1. Built-in Packages2. User-Defined Packages
```

# Summary

- A package is a collection of related classes and interfaces.
- Packages help organize Java applications.
- They prevent class name conflicts.
- The package statement must be written first in the Java file.
- Java provides built-in packages like `java.lang`, `java.util`, `java.io`, and `java.sql`.
- User-defined packages help structure large projects.
- Packages are a fundamental concept used extensively in Java, Spring Boot, Enterprise Applications, and Microservices.