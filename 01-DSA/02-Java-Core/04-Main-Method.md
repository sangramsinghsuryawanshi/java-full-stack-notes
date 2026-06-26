## Introduction

The **main() method** is the most important method in a Java application because program execution starts from this method.

Whenever you run a Java program, the **Java Virtual Machine (JVM)** looks for the `main()` method and starts executing the code inside it.

Without a valid `main()` method, a Java application cannot run.

---

# Syntax of Main Method

```
public static void main(String[] args){
}
```

---

# Complete Example

```
public class Main {    
	public static void main(String[] args) {        
		System.out.println("Hello Java");    
	}
}
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
Main obj = new Main();
obj.main(args);
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
public static void main(String[] args) {    
	System.out.println("Java");
}
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
JavaFullStack Developer
```

---

# Example Program

```
public class Main {    
	public static void main(String[] args) {        
		System.out.println(args[0]);        
		System.out.println(args[1]);        
		System.out.println(args[2]);    
	}
}
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
public class Main {    
	public static void main(String[] args) {        
		System.out.println(args.length);    
	}
}
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
User Runs Program        
		│        
	▼JVM Starts        
		│        
	▼Loads Class        
		│        
	▼Finds main()        
		│        
	▼Executes Statements        
		│        
	▼Program Ends
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
public static int main(String[] args){    
	return 0;
}
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


# 2. Introduction to Methods

## What is a Method?

A **method** is a block of reusable code that performs a specific task.

Instead of writing the same code multiple times, we write it once inside a method and call it whenever needed.

---

## Why Do We Need Methods?

Without methods:

```
System.out.println("Welcome");System.out.println("Welcome");System.out.println("Welcome");
```

With methods:

```
printMessage();printMessage();printMessage();
```

Advantages:

- Code Reusability
- Easy Maintenance
- Better Readability
- Reduces Code Duplication
- Easy Debugging

---

# Real-Life Example

Think of a TV remote.

Each button performs a specific task.

```
Volume Up → increaseVolume()Volume Down → decreaseVolume()Power → turnOn()Mute → mute()
```

Similarly, methods perform specific tasks.

---

# 2. Syntax of a Method

## General Syntax

```
accessModifier returnType methodName(parameters){    // method body}
```

---

## Example

```
public static void greet(){    System.out.println("Welcome to Java");}
```

---

## Components of a Method

```
public static void greet(){}
```

|Part|Description|
|---|---|
|public|Access Modifier|
|static|Can be called without creating an object|
|void|Returns nothing|
|greet|Method Name|
|()|Parameters|
|{}|Method Body|

---

# Calling a Method

```
public class Main {    static void greet(){        System.out.println("Hello");    }    public static void main(String[] args){        greet();    }}
```

Output

```
Hello
```

---

# Method Execution Flow

```
main()   ↓greet()   ↓Print Message   ↓Return to main()
```

---

# 3. Methods with Parameters

## Definition

Parameters allow values to be passed into methods.

---

## Syntax

```
static void display(String name){}
```

---

## Example

```
public class Main {    static void greet(String name){        System.out.println("Hello " + name);    }    public static void main(String[] args){        greet("John");        greet("Alice");    }}
```

Output

```
Hello JohnHello Alice
```

---

# Multiple Parameters

```
static void student(String name, int age){    System.out.println(name + " " + age);}
```

Calling

```
student("Rahul",22);
```

Output

```
Rahul 22
```

---

# Parameter vs Argument

|Parameter|Argument|
|---|---|
|Variable in method definition|Actual value passed|
|Placeholder|Real Data|

Example

```
void add(int a,int b)
```

Here

```
a,b
```

are Parameters.

Calling

```
add(10,20);
```

Here

```
10,20
```

are Arguments.

---

# 4. Returning Values

## Definition

Some methods return a value after completing their work.

---

## Syntax

```
return value;
```

---

## Example

```
static int sum(int a,int b){    return a+b;}
```

Calling

```
int result = sum(10,20);System.out.println(result);
```

Output

```
30
```

---

# Return Types

|Return Type|Meaning|
|---|---|
|void|Returns nothing|
|int|Integer value|
|double|Decimal value|
|boolean|true or false|
|char|Character|
|String|Text|

---

# Example Returning String

```
static String country(){    return "India";}
```

Calling

```
System.out.println(country());
```

Output

```
India
```

---

# Example Returning Boolean

```
static boolean isAdult(int age){    return age>=18;}
```

Calling

```
System.out.println(isAdult(20));
```

Output

```
true
```

---

# 5. Method Scope

## Definition

Variables declared inside a method exist only inside that method.

---

## Example

```
static void test(){    int x = 10;}
```

Outside the method:

```
System.out.println(x);
```

Output

```
Compilation Error
```

Reason:

Variable `x` exists only inside `test()`.

---

# Method Scope Diagram

```
main()↓test()↓x exists only here↓Method Ends↓x destroyed
```

---

# 6. Block Scope

## Definition

Variables declared inside `{}` exist only inside that block.

---

## Example

```
if(true){    int age = 20;    System.out.println(age);}
```

Outside block

```
System.out.println(age);
```

Output

```
Compilation Error
```

---

# Block Scope Diagram

```
{age exists}↓Destroyed
```

---

# 7. Loop Scope

## Definition

Variables declared inside loops exist only inside that loop.

---

## Example

```
for(int i=1;i<=5;i++){    System.out.println(i);}
```

Outside loop

```
System.out.println(i);
```

Output

```
Compilation Error
```

---

# Example

```
while(true){    int x = 5;}
```

Outside

```
System.out.println(x);
```

Invalid.

---

# Loop Scope Diagram

```
Loop Starts↓Variable Created↓Loop Ends↓Variable Destroyed
```

---

# 8. Shadowing

## Definition

When a local variable has the same name as a class variable, the local variable hides (shadows) the class variable.

---

## Example

```
public class Main {    static int x = 90;    public static void main(String[] args){        System.out.println(x);        int x = 40;        System.out.println(x);    }}
```

Output

```
9040
```

Explanation:

- Before local declaration, `x` refers to the class variable.
- After local declaration, the local `x` shadows the class variable.

---

# Shadowing Diagram

```
Class Variablex = 90↓Local Variablex = 40↓Local variable takes priority
```

---

# 9. Variable Arguments (Varargs)

## Definition

Varargs allow a method to accept any number of arguments.

---

## Syntax

```
static void fun(int... numbers){}
```

---

## Example

```
static void sum(int... nums){    for(int n : nums){        System.out.print(n+" ");    }}
```

Calling

```
sum(10);sum(10,20);sum(10,20,30,40);
```

Output

```
1010 2010 20 30 40
```

---

## Rules

- Only one varargs parameter is allowed.
- It must be the last parameter.

Valid

```
void display(String name,int... marks)
```

Invalid

```
void display(int... marks,String name)
```

---

# 10. Method Overloading

## Definition

Method Overloading means creating multiple methods with the same name but different parameter lists.

---

## Why?

It improves readability and allows similar operations with different inputs.

---

## Example

```
static int sum(int a,int b){    return a+b;}static int sum(int a,int b,int c){    return a+b+c;}
```

Calling

```
System.out.println(sum(10,20));System.out.println(sum(10,20,30));
```

Output

```
3060
```

---

# Another Example

```
static void print(int num){    System.out.println(num);}static void print(String text){    System.out.println(text);}
```

---

# Overloading Rules

Method overloading requires different:

- Number of parameters
- Types of parameters
- Order of parameter types

---

## Invalid Overloading

Changing only the return type is **not** overloading.

```
int sum(int a,int b)
```

```
double sum(int a,int b)
```

❌ Compilation Error

---

# Method Overloading Diagram

```
sum()↓sum(int,int)↓sum(int,int,int)↓sum(double,double)
```

---

# Common Interview Questions

### 1. What is a Method?

A reusable block of code that performs a specific task.

---

### 2. Why do we use Methods?

- Reusability
- Readability
- Easy Maintenance
- Reduced Code Duplication

---

### 3. What is the difference between Parameter and Argument?

|Parameter|Argument|
|---|---|
|Declared in method|Passed while calling|

---

### 4. What is a Return Type?

The type of value returned by a method.

---

### 5. What is `void`?

A method with `void` does not return any value.

---

### 6. What is Scope?

The region where a variable is accessible.

---

### 7. What is Shadowing?

A local variable hides a class variable having the same name.

---

### 8. What are Varargs?

A feature that allows methods to accept a variable number of arguments using `...`.

---

### 9. What is Method Overloading?

Creating multiple methods with the same name but different parameter lists.

---

### 10. Can we overload methods by changing only the return type?

**No.** The parameter list must be different.

---

# Quick Revision

```
Method ↓Reusable Block of CodeMethod Call ↓Executes MethodParameters ↓Input VariablesArguments ↓Actual ValuesReturn ↓Sends Result BackMethod Scope ↓Inside Method OnlyBlock Scope ↓Inside {}Loop Scope ↓Inside Loop OnlyShadowing ↓Local Variable Hides Class VariableVarargs ↓Accept Multiple ArgumentsMethod Overloading ↓Same Method NameDifferent Parameters
```