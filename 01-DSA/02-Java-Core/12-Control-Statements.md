
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
int marks = 75;if(marks >= 90){    System.out.println("A");}else if(marks >= 70){    System.out.println("B");}else{    System.out.println("C");}
```

---

# 4. Loops

## What is a Loop?

A loop repeatedly executes a block of code.

### Types

```
for loopwhile loopdo while loop
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
for(int i=1;i<=5;i++){    System.out.println(i);}
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
int i = 1;while(i <= 5){    System.out.println(i);    i++;}
```

---

## Use Case

When number of iterations is unknown.

Example:

```
ATM SystemMenu Driven ProgramUser Input Based Program
```

---

# 7. Do While Loop

## Syntax

```
do{}while(condition);
```

---

## Example

```
int i = 1;do{    System.out.println(i);    i++;}while(i <= 5);
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
Print 1 to 100 → For LoopATM Menu → While Loop
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
int num = 10;while(num < 5){    System.out.println("Hello");}
```

Output:

```
Nothing
```

---

```
int num = 10;do{    System.out.println("Hello");}while(num < 5);
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
Input AInput BIf A > B    Print AElse    Print B
```

---

### Program

```
Scanner sc = new Scanner(System.in);int a = sc.nextInt();int b = sc.nextInt();if(a > b){    System.out.println(a);}else{    System.out.println(b);}
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
char ch = sc.next().charAt(0);if(ch >= 'A' && ch <= 'Z'){    System.out.println("Uppercase");}else{    System.out.println("Lowercase");}
```

---

## Fibonacci Series

### Example

```
0 1 1 2 3 5 8 13
```

---

### Logic

```
first = 0second = 1next = first + second
```

---

### Program

```
int a = 0;int b = 1;for(int i=1;i<=10;i++){    System.out.print(a + " ");    int temp = a + b;    a = b;    b = temp;}
```

---

## Counting Occurrences

### Example

```
Input Number = 45535Digit = 5Output = 3
```

---

### Logic

```
Take last digitCompareCount++Remove last digit
```

---

### Program

```
int count = 0;while(num > 0){    int rem = num % 10;    if(rem == 5){        count++;    }    num = num / 10;}
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