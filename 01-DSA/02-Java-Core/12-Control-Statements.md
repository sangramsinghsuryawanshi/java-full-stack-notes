## Introduction

In real-world programs, decisions need to be made based on conditions.

Examples:

- Check if a student passed or failed.
- Check if a person is eligible to vote.
- Check if a user entered the correct password.

Java provides the **if statement** for decision-making.

---

# Definition

The **if statement** executes a block of code only when a specified condition is true.

---

# Syntax

```
if(condition) {}
```

---

# Flow Diagram

```
Condition    ↓ True → Execute Block False → Skip Block
```

---

# Example 1: Voting Eligibility

```
int age = 20;if(age >= 18) {    System.out.println("Eligible");}
```

Output:

```
Eligible
```

---

# How It Works?

```
age = 20Condition:20 >= 18True ↓Execute Statement
```

---

# Example 2: Condition False

```
int age = 15;if(age >= 18) {    System.out.println("Eligible");}
```

Output:

```
(No Output)
```

Because:

```
15 >= 18False ↓Skipped
```

---

# Using Multiple Statements

```
int marks = 80;if(marks >= 40) {    System.out.println("Passed");    System.out.println("Congratulations");}
```

Output:

```
PassedCongratulations
```

---

# Relational Operators Used in if

|Operator|Meaning|
|---|---|
|>|Greater Than|
|<|Less Than|
|>=|Greater Than or Equal|
|<=|Less Than or Equal|
|==|Equal To|
|!=|Not Equal To|

---

# Example

```
int number = 10;if(number == 10) {    System.out.println("Correct");}
```

Output:

```
Correct
```

---

# Common Beginner Mistake

Wrong:

```
if(age = 18)
```

❌ Invalid

Correct:

```
if(age == 18)
```

✅ Valid

---

# Real-Life Examples

## ATM Withdrawal

```
if(balance >= amount) {    System.out.println("Withdrawal Successful");}
```

---

## Login Validation

```
if(password.equals("admin123")) {    System.out.println("Login Successful");}
```

---

# Interview Questions

### Q1. What is an if statement?

**Answer:**  
An if statement executes a block of code only when a specified condition is true.

---

### Q2. What happens if the condition is false?

**Answer:**  
The block is skipped.

---

### Q3. Which operators are commonly used in if statements?

**Answer:**

```
><>=<===!=
```

---

# Quick Revision

```
if(condition){    statements;}Condition    ↓True  → ExecuteFalse → Skip
```

---

# Summary

- `if` is used for decision-making.
- Executes code only when the condition is true.
- False conditions skip execution.
- Commonly used with relational operators.
- One of the most important control statements in Java.

---

# 13. While Loop in Java

## Introduction

A loop is used to execute a block of code repeatedly.

The **while loop** continues executing as long as the condition remains true.

---

# Definition

A **while loop** repeatedly executes a block of code while the specified condition is true.

---

# Syntax

```
while(condition) {}
```

---

# Flow Diagram

```
Condition    ↓ True → Execute Loop Body    ↑    | False → Exit Loop
```

---

# Example

```
int i = 1;while(i <= 5) {    System.out.println(i);    i++;}
```

Output:

```
12345
```

---

# Execution Flow

```
i = 11 <= 5 ✔Print 12 <= 5 ✔Print 23 <= 5 ✔Print 34 <= 5 ✔Print 45 <= 5 ✔Print 56 <= 5 ✘Stop
```

---

# Infinite Loop Example

```
while(true) {    System.out.println("Java");}
```

Output:

```
JavaJavaJava...
```

Runs forever until manually stopped.

---

# Real-Life Example

## Reading User Input

```
while(userChoice != 0) {    // Continue Program}
```

---

# Use When

Use a while loop when:

✅ Number of iterations is unknown.

Examples:

- ATM System
- Menu-driven programs
- Reading input until user exits
- Login validation

---

# Interview Questions

### Q1. What is a while loop?

**Answer:**  
A loop that executes repeatedly while a condition is true.

---

### Q2. When should we use a while loop?

**Answer:**  
When the number of iterations is unknown.

---

### Q3. What causes an infinite loop?

**Answer:**  
When the condition never becomes false.

Example:

```
while(true) {}
```

---

# Quick Revision

```
while(condition){    statements;}Best For:✔ Unknown Iterations✔ Input Driven Programs✔ Menu Systems
```

---

# Summary

- While loop is an entry-controlled loop.
- Condition is checked before execution.
- Runs repeatedly until the condition becomes false.
- Best when iteration count is not known beforehand.

---

# 14. For Loop in Java

## Introduction

The **for loop** is used when the number of iterations is known in advance.

It combines initialization, condition, and update in one place.

---

# Definition

A **for loop** repeatedly executes a block of code for a known number of iterations.

---

# Syntax

```
for(initialization; condition; update) {}
```

---

# Components

## Initialization

Runs once.

```
int i = 1;
```

---

## Condition

Checked before every iteration.

```
i <= 5
```

---

## Update

Changes loop variable.

```
i++
```

---

# Example

```
for(int i = 1; i <= 5; i++) {    System.out.println(i);}
```

Output:

```
12345
```

---

# Execution Flow

```
Initialize i = 1      ↓Check Condition      ↓Execute Body      ↓Update i      ↓Repeat
```

---

# Example: Print 1 to 10

```
for(int i = 1; i <= 10; i++) {    System.out.println(i);}
```

---

# Example: Print Even Numbers

```
for(int i = 2; i <= 10; i += 2) {    System.out.println(i);}
```

Output:

```
246810
```

---

# Real-Life Example

Generate employee IDs:

```
for(int id = 1; id <= 100; id++) {    System.out.println(id);}
```

---

# Use When

Use a for loop when:

✅ Number of iterations is known.

Examples:

- Print 1 to 100
- Process array elements
- Generate reports
- Perform repeated calculations

---

# Interview Questions

### Q1. What are the three parts of a for loop?

**Answer:**

```
InitializationConditionUpdate
```

---

### Q2. When should we use a for loop?

**Answer:**  
When the number of iterations is known.

---

### Q3. Is for loop entry-controlled?

**Answer:** Yes.

Condition is checked before execution.

---

# Quick Revision

```
for(initialization;    condition;    update){    statements;}Best For:✔ Known Iterations✔ Counting Tasks✔ Arrays
```

---

# Summary

- For loop is ideal for fixed repetitions.
- Combines initialization, condition, and update.
- Easy to read and maintain.
- Most commonly used loop in Java.

---

# 15. While Loop vs For Loop

## Introduction

Both `while` and `for` loops are used to repeat code execution.

The choice depends on whether the number of iterations is known.

---

# Comparison Table

|While Loop|For Loop|
|---|---|
|Unknown iterations|Known iterations|
|Condition written separately|Initialization, condition, update together|
|Best for input-driven programs|Best for counting tasks|
|More flexible|More compact|
|Common in menu systems|Common in arrays and counting|

---

# Syntax Comparison

## While Loop

```
int i = 1;while(i <= 5) {    System.out.println(i);    i++;}
```

---

## For Loop

```
for(int i = 1; i <= 5; i++) {    System.out.println(i);}
```

---

# Example: Print 1 to 100

### Using For Loop

```
for(int i = 1; i <= 100; i++) {    System.out.println(i);}
```

✅ Recommended

Because iterations are known.

---

# Example: ATM System

```
while(choice != 0) {    
// Show Menu
}
```

✅ Recommended

Because user decides when to exit.

---

# Real-Life Examples

### While Loop

```
ATM SystemMenu Driven ProgramsLogin SystemGame LoopRead Input Until Exit
```

---

### For Loop

```
Print 1 to 100Display TableTraverse ArrayGenerate ReportsCount Numbers
```

---

# Interview Questions

### Q1. Difference between while and for loop?

**Answer:**

|While|For|
|---|---|
|Unknown iterations|Known iterations|
|Input driven|Counting driven|
|Condition only|Initialization + Condition + Update|

---

### Q2. Which loop is best for printing 1 to 100?

**Answer:** `for` loop.

---

### Q3. Which loop is best for an ATM system?

**Answer:** `while` loop.

Because the number of operations is not known beforehand.

---

# Quick Revision

```
While Loop     ↓Unknown IterationsExamples:ATMMenuLoginUser Input
```

```
For Loop     ↓Known IterationsExamples:1 to 100TablesArraysCounting
```

# Summary

- Both loops repeat code execution.
- Use **while** when iterations are unknown.
- Use **for** when iterations are known.
- While loops are common in user-driven applications.
- For loops are common in counting and array processing.
- Choosing the correct loop improves code readability and efficiency.