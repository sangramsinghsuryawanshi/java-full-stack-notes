## 1. What is a Variable?

A **variable** is a named memory location used to store a value that can change during program execution.

### Example

```
int age = 25;
```

Here:

- `int` → Data type
- `age` → Variable name
- `25` → Value
- `=` → Assignment operator

Think of a variable as a **container that stores data**.

```
Variable
   ↓
┌─────────────┐
│     25      │
└─────────────┘
    age
```

---

# 2. Why Do We Need Variables?

Variables allow us to:

- Store data
- Reuse data
- Modify data
- Perform calculations
- Pass data to methods

Example:

```
int price = 1000;
int quantity = 5;

int total = price * quantity;

System.out.println(total);
```

Output:

```
5000
```

---

# 3. Syntax of Variable

```
dataType variableName = value;
```

Example:

```
int age = 25;
```

Another example:

```
String name = "Rahul";
```

---

# 4. Declaration

Declaration means creating a variable by specifying its type and name.

```
int age;
```

At this point, no value has been assigned to the variable.

---

# 5. Initialization

Initialization means assigning a value to a variable for the first time.

```
age = 25;
```

---

# 6. Declaration + Initiali	zation

Both can be done together:

```
int age = 25;
```

---

# 7. Assignment

Changing the value of an existing variable is called assignment.

```
int age = 25;

age = 30;
```

Now:

```
age = 30
```

The old value `25` is replaced.

---

# 8. Types of Variables in Java

Java has **three types of variables based on where they are declared**:

```
Variables
   │
   ├── Local Variable
   │
   ├── Instance Variable
   │
   └── Static Variable
```

---

# 9. Local Variable

A variable declared **inside a method, constructor, or block** is called a local variable.

Example:

```
class Student {

    void display() {

        int age = 20;

        System.out.println(age);
    }
}
```

Here:

```
int age = 20;
```

is a local variable.

### Important

A local variable:

- Exists within its method/block
- Cannot be accessed outside its scope
- Does not get a default value automatically
- Must be initialized before use

Example:

```
void display() {

    int age;

    System.out.println(age); // Compile-time error
}
```

You must initialize it:

```
int age = 20;
```

---

# 10. Instance Variable

A variable declared **inside a class but outside methods** and without `static` is called an instance variable.

Example:

```
class Student {

    int age;
    String name;

    void display() {

        System.out.println(age);
        System.out.println(name);
    }
}
```

Here:

```
int age;
String name;
```

are instance variables.

Each object gets its **own copy** of instance variables.

Example:

```
Student s1 = new Student();
Student s2 = new Student();

s1.age = 20;
s2.age = 25;
```

```
s1 → age = 20

s2 → age = 25
```

---

# 11. Static Variable

A variable declared using the `static` keyword is called a static variable.

Example:

```
class Student {

    static String college = "ABC College";

}
```

A static variable belongs to the **class**, rather than to each individual object.

Example:

```
Student.college
```

---

# Instance vs Static

|Instance Variable|Static Variable|
|---|---|
|Belongs to object|Belongs to class|
|Each object gets its own copy|Shared among objects|
|Declared without `static`|Declared with `static`|
|Access through object generally|Can access using class name|

Example:

```
class Student {

    int age;                    // Instance
    static String college;      // Static

}
```

---

# 12. Local vs Instance vs Static

|Feature|Local|Instance|Static|
|---|---|---|---|
|Declared|Inside method/block|Inside class|Inside class|
|`static`|No|No|Yes|
|Belongs to|Method/block|Object|Class|
|Default value|No|Yes|Yes|
|Scope|Method/block|Object/class context|Class|

---

# 13. Primitive Variables

Variables can store primitive data types.

```
byte b = 10;

short s = 100;

int age = 25;

long population = 100000L;

float salary = 50000.5f;

double price = 999.99;

char grade = 'A';

boolean isActive = true;
```

---

# 14. Reference Variables

A reference variable stores a **reference to an object**, rather than the object data itself.

Example:

```
Student s = new Student();
```

Here:

```
Student
   ↓
Reference variable
   ↓
   s
   ↓
Student Object
```

Other examples:

```
String name = "Rahul";

int[] numbers = {10, 20, 30};
```

`name` and `numbers` are reference variables.

---

# 15. Variable Naming Rules

A Java variable name:

### Can contain

```
Letters
Digits
_
$
```

Example:

```
studentName
student_age
salary2026
$amount
```

### Cannot start with a digit

❌

```
int 1age = 20;
```

✅

```
int age1 = 20;
```

### Cannot contain spaces

❌

```
int student age = 20;
```

✅

```
int studentAge = 20;
```

### Cannot use Java keywords

❌

```
int class = 10;
```

---

# 16. Naming Convention

Java commonly follows **camelCase** for variable names.

Good:

```
studentName
employeeSalary
totalAmount
maximumMarks
```

Avoid:

```
student_name
StudentName
STUDENTNAME
```

For constants, uppercase with underscores is commonly used:

```
final double PI = 3.14159;
final int MAX_SIZE = 100;
```

---

# 17. Multiple Variables

You can declare multiple variables of the same type:

```
int a = 10;
int b = 20;
int c = 30;
```

Or:

```
int a = 10, b = 20, c = 30;
```

---

# 18. Changing Variable Value

Java variables can be reassigned.

```
int marks = 50;

marks = 75;

marks = 90;
```

Final value:

```
90
```

---

# 19. `final` Variable

A variable declared using `final` cannot be reassigned after initialization.

```
final int MAX = 100;
```

This is invalid:

```
MAX = 200;
```

Compile-time error.

### Example

```
final double PI = 3.14159;
```

`PI` cannot be changed.

---

# 20. Variable Scope

**Scope** means the region of the program where a variable can be accessed.

Example:

```
if (true) {

    int x = 20;

    System.out.println(x);
}

System.out.println(x); // Error
```

`x` exists only inside the `if` block.

---

## Scope Example

```
class
 │
 ├── Instance variable
 │
 └── method
      │
      ├── local variable
      │
      └── block
           │
           └── block variable
```

---

# 21. Variable Lifetime

**Lifetime** means how long a variable exists during program execution.

### Local variable

Exists while its method/block is executing.

### Instance variable

Exists as long as its object is reachable/alive.

### Static variable

Typically exists for the lifetime of the class's loaded runtime context.

---

# 22. Default Values

Instance and static variables receive default values.

|Data Type|Default Value|
|---|---|
|`byte`|`0`|
|`short`|`0`|
|`int`|`0`|
|`long`|`0L`|
|`float`|`0.0f`|
|`double`|`0.0d`|
|`char`|`'\u0000'`|
|`boolean`|`false`|
|Reference|`null`|

Example:

```
class Demo {

    int number;
    boolean status;
    String name;

    public static void main(String[] args) {

        Demo d = new Demo();

        System.out.println(d.number);
        System.out.println(d.status);
        System.out.println(d.name);
    }
}
```

Output:

```
0
false
null
```

---

# 23. Local Variables Don't Have Default Values

This is an important interview question.

```
public static void main(String[] args) {

    int age;

    System.out.println(age);
}
```

❌ Compile-time error.

You must initialize:

```
int age = 20;
```

---

# 24. Variable Shadowing

When a local variable has the same name as an instance variable, the local variable **shadows** the instance variable within that scope.

Example:

```
class Student {

    int age = 20;

    void display() {

        int age = 25;

        System.out.println(age);
    }
}
```

Output:

```
25
```

The local `age` takes precedence.

---

## Access Instance Variable Using `this`

```
class Student {

    int age = 20;

    void display() {

        int age = 25;

        System.out.println(age);

        System.out.println(this.age);
    }
}
```

Output:

```
25
20
```

`this.age` refers to the current object's instance variable.

---

# 25. Variable vs Constant

|Variable|Constant|
|---|---|
|Value can change|Value cannot change|
|Normal declaration|Uses `final`|
|Example `age`|Example `MAX_AGE`|

Example:

```
int age = 20;

age = 25;       // Valid
```

Constant:

```
final int MAX_AGE = 100;

MAX_AGE = 120;  // Error
```

---

# 26. Type of Variable Based on Storage

Another useful classification:

```
Variable
   │
   ├── Primitive
   │
   └── Reference
```

### Primitive

```
int age = 25;
```

Stores a primitive value.

### Reference

```
Student s = new Student();
```

Stores a reference to an object.

---

# 27. Example Program

```
class Student {

    // Instance variables
    int rollNo;
    String name;

    // Static variable
    static String college = "ABC College";

    void display() {

        // Local variable
        int marks = 85;

        System.out.println("Roll No: " + rollNo);
        System.out.println("Name: " + name);
        System.out.println("College: " + college);
        System.out.println("Marks: " + marks);
    }

    public static void main(String[] args) {

        Student s = new Student();

        s.rollNo = 101;
        s.name = "Rahul";

        s.display();
    }
}
```

Here:

```
rollNo       → Instance variable

name         → Instance variable

college      → Static variable

marks        → Local variable

s            → Reference variable
```

---

# 28. Important Interview Questions

### Q1. What is a variable?

A variable is a named storage location used to hold a value that can change during program execution.

---

### Q2. What are the types of variables in Java?

Three types based on declaration location:

1. Local variable
2. Instance variable
3. Static variable

---

### Q3. What is a local variable?

A variable declared inside a method, constructor, or block.

---

### Q4. What is an instance variable?

A non-static variable declared inside a class but outside methods, associated with an object.

---

### Q5. What is a static variable?

A variable declared with `static` that belongs to the class and is shared among its instances.

---

### Q6. Do local variables have default values?

❌ No.

They must be initialized before use.

---

### Q7. Do instance variables have default values?

✅ Yes.

---

### Q8. What is variable scope?

The region of the program where a variable can be accessed.

---

### Q9. What is variable shadowing?

When a variable declared in a narrower scope has the same name as a variable in an outer scope, it hides / shadows the outer variable.

---

### Q10. What is the purpose of `this`?

`this` refers to the **current object**.

It is commonly used to distinguish an instance variable from a local variable with the same name.

---

### Q11. Can a variable be changed after declaration?

Yes, unless it is declared `final`.

---

### Q12. What is a reference variable?

A variable that holds a reference to an object.

Example:

```
Student s = new Student();
```

---

### Q13. What is the difference between declaration and initialization?

```
int age;       // Declaration

age = 20;      // Initialization
```

Together:

```
int age = 20;
```

---

### Q14. What is the difference between `static` and instance variables?

`static` variables belong to the class and are shared, while instance variables belong to individual objects.

---

# Quick Revision

```
VARIABLE
   ↓
Named storage for data
   ↓
dataType name = value;
```

### Types

```
Local
   ↓
Inside method/block

Instance
   ↓
Inside class, non-static

Static
   ↓
Inside class + static
```

### Important Keywords

```
static → Class-level variable

final → Cannot reassign

this → Current object
```

### Remember

```
Local variable
→ No default value

Instance variable
→ Has default value

Static variable
→ Has default value

Reference variable
→ Refers to an object
```

### Interview Formula

**Variable = Data Type + Name + Value**

```
int age = 25;
```

**`int` → Type**

**`age` → Variable**

**`25` → Value**
