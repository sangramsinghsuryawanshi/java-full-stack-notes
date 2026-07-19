# Arrays Interview Questions with Answers

## 1. What is an Array?

### Answer

An array is a collection of elements of the **same data type** stored in **contiguous memory locations**. It allows accessing elements using an index.

Example:

```
int[] arr = {10, 20, 30, 40};
```

---

## 2. Why do we use Arrays?

### Answer

Arrays are used to:

- Store multiple values of the same type.
- Access elements quickly using an index.
- Reduce the need to create multiple variables.

---

## 3. What are the advantages of Arrays?

### Answer

- Fast random access using indexes (`O(1)`).
- Easy to traverse.
- Memory efficient because elements are stored contiguously.
- Suitable for fixed-size collections.

---

## 4. What are the disadvantages of Arrays?

### Answer

- Fixed size.
- Cannot grow or shrink dynamically.
- Insertion and deletion are costly.
- Can store only one data type.

---

## 5. How do you declare an Array in Java?

### Answer

```
int[] arr;
```

or

```
int arr[];
```

---

## 6. How do you initialize an Array?

### Answer

Using values:

```
int[] arr = {10, 20, 30};
```

Using size:

```
int[] arr = new int[5];
```

---

## 7. What is the default value of an Array?

### Answer

|Data Type|Default Value|
|---|---|
|int|0|
|double|0.0|
|boolean|false|
|char|'\u0000'|
|Object|null|

---

## 8. Can an Array store different data types?

### Answer

No.

An array stores only one data type.

Example:

```
int[] arr = {1,2,3};
```

---

## 9. Can Array size be changed after creation?

### Answer

No.

Arrays have a fixed size. To change the size, create a new array or use `ArrayList`.

---

## 10. What is the index of the first element?

### Answer

The first element is at index **0**.

Example:

```
arr[0];
```

---

## 11. What happens if you access an invalid index?

### Answer

Java throws:

```
ArrayIndexOutOfBoundsException
```

---

## 12. How do you find the length of an Array?

### Answer

Using the `length` property.

```
arr.length
```

---

## 13. Difference between `length` and `length()`?

### Answer

|length|length()|
|---|---|
|Used for arrays|Used for String|
|Property|Method|

Example:

```
arr.length
```

```
str.length()
```

---

## 14. Difference between Array and ArrayList?

### Answer

|Array|ArrayList|
|---|---|
|Fixed size|Dynamic size|
|Faster|Slightly slower|
|Stores primitives and objects|Stores objects only|
|Part of Java language|Part of Collections Framework|

---

## 15. What is a One-Dimensional Array?

### Answer

Stores elements in a single row.

Example:

```
int[] arr = {1,2,3};
```

---

## 16. What is a Two-Dimensional Array?

### Answer

A matrix-like structure with rows and columns.

Example:

```
int[][] arr = {
    {1,2},
    {3,4}
};
```

---

## 17. What is a Jagged Array?

### Answer

A 2D array where each row can have a different number of columns.

Example:

```
int[][] arr = {
    {1,2},
    {3,4,5},
    {6}
};
```

---

## 18. How are Arrays stored in memory?

### Answer

- Elements are stored in **contiguous memory locations**.
- Each element is accessed using an index.

---

## 19. Why is Array access O(1)?

### Answer

Because Java calculates the memory address using:

```
Base Address + (Index × Size of Data Type)
```

So accessing any element takes constant time.

---

## 20. Can Arrays store Objects?

### Answer

Yes.

Example:

```
Student[] students = new Student[10];
```

---

## 21. Can Arrays be passed to methods?

### Answer

Yes.

Example:

```
void display(int[] arr) {
    // process array
}
```

---

## 22. Can a method return an Array?

### Answer

Yes.

Example:

```
int[] getNumbers() {
    return new int[]{1,2,3};
}
```

---

## 23. What is the difference between == and `Arrays.equals()`?

### Answer

- == compares references.
- `Arrays.equals()` compares element values.

Example:

```
int[] a = {1,2};
int[] b = {1,2};

System.out.println(a == b);              // false
System.out.println(Arrays.equals(a, b)); // true
```

---

## 24. How do you copy an Array?

### Answer

Using:

- `Arrays.copyOf()`
- `System.arraycopy()`
- `clone()`

---

## 25. What is the time complexity of common Array operations?

|Operation|Complexity|
|---|---|
|Access|O(1)|
|Search (Linear)|O(n)|
|Binary Search (Sorted)|O(log n)|
|Insertion (Middle)|O(n)|
|Deletion|O(n)|
|Traversal|O(n)|

---

# Frequently Asked Scenario-Based Questions

### Q1. Why is insertion in an Array costly?

**Answer:** Elements after the insertion point must be shifted one position to the right, making the time complexity **O(n)**.

---

### Q2. Why is deletion from an Array also O(n)?

**Answer:** After deleting an element, the remaining elements must be shifted left to fill the gap.

---

### Q3. When should you use an Array instead of an ArrayList?

**Answer:**

- When the size is fixed.
- When high performance is required.
- When storing primitive data types efficiently.

---

### Q4. When should you use an ArrayList instead of an Array?

**Answer:**

- When the collection size changes dynamically.
- When you need built-in methods like `add()`, `remove()`, and `contains()`.

---

### Q5. Why are Arrays faster than ArrayLists?

**Answer:**  
Arrays access elements directly from contiguous memory and don't have the overhead of resizing or additional collection management.

---

# ⭐ Top 10 Array Interview Questions (Most Repeated)

1. What is an Array?
2. Advantages and disadvantages of Arrays.
3. Difference between Array and ArrayList.
4. What are the default values of Arrays?
5. Difference between `length` and `length()`.
6. What is a Jagged Array?
7. Explain `ArrayIndexOutOfBoundsException`.
8. How are Arrays stored in memory?
9. Why is array access `O(1)`?
10. What is the time complexity of insertion, deletion, search, and access?