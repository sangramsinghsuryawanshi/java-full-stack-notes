## Introduction

A **data type** specifies what kind of value a variable can store and how much memory is allocated for it.

Java provides **8 Primitive Data Types**.

These are built-in (predefined) data types and are not objects.

---

# Definition

**Primitive Data Types** are predefined data types provided by Java to store simple values such as numbers, characters, and boolean values.

---

# Primitive Data Types Table

|Type|Size|Example|
|---|---|---|
|byte|1 byte|10|
|short|2 bytes|100|
|int|4 bytes|1000|
|long|8 bytes|100000L|
|float|4 bytes|10.5f|
|double|8 bytes|10.55|
|char|2 bytes|'A'|
|boolean|1 bit*|true|

*Boolean size is JVM-dependent internally, but interview notes often mention 1 bit conceptually.

---

# 1. byte

## Definition

Stores small integer values.

### Range

```
-128 to 127
```

### Example

```
byte age = 25;System.out.println(age);
```

Output:

```
25
```

### Memory

```
1 Byte = 8 Bits
```

---

# 2. short

## Definition

Stores larger integers than byte.

### Range

```
-32,768 to 32,767
```

### Example

```
short marks = 30000;System.out.println(marks);
```

Output:

```
30000
```

---

# 3. int

## Definition

Most commonly used integer data type.

### Range

```
-2^31 to 2^31-1
```

### Example

```
int salary = 50000;System.out.println(salary);
```

Output:

```
50000
```

---

# 4. long

## Definition

Used to store very large integer values.

### Example

```
long population = 8000000000L;System.out.println(population);
```

Output:

```
8000000000
```

### Important

Always use:

```
L
```

or

```
l
```

after long literals.

Recommended:

```
100000L
```

---

# 5. float

## Definition

Stores decimal numbers.

### Example

```
float price = 99.99f;System.out.println(price);
```

Output:

```
99.99
```

### Important

Float literals must end with:

```
f
```

or

```
F
```

---

# 6. double

## Definition

Stores large decimal values with higher precision.

### Example

```
double pi = 3.1415926535;System.out.println(pi);
```

Output:

```
3.1415926535
```

### Default Decimal Type

```
double num = 10.5;
```

is valid.

```
float num = 10.5;
```

is invalid.

---

# 7. char

## Definition

Stores a single character.

### Example

```
char grade = 'A';System.out.println(grade);
```

Output:

```
A
```

### Important

Characters use single quotes:

```
'A'
```

Not:

```
"A"
```

---

# 8. boolean

## Definition

Stores logical values.

Possible values:

```
truefalse
```

### Example

```
boolean isPassed = true;System.out.println(isPassed);
```

Output:

```
true
```

---

# Memory Order

From smallest to largest:

```
byte < short < int < long < float < double
```

Visualization:

```
byte  ↓short  ↓int  ↓long  ↓float  ↓double
```

---

# Example Program

```
public class Main {    public static void main(String[] args) {        byte age = 25;        short marks = 30000;        int salary = 50000;        long population = 8000000000L;        float price = 99.99f;        double pi = 3.14159;        char grade = 'A';        boolean passed = true;        System.out.println(age);        System.out.println(marks);        System.out.println(salary);        System.out.println(population);        System.out.println(price);        System.out.println(pi);        System.out.println(grade);        System.out.println(passed);    }}
```

---

# Interview Questions

### Q1. How many primitive data types are available in Java?

**Answer:** 8

```
byteshortintlongfloatdoublecharboolean
```

---

### Q2. Which data type is used for decimal values?

**Answer:**

```
floatdouble
```

---

### Q3. Which data type stores a single character?

```
char
```

---

### Q4. Which is the default integer type in Java?

```
int
```

---

### Q5. Which is the default decimal type?

```
double
```

---

# Summary

- Java has 8 primitive data types.
- `int` is the most commonly used integer type.
- `double` is the default decimal type.
- `char` stores a single character.
- `boolean` stores true/false values.
- Primitive data types are frequently asked in interviews and form the foundation of Java programming.

---

# 4. Comments in Java

## Introduction

Comments are notes written inside code for developers.

Comments are ignored by the Java compiler.

They help explain code and improve readability.

---

# Purpose of Comments

### Documentation

Describe program functionality.

### Explanation

Explain complex logic.

### Debugging

Temporarily disable code during testing.

---

# Types of Comments

Java supports:

```
1. Single-Line Comments2. Multi-Line Comments3. Documentation Comments (JavaDoc)
```

---

# 1. Single-Line Comment

## Syntax

```
// This is a comment
```

### Example

```
public class Main {    public static void main(String[] args) {        // Printing Hello World        System.out.println("Hello World");    }}
```

---

# 2. Multi-Line Comment

## Syntax

```
/*This isa multi-linecomment*/
```

### Example

```
/*Author: SangramProgram: First Java ProgramDate: 2026*/public class Main {}
```

---

# 3. JavaDoc Comment

## Syntax

```
/** * Documentation Comment */
```

### Example

```
/** * Calculates student marks */public class Student {}
```

Used with:

```
javadoc FileName.java
```

---

# Example Program

```
public class Main {    public static void main(String[] args) {        // Variable declaration        int age = 23;        /*         Multi-line comment         explaining age        */        System.out.println(age);    }}
```

---

# Interview Questions

### Q1. Does the compiler execute comments?

**Answer:** No.

Comments are ignored by the compiler.

---

### Q2. Why are comments used?

**Answer:**

- Documentation
- Explanation
- Debugging

---

### Q3. Which comment is used to generate documentation?

```
/** */
```

(JavaDoc Comment)

---

# Summary

- Comments improve code readability.
- Single-line comments use `//`.
- Multi-line comments use `/* */`.
- JavaDoc comments use `/** */`.
- Comments are ignored during compilation.

---

# 5. Literals in Java

## Definition

A **Literal** is a fixed value directly written in a program.

Literals are assigned to variables or used in expressions.

---

# Examples

```
1020.5'A'true"Hello"
```

All of the above are literals.

---

# Types of Literals

## Integer Literal

```
int age = 23;
```

Literal:

```
23
```

---

## Floating Point Literal

```
double salary = 45000.50;
```

Literal:

```
45000.50
```

---

## Character Literal

```
char grade = 'A';
```

Literal:

```
'A'
```

---

## Boolean Literal

```
boolean passed = true;
```

Literal:

```
true
```

---

## String Literal

```
String name = "Sangram";
```

Literal:

```
"Sangram"
```

---

# Example Program

```
public class Main {    public static void main(String[] args) {        int age = 23;        double salary = 50000.50;        char grade = 'A';        boolean passed = true;        String name = "Sangram";        System.out.println(age);        System.out.println(salary);        System.out.println(grade);        System.out.println(passed);        System.out.println(name);    }}
```

---

# Interview Questions

### Q1. What is a literal?

**Answer:**  
A literal is a fixed value directly written in a Java program.

---

### Q2. Is `"Hello"` a literal?

**Answer:** Yes, it is a String literal.

---

### Q3. Is `'A'` a literal?

**Answer:** Yes, it is a character literal.

---

# Summary

- Literals are fixed values.
- Examples include numbers, characters, strings, and boolean values.
- Literals are used to initialize variables.

---

# 6. Identifiers in Java

## Definition

**Identifiers** are names given to Java program elements such as:

- Variables
- Methods
- Classes
- Interfaces
- Packages

Identifiers help uniquely identify program components.

---

# Examples

### Variable

```
studentName
```

### Method

```
calculateSum
```

### Class

```
Employee
```

---

# Identifier Examples

```
int age = 23;String studentName = "Sangram";double salaryAmount = 50000;
```

Here:

```
agestudentNamesalaryAmount
```

are identifiers.

---

# Rules for Identifiers

## Rule 1: Can Contain Letters

```
studentEmployee
```

✅ Valid

---

## Rule 2: Can Contain Digits

```
student1emp2026
```

✅ Valid

---

## Rule 3: Can Contain Underscore (_)

```
student_nametotal_marks
```

✅ Valid

---

## Rule 4: Can Contain Dollar Sign ($)

```
salary$$total
```

✅ Valid (but not recommended)

---

## Rule 5: Cannot Start with a Digit

```
1student
```

❌ Invalid

Correct:

```
student1
```

✅ Valid

---

## Rule 6: Cannot Use Java Keywords

Wrong:

```
int class = 10;
```

❌ Invalid

Because:

```
class
```

is a keyword.

---

## Rule 7: No Spaces Allowed

Wrong:

```
student name
```

❌ Invalid

Correct:

```
studentName
```

✅ Valid

---

# Valid Identifiers

```
studentNameagesalaryAmountEmployeecalculateSum_marks$amount
```

---

# Invalid Identifiers

```
1studentstudent-namestudent nameclasspublic
```

---

# Naming Conventions

## Variable Names

Use camelCase:

```
studentNametotalMarkssalaryAmount
```

---

## Method Names

Use camelCase:

```
calculateSum()displayData()findMaximum()
```

---

## Class Names

Use PascalCase:

```
StudentEmployeeBankAccount
```

---

## Constants

Use UPPER_CASE:

```
MAX_VALUEPITAX_RATE
```

---

# Example Program

```
public class Employee {    public static void main(String[] args) {        String employeeName = "Sangram";        int employeeAge = 23;        System.out.println(employeeName);        System.out.println(employeeAge);    }}
```

---

# Interview Questions

### Q1. What is an identifier?

**Answer:**  
An identifier is a name used to identify variables, methods, classes, interfaces, and packages.

---

### Q2. Can an identifier start with a number?

**Answer:** No.

```
1student
```

is invalid.

---

### Q3. Can we use Java keywords as identifiers?

**Answer:** No.

```
int class = 10;
```

is invalid.

---

### Q4. Which characters are allowed in identifiers?

**Answer:**

```
LettersDigits_$
```

---

# Quick Revision

```
Identifiers      │      ▼Names Given To✔ Variables✔ Methods✔ Classes✔ Interfaces✔ Packages
```

### Rules

```
✔ Letters✔ Digits✔ _✔ $❌ Start with Digit❌ Keywords❌ Spaces
```

# Summary

- Identifiers are names used in Java programs.
- They can contain letters, digits, `_`, and `$`.
- They cannot start with digits.
- Java keywords cannot be used as identifiers.
- Follow camelCase for variables/methods and PascalCase for classes.
- Good naming conventions improve readability and maintainability.