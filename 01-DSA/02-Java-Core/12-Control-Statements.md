
# 1. Conditions

## What are Conditions?

Conditions are expressions that evaluate to:

```
true false
```

Example:

```
age >= 18
num > 100
marks < 35
```

Used for decision making.

---

# 2. If-Else Statement

## Syntax

```
if(condition)
{    // code
}else{    
	// code
}
```

---

## Example

```
int age = 20;
if(age >= 18)
{    
	System.out.println("Eligible");
}else{    
	System.out.println("Not Eligible");
}
```

### Flow

```
Condition    
	↓
True  → If BlockFalse → Else Block
```

---

# 3. Multiple If-Else

## Syntax

```
if(condition1){

}else if(condition2){

}else{

}
```

---

## Example

```
int marks = 75;
if(marks >= 90){    
	System.out.println("A");
}else if(marks >= 70){    
	System.out.println("B");
}else{    
	System.out.println("C");
}
```

---

# 4. Loops

## What is a Loop?

A loop repeatedly executes a block of code.

### Types

```
for loop, while loop, do while loop
```

---

# 5. For Loop

## Syntax

```
for(initialization; condition; update){}
```

---

## Example

```
for(int i=1;i<=5;i++){    
	System.out.println(i);
}
```

Output:

```
12345
```

---

## Flow

```
Initialize     ↓Condition     ↓Execute     ↓Update     ↓Condition
```

---

# 6. While Loop

## Syntax

```
while(condition){}
```

---

## Example

```
int i = 1;while(i <= 5){    
	System.out.println(i);    
	i++;
}
```

---

## Use Case

When number of iterations is unknown.

Example:

```
ATM System Menu Driven Program User Input Based Program
```

---

# 7. Do While Loop

## Syntax

```
do{
}while(condition);
```

---

## Example

```
int i = 1;
do{    
	System.out.println(i);    
	i++;
}while(i <= 5);
```

---

## Important

Loop executes at least one time.

---

# 8. For vs While

|For Loop|While Loop|
|---|---|
|Known iterations|Unknown iterations|
|Counter based|Condition based|
|Compact syntax|More flexible|

### Example

```
Print 1 to 100 → For Loop ATM Menu → While Loop
```

---

# 9. While vs Do While

|While|Do While|
|---|---|
|Checks condition first|Executes first|
|May run 0 times|Runs at least 1 time|

---

## Example

```
int num = 10;
while(num < 5)
{    
	System.out.println("Hello");
}
```

Output:

```
Nothing
```

---

```
int num = 10;
do{    
	System.out.println("Hello");
}while(num < 5);
```

Output:

```
Hello
```

---

# 10. Practice Problems

---

## Largest Number

### Logic

```
Input A
Input B
If A > B    
	Print A
Else    
	Print B
```

---

### Program

```
Scanner sc = new Scanner(System.in);
int a = sc.nextInt();
int b = sc.nextInt();
if(a > b){    
System.out.println(a);
}else{    
System.out.println(b);
}
```

---

## Alphabet Case Check

### Logic

```
A-Z → Uppercasea-z → Lowercase
```

---

### Program

```
char ch = sc.next().charAt(0);
if(ch >= 'A' && ch <= 'Z'){    
	System.out.println("Uppercase");
}else{    
	System.out.println("Lowercase");
}
```

---

## Fibonacci Series

### Example

```
for(int i = 1; i <= 100; i++) {    
	System.out.println(i);
}
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
ATM SystemMenu Driven ProgramsLogin SystemGame LoopRead Input Until Exit main
```

---

### Logic

```
first = 0
second = 1
next = first + second
```

---

### Program

```
int a = 0;int b = 1;
for(int i=1;i<=10;i++){    
	System.out.print(a + " ");    
	int temp = a + b;    
	a = b;    
	b = temp;
}
```

---

## Counting Occurrences

### Example

```
Input Number = 45535
Digit = 5
Output = 3
```

---

### Logic

```
Take last digitCompareCount++Remove last digit
```

---

### Program

```
int count = 0;
while(num > 0){    
	int rem = num % 10;    
	if(rem == 5){        
		count++;   
	}    
	num = num / 10;
}
```

---

## Reverse Number

### Example

```
Input 1234 Output 4321
```

---

### Program

```
int rev = 0;
while(num > 0)
{    
	int rem = num % 10;    
	rev = rev * 10 + rem;    
	num = num / 10;
}
```

---

## Calculator Program

### Operations

```
+-*/%
```

---

### Example

```
char op = sc.next().charAt(0);if(op == '+'){    System.out.println(a+b);}else if(op == '-'){    System.out.println(a-b);}
```

---

# Interview Questions

```
1. What is a control statement?
2. Difference between if and if-else?
3. Difference between for and while loop?
4. Difference between while and do while?
5. What is an infinite loop?6. How to reverse a number?
6. How to find Fibonacci numbers?
7. How to count occurrences of a digit?
```

---

# Quick Revision

```
if → Decision Makingif-else → Two Choiceselse if → Multiple Choicesfor → Known Iterationswhile → Unknown Iterationsdo while → Executes At Least OnceLargest Number → ComparisonFibonacci → Previous Two Numbers SumCount Occurrences → %10 and /10Reverse Number → rev = rev*10 + rem
```


# 11. Switch Statement

## Introduction

When multiple conditions depend on a single variable, writing many `if-else` statements becomes difficult.

Java provides the **switch statement** for such situations.

---

# Definition

A switch statement selects and executes one block of code from multiple choices.

---

# Syntax

```
switch(expression) {    case value1:        // code        break;    case value2:        // code        break;    default:        // code}
```

---

# Flow Diagram

```
Expression     ↓Case 1 ?     ↓Case 2 ?     ↓Case 3 ?     ↓Default
```

---

# How Switch Works

1. Expression is evaluated.
2. Matching case is found.
3. Corresponding block executes.
4. `break` exits the switch.
5. If no match exists, `default` executes.

---

# Important Notes

## Rule 1

Expression can be:

```
byteshortintcharStringenum
```

---

## Rule 2

Case values must be constants.

Valid:

```
case 1:case 'A':case "Java":
```

---

## Rule 3

Duplicate cases are not allowed.

❌ Invalid

```
case 1:case 1:
```

---

## Rule 4

Default is optional.

---

## Rule 5

Break prevents fall-through.

---

# Program 1: Describe Fruit

## Problem

Input fruit name and display description.

---

## Program

```
import java.util.Scanner;
public class Main {    
	public static void main(String[] args) {        
		Scanner sc = new Scanner(System.in);        
		String fruit = sc.next();        
		switch(fruit) {            
			case "Mango":                
				System.out.println("King of Fruits");                
			break;            
			case "Apple":                
				System.out.println("Keeps doctor away");                
			break;            
			case "Banana":                
				System.out.println("Rich in Potassium");                
			break;            
			default:                
				System.out.println("Unknown Fruit");        
			break;
		}    
	}
}
```

---

## Output

Input:

```
Mango
```

Output:

```
King of Fruits
```

---

# Break Statement

## Why Needed?

Without break, execution continues into the next cases.

---

## Example

```
int num = 2;switch(num){    case 1:        System.out.println("One");    case 2:        System.out.println("Two");    case 3:        System.out.println("Three");}
```

Output:

```
TwoThree
```

This is called:

```
Fall Through
```

---

# Correct Version

```
switch(num){    case 1:        System.out.println("One");        break;    case 2:        System.out.println("Two");        break;    case 3:        System.out.println("Three");        break;}
```

Output:

```
Two
```

---

# Enhanced Switch (Java 14+)

## Introduction

Java introduced a shorter switch syntax.

---

# Syntax

```
switch(expression){    case value -> statement;}
```

---

# Example

```
String fruit = "Mango";switch(fruit){    case "Mango" ->        System.out.println("King of Fruits");    case "Apple" ->        System.out.println("Keeps doctor away");    default ->        System.out.println("Unknown Fruit");}
```

---

# Benefits

- Cleaner syntax
- No break needed
- Less code
- Easy to read

---

# Program 2: Display Day Name (1–7)

## Problem

Input a number and display the day name.

---

## Program

```
int day = 3;switch(day){    case 1:        System.out.println("Monday");        break;    case 2:        System.out.println("Tuesday");        break;    case 3:        System.out.println("Wednesday");        break;    case 4:        System.out.println("Thursday");        break;    case 5:        System.out.println("Friday");        break;    case 6:        System.out.println("Saturday");        break;    case 7:        System.out.println("Sunday");        break;    default:        System.out.println("Invalid Day");}
```

Output:

```
Wednesday
```

---

# Enhanced Version

```
switch(day){    case 1 -> System.out.println("Monday");    case 2 -> System.out.println("Tuesday");    case 3 -> System.out.println("Wednesday");    case 4 -> System.out.println("Thursday");    case 5 -> System.out.println("Friday");    case 6 -> System.out.println("Saturday");    case 7 -> System.out.println("Sunday");    default -> System.out.println("Invalid Day");}
```

---

# Program 3: Weekdays and Weekends

## Problem

Display whether a day is a weekday or weekend.

---

## Program

```
int day = 6;switch(day){    case 1:    case 2:    case 3:    case 4:    case 5:        System.out.println("Weekday");        break;    case 6:    case 7:        System.out.println("Weekend");        break;    default:        System.out.println("Invalid Day");}
```

Output:

```
Weekend
```

---

# Enhanced Switch Version

```
switch(day){    case 1,2,3,4,5 ->        System.out.println("Weekday");    case 6,7 ->        System.out.println("Weekend");    default ->        System.out.println("Invalid Day");}
```

---

# Nested Switch Case

## Definition

A switch statement inside another switch statement.

---

# Example

```
int empId = 101;String department = "IT";switch(empId){    case 101:        switch(department){            case "IT":                System.out.println("Developer");                break;            case "HR":                System.out.println("HR Executive");                break;        }        break;    default:        System.out.println("Employee Not Found");}
```

Output:

```
Developer
```

---

# When to Use Nested Switch?

Use when:

- Multiple levels of selection are needed.
- One decision depends on another.

Examples:

- Employee → Department
- Country → State
- College → Department

---

# Switch vs If-Else

|Switch|If-Else|
|---|---|
|Multiple fixed choices|Complex conditions|
|Faster for many cases|More flexible|
|Cleaner syntax|Can become lengthy|
|Equality checks|Any logical condition|

---

# When to Use Switch?

Use switch when:

✅ Multiple fixed options exist

Examples:

```
Menu ProgramsCalculatorDay NamesMonthsGradesFruit NamesDepartment Selection
```

---

# Interview Questions

### Q1. Difference between == and .equals()?

**Answer:**

```
==       → Reference Comparison.equals() → Content Comparison
```

---

### Q2. What is a Switch Statement?

A control statement used to select one block from multiple choices.

---

### Q3. Why is break used?

To stop execution and prevent fall-through.

---

### Q4. What is default in switch?

Executes when no case matches.

---

### Q5. What is Fall Through?

Execution continues into subsequent cases when break is omitted.

---

### Q6. What is Enhanced Switch?

A modern switch syntax introduced in newer Java versions using `->`.

---

### Q7. Can switch work with String?

✅ Yes

```
switch(name){}
```

---

### Q8. Can switch use double?

❌ No

Valid types:

```
byteshortintcharStringenum
```

---

# Quick Revision

```
== vs .equals()==       → Reference.equals  → ContentSwitch Statementswitch(expression){    case value:        break;    default:}
```

```
Traditional Switch ↓Requires breakEnhanced Switch ↓Uses ->No break needed
```

```
Weekday1 2 3 4 5Weekend6 7
```

# Summary

- `==` compares references for objects and values for primitives.
- `.equals()` compares actual content and should be used for Strings.
- `switch` is useful for multiple fixed choices.
- `break` prevents fall-through.
- `default` executes when no case matches.
- Enhanced switch (`->`) is cleaner and does not require break.
- Nested switches allow multi-level decision-making.
- Switch statements are frequently asked in Java interviews and coding assessments.