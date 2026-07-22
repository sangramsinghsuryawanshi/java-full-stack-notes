# Basic Interview Questions

### 1. What is searching?

**Answer:**  
Searching is the process of finding the location (index) of a specific element in a collection such as an array, list, or string.

---

### 2. What are the types of searching?

- Linear Search
- Binary Search
- Jump Search
- Interpolation Search
- Exponential Search

---

### 3. What is Linear Search?

Linear Search checks each element one by one until the target element is found.

---

### 4. When should you use Linear Search?

Use Linear Search when:

- Array is unsorted
- Dataset is small
- Frequent insertions/deletions
- Simplicity is preferred

---

### 5. What is the time complexity of Linear Search?

|Case|Complexity|
|---|---|
|Best|O(1)|
|Average|O(n)|
|Worst|O(n)|

---

### 6. What is the space complexity?

```
O(1)
```

---

### 7. Does Linear Search require a sorted array?

No.

It works on both sorted and unsorted arrays.

---

### 8. Why is Linear Search called Sequential Search?

Because elements are checked one after another sequentially.

---

### 9. What happens if the element is not found?

Usually, return:

```
-1
```

---

### 10. Can Linear Search find duplicate elements?

Yes.

But the normal implementation returns only the first occurrence.

---

# Coding Interview Questions

## 11. Search an element in an array

Example

```
Input

10 20 30 40

Search = 30

Output

2
```

---

## 12. Search an element in a String

```
"HELLO"

Find 'L'

Output

true
```

---

## 13. Search within a range

Search only from index 3 to index 8.

---

## 14. Find minimum element

```
18 2 45 1 10

Answer

1
```

---

## 15. Find maximum element

```
2 5 9 1

Answer

9
```

---

## 16. Search in 2D array

Return row and column index.

---

## 17. Find number having even digits

Example

```
12
345
6789

Answer

2
```

---

## 18. Richest Customer Wealth

LeetCode 1672

---

## 19. Count occurrences of an element

Example

```
1 2 2 2 3

Target

2

Answer

3
```

---

## 20. Find first occurrence

Example

```
2 5 8 5 9

Target

5

Answer

1
```

---

## 21. Find last occurrence

Example

```
2 5 8 5 9

Answer

3
```

---

## 22. Search negative number

Example

```
10 5 -3 7

Answer

2
```

---

## 23. Find smallest positive number

---

## 24. Find largest negative number

---

## 25. Find index of character in String

---

# Theory Interview Questions

## 26. Difference between Linear Search and Binary Search

|Linear|Binary|
|---|---|
|No sorting needed|Sorted array required|
|O(n)|O(log n)|
|Sequential|Divide and conquer|

---

## 27. Why is Binary Search faster?

Because it eliminates half of the search space after each comparison.

---

## 28. Can Binary Search work on an unsorted array?

No.

---

## 29. Why does Linear Search take O(n)?

Because in the worst case every element must be checked.

---

## 30. Which searching algorithm is easiest?

Linear Search.

---

# Arrays Interview Questions

## 31. Why do arrays start from index 0?

The first element is stored at the base address with an offset of 0, making address calculation efficient.

---

## 32. Can array size change?

No.

Arrays are fixed size.

---

## 33. Difference between Array and ArrayList

|Array|ArrayList|
|---|---|
|Fixed Size|Dynamic Size|
|Faster|Flexible|
|length|size()|

---

## 34. Default values of array

|Type|Default|
|---|---|
|int|0|
|double|0.0|
|boolean|false|
|char|'\u0000'|
|String|null|

---

## 35. What is ArrayIndexOutOfBoundsException?

Occurs when an invalid index is accessed.

Example

```
arr[10];
```

---

# Pattern Questions

## 36. Search first even number

---

## 37. Search last odd number

---

## 38. Find second largest

---

## 39. Find second smallest

---

## 40. Count positive numbers

---

## 41. Count negative numbers

---

## 42. Search multiple targets

---

## 43. Search duplicate elements

---

## 44. Search character frequency

---

## 45. Search word in String array

---

## 46. Find longest String

---

## 47. Find shortest String

---

## 48. Find richest employee

---

## 49. Search in jagged array

---

## 50. Search using enhanced for loop

---

# Frequently Asked Coding Problems

## Easy

- Linear Search
- Search in String
- Search in Range
- Find Minimum
- Find Maximum
- Search in 2D Array
- Richest Customer Wealth
- Even Digits
- Count Occurrences
- Reverse Array

---

## Medium

- Two Sum
- Move Zeroes
- Remove Duplicates
- Merge Sorted Arrays
- Rotate Array
- Product Except Self
- Majority Element
- Best Time to Buy and Sell Stock
- Container With Most Water
- 3Sum

---

## LeetCode Questions

|Problem|Difficulty|
|---|---|
|1672 Richest Customer Wealth|Easy|
|1295 Find Numbers with Even Digits|Easy|
|1 Two Sum|Easy|
|26 Remove Duplicates|Easy|
|27 Remove Element|Easy|
|88 Merge Sorted Array|Easy|
|66 Plus One|Easy|
|189 Rotate Array|Medium|
|283 Move Zeroes|Easy|
|169 Majority Element|Easy|

---

# HR + Technical Questions

### What is the difference between searching and sorting?

**Searching:** Finds an element.

**Sorting:** Arranges elements in ascending or descending order.

---

### Which search is better?

- Small/unsorted data → Linear Search
- Large/sorted data → Binary Search

---

### Why is Binary Search not always used?

Because it requires the array to be sorted. Sorting itself may take **O(n log n)** time.

---

### Can we search in a linked list using Binary Search?

Not efficiently, because linked lists do not support random access by index.

---

### Can Linear Search work with Strings?

Yes.

You can search characters using `charAt()` or `toCharArray()`.

---

### What is the best case for Linear Search?

The target element is found at the **first index**.

Time Complexity:

```
O(1)
```

---

### What is the worst case?

The target is at the last index or not present.

Time Complexity:

```
O(n)
```

---

# Interview Tips

When solving searching questions:

1. Check if the array is **sorted**.
2. If **unsorted**, think of **Linear Search**.
3. If **sorted**, consider **Binary Search**.
4. Clarify what to return:
    - Index?
    - Value?
    - Boolean?
    - Count?
5. Discuss **time complexity** and **space complexity** before or after coding.

---

## Most Repeated Interview Questions (★★★★★)

1. Linear Search
2. Binary Search
3. Search in String
4. Search in 2D Array
5. Find Minimum/Maximum
6. Count Occurrences
7. Two Sum
8. Remove Duplicates
9. Move Zeroes
10. Richest Customer Wealth
11. Find Numbers with Even Digits
12. First and Last Occurrence
13. Reverse Array
14. Rotate Array
15. Array vs ArrayList