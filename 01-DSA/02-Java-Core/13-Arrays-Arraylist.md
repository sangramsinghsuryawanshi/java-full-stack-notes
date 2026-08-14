
## Definition
Array is a collection of elements stored in contiguous memory locations.

## Advantages
- Fast access
- Easy traversal

## Disadvantages
- Fixed size

## Time Complexity

| Operation | Complexity |
| --------- | ---------- |
| Access    | O(1)       |
| Search    | O(n)       |
| Insert    | O(n)       |
| Delete    | O(n)       |

# 1. Introduction to Arrays

## What is an Array?

An **array** is a collection of elements of the **same data type** stored in **contiguous (continuous) memory locations**.

Instead of creating multiple variables, we can store many values in a single array.

---

## Real-Life Example

Suppose you want to store marks of 5 students.

Without an array:

```
int mark1 = 80;
int mark2 = 75;
int mark3 = 90;
int mark4 = 65;
int mark5 = 88;
```

With an array:

```
int[] marks = {80, 75, 90, 65, 88};
```

---

## Advantages

- Stores multiple values using one variable.
- Easy to access using an index.
- Reduces code duplication.
- Easy to process using loops.
- Better memory management.

---

# 2. Why Do We Need Arrays?

Without arrays:

```
int roll1 = 101;
int roll2 = 102;
int roll3 = 103;
int roll4 = 104;
int roll5 = 105;
```

Problems:

- Too many variables.
- Difficult to manage.
- Hard to iterate.

Using an array:

```
int[] roll = {101, 102, 103, 104, 105};
```

One variable stores all values.

---

# 3. Syntax of an Array

## Declaration

```
dataType[] arrayName;
```

Example:

```
int[] numbers;
```

---

## Declaration + Creation

```
int[] numbers = new int[5];
```

---

## Declaration + Initialization

```
int[] numbers = {10, 20, 30, 40, 50};
```

---

# 4. Program: Store 5 Roll Numbers

```
public class Main {    
	public static void main(String[] args) {        
		int[] roll = new int[5];        
		roll[0] = 101;        
		roll[1] = 102;        
		roll[2] = 103;        
		roll[3] = 104;        
		roll[4] = 105;        
		for (int i = 0; i < roll.length; i++) {           
			 System.out.println(roll[i]);        
		}    
	}
}
```

Output:

```
101102103104105
```

---

# 5. How Does an Array Work?

When we write:

```
int[] arr = new int[5];
```

Memory is allocated in the **heap**.

```
Index      0    1    2    3    4           -----------------------Value      0    0    0    0    0
```

---

# 6. Internal Working of an Array

```
int[] arr = new int[5];
```

Steps:

1. `new` allocates memory in the heap.
2. Five integer locations are created.
3. Default values are assigned (`0` for int).
4. The reference variable `arr` stores the address of the array.

---

# 7. Dynamic Memory Allocation

## What is Dynamic Memory Allocation?

Memory is allocated **at runtime** using the `new` keyword.

Example:

```
int[] arr = new int[10];
```

Memory is not allocated during compilation.

It is allocated when the program runs.

---

# 8. Internal Representation of an Array

```
int[] arr = {10, 20, 30};
```

Memory:

```
Reference Variable       │       ▼Address 1000Heap Memory1000 → 101004 → 201008 → 30
```

`arr` stores only the reference (address), not the actual data.

---

# 9. Continuity of an Array

Array elements are stored in **continuous memory locations**.

```
Index      0     1     2     3Address   100   104   108   112Value      10    20    30    40
```

Benefits:

- Fast access.
- Efficient iteration.

---

# 10. Index of an Array

Arrays are **zero-indexed**.

```
int[] arr = {10, 20, 30};
```

```
Index   0    1    2Value  10   20   30
```

Access:

```
System.out.println(arr[0]); // 10System.out.println(arr[2]); // 30
```

---

# Array Index Out of Bounds

```
System.out.println(arr[5]);
```

Output:

```
ArrayIndexOutOfBoundsException
```

---

# 11. String Array

```
String[] names = {    "Rahul",    "Priya",    "Amit"};
```

Output:

```
System.out.println(names[1]);
```

```
Priya
```

---

# 12. What is null in Java?

`null` means a reference variable is **not pointing to any object**.

Example:

```
String name = null;
```

```
name ↓null
```

No object exists.

---

# 13. null as Default Value

Reference arrays get `null` by default.

```
String[] names = new String[3];
```

Memory:

```
Index   0      1      2Value  null   null   null
```

Primitive arrays receive default values:

|Data Type|Default Value|
|---|---|
|int|0|
|double|0.0|
|boolean|false|
|char|'\u0000'|
|String|null|

---

# 14. Array Input

```
import java.util.Scanner;Scanner sc = new Scanner(System.in);int[] arr = new int[5];for(int i = 0; i < arr.length; i++){    arr[i] = sc.nextInt();}
```

---

# Array Output

```
for(int i = 0; i < arr.length; i++){    System.out.println(arr[i]);}
```

---

# 15. For-Each Loop

## Definition

The enhanced `for` loop is used to traverse arrays without using an index.

Syntax:

```
for(dataType variable : array){}
```

Example:

```
int[] arr = {10,20,30};for(int num : arr){    System.out.println(num);}
```

Output:

```
102030
```

---

# Difference Between for and for-each

|for Loop|for-each Loop|
|---|---|
|Uses index|No index|
|Can modify elements|Read-only traversal|
|Flexible|Simpler|

---

# 16. toString() Method

Printing an array directly:

```
System.out.println(arr);
```

Output:

```
[I@15db9742
```

To print contents:

```
import java.util.Arrays;System.out.println(Arrays.toString(arr));
```

Output:

```
[10, 20, 30]
```

---

# 17. Array of Objects

```
String[] students = new String[3];
```

Each element stores a **reference**.

```
students↓Address↓"Rahul""Priya""Amit"
```

---

# 18. Storage of Objects in Heap

```
String[] names = new String[2];names[0] = "Java";names[1] = "Python";
```

Memory:

```
Stacknames ↓2000Heap2000 → References ↓        ↓"Java"  "Python"
```

---

# 19. Passing Array to a Method

```
static void printArray(int[] arr){    for(int num : arr){        System.out.println(num);    }}
```

Calling:

```
int[] arr = {10,20,30};printArray(arr);
```

---

# Arrays are Passed by Reference Value

Changes inside the method affect the original array.

Example:

```
static void change(int[] arr){    arr[0] = 100;}
```

Calling:

```
int[] arr = {10,20};change(arr);System.out.println(arr[0]);
```

Output:

```
100
```

---

# 20. Multidimensional Arrays

A multidimensional array is an array containing other arrays.

Most common:

**2D Array**

---

# 21. Syntax of a 2D Array

Declaration:

```
int[][] matrix;
```

Creation:

```
int[][] matrix = new int[3][3];
```

Initialization:

```
int[][] matrix = {    {1,2,3},    {4,5,6},    {7,8,9}};
```

---

# 22. Internal Working of a 2D Array

A 2D array is an **array of arrays**.

```
matrix↓Row 0 → [1 2 3]↓Row 1 → [4 5 6]↓Row 2 → [7 8 9]
```

Each row is a separate array.

---

# 23. Input for 2D Array

```
Scanner sc = new Scanner(System.in);int[][] arr = new int[3][3];for(int i = 0; i < arr.length; i++){    for(int j = 0; j < arr[i].length; j++){        arr[i][j] = sc.nextInt();    }}
```

---

# 24. Output for 2D Array

Using nested loops:

```
for(int i = 0; i < arr.length; i++){    for(int j = 0; j < arr[i].length; j++){        System.out.print(arr[i][j] + " ");    }    System.out.println();}
```

Using for-each:

```
for(int[] row : arr){    for(int value : row){        System.out.print(value + " ");    }    System.out.println();}
```

---

# 25. Dynamic Arrays (ArrayList)

## Why ArrayList?

Arrays have a fixed size.

```
int[] arr = new int[5];
```

Cannot grow.

`ArrayList` grows dynamically.

---

## Syntax

```
import java.util.ArrayList;ArrayList<Integer> list = new ArrayList<>();
```

---

## Adding Elements

```
list.add(10);list.add(20);list.add(30);
```

---

## Access

```
System.out.println(list.get(0));
```

---

## Update

```
list.set(1,100);
```

---

## Remove

```
list.remove(0);
```

---

## Size

```
System.out.println(list.size());
```

---

# 26. Internal Working of ArrayList

Initially:

```
Capacity = 10
```

If full:

```
Old Capacity = 10↓New Capacity = 15
```

A larger array is created and elements are copied.

---

# Array vs ArrayList

|Array|ArrayList|
|---|---|
|Fixed Size|Dynamic Size|
|Faster|Slightly Slower|
|Stores primitives & objects|Stores objects (wrapper classes for primitives)|
|Uses `length`|Uses `size()`|

---

# 27. Common Array Functions

## length

```
arr.length
```

---

## Arrays.toString()

```
Arrays.toString(arr)
```

---

## Arrays.sort()

```
Arrays.sort(arr);
```

---

## Arrays.fill()

```
Arrays.fill(arr,5);
```

---

## Arrays.equals()

```
Arrays.equals(arr1,arr2);
```

---

# Frequently Asked Interview Questions

### 1. What is an Array?

A collection of elements of the same data type stored in contiguous memory locations.

---

### 2. Why are arrays zero-indexed?

Because the first element is stored at the base address (offset `0`), making address calculation simple and efficient.

---

### 3. What is the default value of an int array?

```
0
```

---

### 4. What is the default value of a String array?

```
null
```

---

### 5. Difference Between Array and ArrayList?

|Array|ArrayList|
|---|---|
|Fixed size|Dynamic size|
|`length`|`size()`|
|Faster|Flexible|

---

### 6. What is `null`?

A reference that does not point to any object.

---

### 7. Can arrays store objects?

Yes. Arrays can store references to objects, such as `String`, custom classes, or wrapper classes.

---

### 8. What is a 2D array?

An array whose elements are themselves arrays (an array of arrays).

---

### 9. Can we pass arrays to methods?

Yes. Arrays are passed by reference value, so modifications to elements inside the method are reflected in the original array.

---

### 10. What is the difference between `for` and `for-each`?

|for|for-each|
|---|---|
|Uses index|No index|
|Can modify elements|Mainly for traversal|
|More control|Simpler syntax|

---

# Quick Revision

```
Array ↓Same Data TypeContinuous MemoryDeclaration ↓int[] arr;Creation ↓new int[5]Initialization ↓{10,20,30}Index ↓Starts From 0Default Values ↓int → 0boolean → falseString → nullFor-each ↓Easy Traversal2D Array ↓Array of ArraysArrayList ↓Dynamic SizeUseful Methods ↓lengthArrays.toString()Arrays.sort()Arrays.fill()Arrays.equals()
```

# Summary

- An array stores multiple elements of the same data type in contiguous memory.
- Arrays use zero-based indexing and have a fixed size.
- Primitive arrays receive default values such as `0` and `false`, while reference arrays receive `null`.
- Arrays can be traversed using `for` or enhanced `for-each` loops.
- Arrays are passed to methods by reference value.
- A 2D array is an array of arrays.
- `ArrayList` provides dynamic resizing and useful methods like `add()`, `get()`, `set()`, and `remove()`.
- Arrays and `ArrayList` are fundamental data structures and are among the most frequently tested topics in Java interviews and coding assessments.