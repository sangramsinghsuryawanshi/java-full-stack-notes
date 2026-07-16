# Object-Oriented Programming System (OOPS) in Java – Complete Notes

---

# 1. What is OOPS?

## Definition

**Object-Oriented Programming System (OOPS)** is a programming paradigm that organizes software using **objects** instead of functions and logic.

An object contains:

- Data (Fields/Variables)
- Behavior (Methods)

Java is an **Object-Oriented Programming Language** because it supports the four main OOP principles.

---

# Four Pillars of OOPS

```
                OOPS
                  │
      ┌───────────┼───────────┐
      │           │           │
      ▼           ▼           ▼
 Encapsulation  Inheritance  Polymorphism
                  │
                  ▼
             Abstraction
```

---

# Real-Life Example

Imagine a **Car**.

A car has:

Data:

- Color
- Brand
- Speed

Behavior:

- Start()
- Stop()
- Accelerate()

Here,

Car = Object

---

# Advantages of OOPS

- Code Reusability
- Better Security
- Easy Maintenance
- Scalability
- Modularity
- Real-world Modeling
- Easier Testing

---

# Class and Object

## What is a Class?

A class is a blueprint or template for creating objects.

Example:

```
class Student{

    int rollNo;
    String name;

    void display(){

        System.out.println(name);

    }
}
```

---

## What is an Object?

An object is a real-world entity created from a class.

Example:

```
Student s1 = new Student();

s1.name = "Rahul";

s1.display();
```

---

# Relationship

```
Class
 ↓
Blueprint

↓

Object

↓

Real Instance
```

---

# 1. Encapsulation

---

## Definition

Encapsulation means **wrapping data and methods together into a single unit (class)** and protecting data using **private access**.

Also called:

```
Data Hiding
```

---

## Why Encapsulation?

Without encapsulation:

Anyone can modify data.

```
account.balance = -50000;
```

Invalid.

---

With encapsulation:

Data is protected.

Only methods can access it.

---

## Example

```
class BankAccount{

    private double balance;

    public void deposit(double amount){

        if(amount > 0){

            balance += amount;

        }

    }

    public double getBalance(){

        return balance;

    }

}
```

Using

```
BankAccount account = new BankAccount();

account.deposit(5000);

System.out.println(account.getBalance());
```

Output

```
5000
```

---

## Diagram

```
User

↓

Getter / Setter

↓

Private Data
```

---

## Advantages

- Data Security
- Validation
- Controlled Access
- Better Maintainability

---

## Real-Life Example

ATM Machine

You cannot directly access bank balance.

You use:

- Deposit
- Withdraw
- Check Balance

---

# Interview Questions

### What is Encapsulation?

Wrapping data and methods together and hiding data using private variables.

---

### Which keyword is used?

```
private
```

---

### How is data accessed?

Using

```
Getter
Setter
```

---

# 2. Abstraction

---

## Definition

Abstraction means **showing only essential features and hiding implementation details**.

---

## Real-Life Example

Driving a car.

You know:

- Accelerator
- Brake
- Steering

You don't know:

- Engine combustion
- Gear mechanism
- Fuel injection

Implementation is hidden.

---

## Example Using Abstract Class

```
abstract class Animal{

    abstract void sound();

}
```

Subclass

```
class Dog extends Animal{

    void sound(){

        System.out.println("Bark");

    }

}
```

Using

```
Animal a = new Dog();

a.sound();
```

Output

```
Bark
```

---

## Example Using Interface

```
interface Shape{

    void draw();

}
```

Implementation

```
class Circle implements Shape{

    public void draw(){

        System.out.println("Drawing Circle");

    }

}
```

---

## Diagram

```
User

↓

Uses Method

↓

Implementation Hidden
```

---

## Advantages

- Security
- Cleaner Code
- Loose Coupling
- Easier Maintenance

---

## Interview Questions

### Which keywords support abstraction?

```
abstract

interface
```

---

### Can abstract classes have constructors?

Yes.

---

### Can abstract class have normal methods?

Yes.

---

# 3. Inheritance

---

## Definition

Inheritance allows one class to acquire the properties and methods of another class.

Also called:

```
Code Reusability
```

---

## Syntax

```
class Child extends Parent{

}
```

---

## Example

```
class Animal{

    void eat(){

        System.out.println("Eating");

    }

}
```

Child

```
class Dog extends Animal{

    void bark(){

        System.out.println("Barking");

    }

}
```

Using

```
Dog d = new Dog();

d.eat();

d.bark();
```

Output

```
Eating

Barking
```

---

## Diagram

```
Animal

↓

Dog
```

---

## Types of Inheritance

### Single

```
A

↓

B
```

---

### Multilevel

```
A

↓

B

↓

C
```

---

### Hierarchical

```
      A

   /     \

  B       C
```

---

### Multiple

Not supported using classes.

Supported using interfaces.

---

### Hybrid

Combination of inheritance.

Implemented using interfaces.

---

## Advantages

- Code Reuse
- Reduced Duplication
- Easy Maintenance

---

## Interview Questions

### Which keyword is used?

```
extends
```

---

### Why doesn't Java support multiple inheritance using classes?

To avoid

```
Diamond Problem
```

---

# 4. Polymorphism

---

## Definition

Polymorphism means

```
One Name

↓

Many Forms
```

Same method behaves differently depending on the object.

---

## Types

```
Compile Time

(Method Overloading)

Runtime

(Method Overriding)
```

---

# Compile-Time Polymorphism

Method Overloading

Example

```
class MathUtil{

    int add(int a,int b){

        return a+b;

    }

    int add(int a,int b,int c){

        return a+b+c;

    }

}
```

---

Output

```
30

60
```

---

# Runtime Polymorphism

Method Overriding

Parent

```
class Animal{

    void sound(){

        System.out.println("Animal Sound");

    }

}
```

Child

```
class Dog extends Animal{

    @Override

    void sound(){

        System.out.println("Bark");

    }

}
```

Using

```
Animal a = new Dog();

a.sound();
```

Output

```
Bark
```

---

## Diagram

```
Animal

↓

Dog

↓

sound()

↓

Different Output
```

---

## Advantages

- Flexibility
- Runtime Decision Making
- Loose Coupling

---

# Method Overloading vs Overriding

|Overloading|Overriding|
|---|---|
|Same class|Parent & Child|
|Different parameters|Same parameters|
|Compile Time|Runtime|
|No inheritance required|Inheritance required|

---

# OOPS Relationship

```
               OOPS

                 │

   ┌─────────────┼─────────────┐

   ▼             ▼             ▼

Encapsulation Inheritance Polymorphism

                 │

                 ▼

           Abstraction
```

---

# Real-Life Example (Bank System)

### Encapsulation

```
Balance is Private

Access using Deposit()

Withdraw()
```

---

### Abstraction

```
User clicks

Withdraw

↓

Internal Banking Process Hidden
```

---

### Inheritance

```
Account

↓

Savings Account

↓

Current Account
```

---

### Polymorphism

```
Payment()

↓

UPI

↓

Card

↓

Net Banking
```

Different implementations of the same operation.

---

# Comparison of Four Pillars

|Feature|Encapsulation|Abstraction|Inheritance|Polymorphism|
|---|---|---|---|---|
|Purpose|Data Hiding|Hide Implementation|Code Reuse|Multiple Behaviors|
|Keyword|`private`|`abstract`, `interface`|`extends`, `implements`|Overloading/Overriding|
|Achieved By|Getters & Setters|Abstract Class / Interface|Parent-Child Classes|Method Overloading & Overriding|
|Focus|Security|Simplicity|Reusability|Flexibility|

---

# Frequently Asked Interview Questions

### 1. What are the four pillars of OOPS?

- Encapsulation
- Abstraction
- Inheritance
- Polymorphism

---

### 2. Difference between Encapsulation and Abstraction?

|Encapsulation|Abstraction|
|---|---|
|Hides data|Hides implementation|
|Uses private variables|Uses abstract classes/interfaces|
|Focuses on security|Focuses on simplicity|

---

### 3. Difference between Overloading and Overriding?

|Overloading|Overriding|
|---|---|
|Compile-time|Runtime|
|Same class|Parent-child classes|
|Different parameters|Same parameters|

---

### 4. Which keyword is used for inheritance?

```
extends
```

---

### 5. Can Java support multiple inheritance?

- **Classes:** ❌ No
- **Interfaces:** ✅ Yes (a class can implement multiple interfaces)

Example:

```
interface A {
    void show();
}

interface B {
    void display();
}

class Demo implements A, B {

    public void show() {
        System.out.println("Show");
    }

    public void display() {
        System.out.println("Display");
    }
}
```

---

### 6. Why is encapsulation important?

It protects data, prevents invalid modifications, and allows controlled access through methods.

---

### 7. Why is polymorphism useful?

It allows the same method call to behave differently depending on the object, making code flexible and extensible.

---

### 8. What is the difference between an abstract class and an interface?

|Abstract Class|Interface|
|---|---|
|Can have abstract and concrete methods|Traditionally only abstract methods (Java 8+ allows default/static methods)|
|Can have constructors|Cannot have constructors|
|Uses `extends`|Uses `implements`|

---

# Quick Revision

```
OOPS
 │
 ├── Encapsulation
 │     ↓
 │   Data Hiding
 │   private + Getter/Setter
 │
 ├── Abstraction
 │     ↓
 │   Hide Implementation
 │   abstract / interface
 │
 ├── Inheritance
 │     ↓
 │   Code Reuse
 │   extends
 │
 └── Polymorphism
       ↓
   One Interface, Many Forms
   Overloading + Overriding
```