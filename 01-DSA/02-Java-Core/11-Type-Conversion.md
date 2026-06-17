## Introduction

Sometimes we need to store a value of one data type into another data type.

When Java automatically converts a smaller data type into a larger compatible data type, it is called **Type Conversion** or **Widening Conversion**.

---

# Definition

**Type Conversion** is the automatic conversion of one data type into another compatible data type by the Java compiler.

No explicit conversion is required.

---

# Example

```
int num = 10;float value = num;System.out.println(value);
```

Output:

```
10.0
```

Java automatically converts:

```
int → float
```

---

# Conversion Flow

Java automatically converts data in the following order:

```
byte ↓short ↓int ↓long ↓float ↓double
```

---

# Examples

## byte to int

```
byte num = 10;int value = num;System.out.println(value);
```

Output:

```
10
```

---

## int to double

```
int marks = 95;double result = marks;System.out.println(result);
```

Output:

```
95.0
```

---

## long to float

```
long population = 1000000L;float value = population;System.out.println(value);
```

Output:

```
1000000.0
```

---

# Why is Automatic Conversion Safe?

Because the destination type can hold all values of the source type.

Example:

```
int num = 100;double value = num;
```

```
int range     < double range
```

No data loss occurs.

---

# Conditions for Type Conversion

## Condition 1

Types must be compatible.

✔ Valid

```
int num = 10;float value = num;
```

---

## Condition 2

Destination type must be larger.

✔ Valid

```
int num = 10;double value = num;
```

❌ Invalid

```
double num = 10.5;int value = num;
```

Compilation Error.

---

# Memory Representation

```
int num = 1010 │ ▼float value = num10.0
```

Automatic widening occurs.

---

# Interview Questions

### Q1. What is Type Conversion?

**Answer:**  
Automatic conversion of a smaller data type into a larger compatible data type.

---

### Q2. Is explicit casting required in type conversion?

**Answer:** No.

Java performs it automatically.

---

### Q3. Why is type conversion safe?

**Answer:**  
Because the destination type is larger and can store all values of the source type.

---

# Summary

- Type conversion is automatic.
- Also called **Widening Conversion**.
- No data loss occurs.
- Destination type must be larger.
- Common examples: `int → long`, `int → float`, `float → double`.

---

# 9. Type Casting in Java (Explicit Casting)

## Introduction

When converting a larger data type into a smaller data type, Java does not perform the conversion automatically.

The programmer must explicitly instruct Java to perform the conversion.

This process is called **Type Casting**.

---

# Definition

**Type Casting** is the manual conversion of a larger data type into a smaller data type.

Also called **Narrowing Conversion**.

---

# Syntax

```
targetType variable = (targetType) value;
```

---

# Example

```
float marks = 89.5f;int result = (int) marks;System.out.println(result);
```

Output:

```
89
```

---

# Why is Output 89?

Original value:

```
89.5
```

After casting:

```
89
```

Decimal part is removed.

---

# Another Example

```
double salary = 50000.75;int amount = (int) salary;System.out.println(amount);
```

Output:

```
50000
```

---

# Why Does Java Require Explicit Casting?

Because data may be lost.

Example:

```
double num = 99.99;
```

If converted to:

```
int value = (int) num;
```

Output:

```
99
```

Decimal information is lost.

Therefore Java requires explicit conversion.

---

# Memory Representation

```
double99.99   │   ▼(int)   │   ▼99
```

Data loss occurs.

---

# Type Casting Flow

```
double  ↑float  ↑long  ↑int  ↑short  ↑byte
```

Moving upward is automatic.

Moving downward requires casting.

---

# Common Examples

## double → int

```
double num = 20.99;int value = (int) num;
```

Output:

```
20
```

---

## int → byte

```
int num = 100;byte value = (byte) num;
```

Output:

```
100
```

---

# Interview Questions

### Q1. What is Type Casting?

**Answer:**  
Manual conversion of a larger data type into a smaller data type.

---

### Q2. Why is casting required?

**Answer:**  
Because data loss may occur during narrowing conversion.

---

### Q3. What is the difference between conversion and casting?

|Type Conversion|Type Casting|
|---|---|
|Automatic|Manual|
|Widening|Narrowing|
|No data loss|Possible data loss|
|No cast operator|Cast operator required|

---

# Summary

- Type casting is manual conversion.
- Also called narrowing conversion.
- Explicit cast operator is required.
- Data loss may occur.
- Examples: `double → int`, `float → int`, `long → short`.

---

# 10. Automatic Type Promotion

## Introduction

During arithmetic operations, Java automatically promotes smaller data types to `int`.

This is known as **Automatic Type Promotion**.

---

# Definition

Automatic Type Promotion is the process in which Java converts smaller data types (`byte`, `short`, `char`) into `int` before performing arithmetic operations.

---

# Example

```
byte a = 10;byte b = 20;int result = a + b;System.out.println(result);
```

Output:

```
30
```

---

# Why is Result int?

Even though:

```
a → byteb → byte
```

Java internally converts:

```
byte → intbyte → int
```

before performing addition.

Therefore:

```
int result = a + b;
```

---

# Internal Process

```
byte a = 10byte b = 20      │      ▼int a = 10int b = 20      │      ▼int result = 30
```

---

# Example with char

```
char ch = 'A';int value = ch;System.out.println(value);
```

Output:

```
65
```

---

# Why 65?

Unicode value of:

```
A = 65
```

Java promotes the character to an integer.

---

# More Examples

## short

```
short x = 100;short y = 200;int result = x + y;
```

Result type:

```
int
```

---

## Mixed Data Types

```
int num = 10;double value = 20.5;double result = num + value;
```

Output:

```
30.5
```

Smaller type is promoted to larger type.

---

# Promotion Rules

```
byteshortchar    ↓   int    ↓   long    ↓  float    ↓ double
```

---

# Interview Questions

### Q1. What is Automatic Type Promotion?

**Answer:**  
Java automatically converts smaller data types into `int` during arithmetic operations.

---

### Q2. Why does `byte + byte` return int?

**Answer:**  
Because Java promotes both operands to `int` before performing the calculation.

---

### Q3. What is the output?

```
char ch = 'A';int value = ch;System.out.println(value);
```

Output:

```
65
```

---

# Summary

- Java promotes `byte`, `short`, and `char` to `int`.
- Arithmetic operations on smaller types produce an `int`.
- Mixed-type expressions are promoted to the largest type involved.
- Automatic type promotion improves calculation efficiency and consistency.

---

# 11. Unicode in Java

## Definition

Java uses the **Unicode Character Set** to represent characters from different languages around the world.

Each character has a unique Unicode value.

---

# Example 1

```
char ch = 'A';System.out.println((int) ch);
```

Output:

```
65
```

Unicode value:

```
A → 65
```

---

# Example 2

```
char ch = 'अ';System.out.println(ch);
```

Output:

```
अ
```

This is valid because Java supports Unicode.

---

# More Unicode Examples

```
char english = 'A';char hindi = 'अ';char marathi = 'क';
```

All are valid.

---

# Unicode Escape Sequence

```
char ch = '\u0041';System.out.println(ch);
```

Output:

```
A
```

---

# Why Unicode?

Unicode allows Java programs to support:

```
EnglishHindiMarathiChineseJapaneseArabicMany More Languages
```

---

# Character Storage

```
char ch = 'A';
```

A Java `char` occupies:

```
2 Bytes (16 Bits)
```

because Java uses Unicode.

---

# Interview Questions

### Q1. Which character set does Java use?

**Answer:** Unicode.

---

### Q2. What is the Unicode value of 'A'?

```
65
```

or

```
U+0041
```

---

### Q3. Is this valid?

```
char ch = 'अ';
```

**Answer:** Yes.

Because Java supports Unicode.

---

### Q4. How many bytes does a char occupy?

```
2 Bytes
```

---

# Quick Revision

```
Type Conversion   ↓AutomaticSmaller → LargerType Casting   ↓ManualLarger → SmallerAutomatic Promotion   ↓byte/short/char → intUnicode   ↓Java Character Set'A' → 65'अ' → Valid
```

# Summary

- **Type Conversion** is automatic widening conversion.
- **Type Casting** is manual narrowing conversion.
- **Automatic Type Promotion** converts smaller types to `int` during calculations.
- Java uses **Unicode** to support global languages.
- `char` stores Unicode characters and occupies **2 bytes**.
- These topics are frequently asked in Java fresher interviews and are fundamental for understanding variables, operators, and expressions.