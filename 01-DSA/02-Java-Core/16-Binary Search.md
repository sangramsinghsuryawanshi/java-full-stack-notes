# 1. What is Binary Search?

## Definition

**Binary Search** is a searching algorithm that repeatedly divides a **sorted array** into two parts until the target element is found.

Instead of checking every element, Binary Search eliminates **half of the remaining elements** after each comparison.

---

## Prerequisite

✅ **The array must be sorted** (ascending or descending).

---

## Example

```
Array: [2, 5, 8, 12, 16, 23, 38, 56, 72]

Target = 23
```

Instead of checking all elements:

- Check middle element
- Decide whether to search left or right half
- Repeat until found

---

# 2. Why Binary Search?

Suppose an array has **1,000,000 elements**.

### Linear Search

Worst case:

```
Need to check all 1,000,000 elements
```

Time Complexity:

```
O(n)
```

---

### Binary Search

```
1000000

↓

500000

↓

250000

↓

125000

↓

...

↓

1
```

Only about **20 comparisons** are needed.

Time Complexity:

```
O(log₂ n)
```

---

# Linear Search vs Binary Search

|Linear Search|Binary Search|
|---|---|
|Works on sorted & unsorted arrays|Requires sorted array|
|O(n)|O(log n)|
|Sequential search|Divide and conquer|
|Slower|Much faster|

---

# 3. Binary Search Algorithm

Suppose:

```
Array = [2,5,8,12,16,23,38,56]

Target = 23
```

### Step 1

```
start = 0

end = 7
```

Find middle:

```
mid = start + (end - start) / 2;
```

```
mid = 3

Value = 12
```

Since

```
23 > 12
```

Move right.

```
start = mid + 1
```

---

### Step 2

```
start = 4

end = 7
```

Middle

```
mid = 5

Value = 23
```

Found.

Return

```
5
```

---

# Algorithm

```
start = 0

end = n-1

while(start <= end)

    mid

    if(target == arr[mid])

        return mid

    else if(target > arr[mid])

        search right

    else

        search left

return -1
```

---

# Flow Diagram

```
Start

↓

Find Mid

↓

Target == Mid ?

↓

Yes

↓

Return Index

↓

No

↓

Target > Mid ?

↓

Yes

↓

Search Right

↓

No

↓

Search Left
```

---

# 4. Why use `mid = start + (end - start) / 2`?

### Incorrect

```
int mid = (start + end) / 2;
```

Problem:

If `start` and `end` are very large integers:

```
start = 2,000,000,000

end = 2,100,000,000
```

Then:

```
start + end
```

may exceed Java's `int` limit (`2,147,483,647`), causing **integer overflow**.

---

### Correct

```
int mid = start + (end - start) / 2;
```

Reason:

- `end - start` is always within range.
- Avoids overflow.
- Produces the same result mathematically.

---

# 5. Binary Search Code

```
public class Main {

    static int binarySearch(int[] arr, int target){

        int start = 0;
        int end = arr.length - 1;

        while(start <= end){

            int mid = start + (end - start) / 2;

            if(target < arr[mid]){

                end = mid - 1;

            }
            else if(target > arr[mid]){

                start = mid + 1;

            }
            else{

                return mid;

            }
        }

        return -1;
    }

    public static void main(String[] args){

        int[] arr = {2,5,8,12,16,23,38,56};

        System.out.println(binarySearch(arr,23));

    }
}
```

Output

```
5
```

---

# Dry Run

```
Array

2 5 8 12 16 23 38 56

Target = 23
```

|Start|End|Mid|Value|Action|
|---|---|---|---|---|
|0|7|3|12|Search Right|
|4|7|5|23|Found|

---

# Time Complexity

|Case|Complexity|
|---|---|
|Best|O(1)|
|Average|O(log n)|
|Worst|O(log n)|

Space Complexity

```
O(1)
```

---

# 6. Order-Agnostic Binary Search

## Definition

Order-Agnostic Binary Search works for both:

- Ascending arrays
- Descending arrays

without needing separate algorithms.

---

## Example 1 (Ascending)

```
2 5 8 10 15 20
```

---

## Example 2 (Descending)

```
20 15 10 8 5 2
```

Same algorithm handles both.

---

# Idea

First determine the order.

```
boolean isAsc = arr[start] < arr[end];
```

If true:

Use ascending comparisons.

Otherwise:

Use descending comparisons.

---

# Order-Agnostic Binary Search Code

```
public class Main {

    static int orderAgnosticBS(int[] arr, int target){

        int start = 0;
        int end = arr.length - 1;

        boolean isAsc = arr[start] < arr[end];

        while(start <= end){

            int mid = start + (end - start) / 2;

            if(arr[mid] == target){
                return mid;
            }

            if(isAsc){

                if(target < arr[mid]){
                    end = mid - 1;
                }else{
                    start = mid + 1;
                }

            }else{

                if(target > arr[mid]){
                    end = mid - 1;
                }else{
                    start = mid + 1;
                }

            }
        }

        return -1;
    }

    public static void main(String[] args){

        int[] arr = {20,15,10,8,5,2};

        System.out.println(orderAgnosticBS(arr,10));

    }
}
```

Output

```
2
```

---

# Dry Run (Descending)

```
Array

20 15 10 8 5 2

Target = 10
```

|Start|End|Mid|Value|Action|
|---|---|---|---|---|
|0|5|2|10|Found|

---

# Binary Search Decision Tree

```
Is Array Sorted?

      │

      ▼

Ascending?

   │         │

 Yes        No

 │          │

Normal    Descending

Binary     Binary

Search     Search

      │

      ▼

Order Agnostic

Handles Both
```

---

# Common Mistakes

### 1. Using Binary Search on an Unsorted Array

❌ Wrong

Binary Search assumes the array is sorted.

---

### 2. Incorrect Loop Condition

❌

```
while(start < end)
```

May skip the last element.

✅

```
while(start <= end)
```

---

### 3. Integer Overflow

❌

```
int mid = (start + end) / 2;
```

✅

```
int mid = start + (end - start) / 2;
```

---

### 4. Wrong Update

Correct:

```
end = mid - 1;

start = mid + 1;
```

Not:

```
end = mid;

start = mid;
```

This can cause an infinite loop.

---

# Frequently Asked Interview Questions

### 1. What is Binary Search?

A searching algorithm that repeatedly divides a **sorted array** into halves(अर्धे) to find a target element.

---

### 2. What is the prerequisite(पूर्व-आवश्यकता) for Binary Search?

The array **must be sorted**.

---

### 3. What is the time complexity?

|Case|Complexity|
|---|---|
|Best|O(1)|
|Average|O(log n)|
|Worst|O(log n)|

---

### 4. What is the space complexity?

```
O(1)
```

---

### 5. Why is Binary Search faster than Linear Search?

Because it eliminates **half of the search space** after every comparison.

---

### 6. Why do we use `start + (end - start) / 2`?

To avoid integer overflow.

---

### 7. Can Binary Search work on an unsorted array?

❌ No.

---

### 8. What is Order-Agnostic Binary Search?

A Binary Search that automatically works on both ascending and descending sorted arrays.

---

### 9. What happens if the target is not found?

Return:

```
-1
```

---

### 10. Difference Between Linear Search and Binary Search

|Linear Search|Binary Search|
|---|---|
|O(n)|O(log n)|
|Works on unsorted arrays|Requires sorted arrays|
|Sequential|Divide and conquer|

---

# Most Asked Binary Search Problems (LeetCode)

|Problem|Difficulty|
|---|---|
|704. Binary Search|Easy|
|35. Search Insert Position|Easy|
|34. Find First and Last Position of Element|Medium|
|33. Search in Rotated Sorted Array|Medium|
|852. Peak Index in a Mountain Array|Easy|
|744. Find Smallest Letter Greater Than Target|Easy|
|69. Sqrt(x)|Easy|
|278. First Bad Version|Easy|
|374. Guess Number Higher or Lower|Easy|
|153. Find Minimum in Rotated Sorted Array|Medium|

---

# Quick Revision

```
Binary Search
      ↓
Sorted Array Required

Find Mid
      ↓
mid = start + (end - start) / 2

Target == Mid
      ↓
Found

Target < Mid
      ↓
Search Left

Target > Mid
      ↓
Search Right

Time
      ↓
O(log n)

Space
      ↓
O(1)

Order-Agnostic
      ↓
Works for
Ascending + Descending Arrays
```

# Summary

- Binary Search is an efficient searching algorithm for **sorted arrays**.
- It follows the **divide-and-conquer** approach by halving the search space at each step.
- Always calculate the middle index using `start + (end - start) / 2` to avoid integer overflow.
- Order-Agnostic Binary Search extends the algorithm to work on both ascending and descending sorted arrays.
- Binary Search is one of the most frequently tested topics in coding interviews and is the foundation for many advanced search problems such as rotated arrays, peak elements, and search insert position.