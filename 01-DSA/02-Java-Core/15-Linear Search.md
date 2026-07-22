# 1. What is Searching?

## Definition

**Searching** is the process of finding the location (index) of a target element in a collection of data.

### Example

```
Array = [12, 45, 67, 89, 10]

Target = 67
```

Output

```
Element found at index 2
```

---

## Real-Life Examples

- Searching a contact in your phone
- Finding a student by roll number
- Searching a product on Amazon
- Searching a file in your computer

---

# Types of Searching

|Searching Technique|Requirement|Time Complexity|
|---|---|---|
|Linear Search|Array can be unsorted|O(n)|
|Binary Search|Array must be sorted|O(log n)|

---

# 2. Linear Search

## Definition

Linear Search checks every element one by one until the target element is found or the array ends.

---

## Algorithm

1. Start from index 0.
2. Compare current element with target.
3. If equal → return index.
4. Otherwise move to next element.
5. If end of array is reached → return -1.

---

## Flow Diagram

```
Start

↓

Compare arr[i]

↓

Match?

↓

Yes → Return Index

↓

No

↓

Next Element

↓

End?

↓

Yes → Return -1
```

---

## Java Program

```
public class Main {

    static int linearSearch(int[] arr, int target){

        for(int i = 0; i < arr.length; i++){

            if(arr[i] == target){
                return i;
            }

        }

        return -1;
    }

    public static void main(String[] args){

        int[] arr = {23,45,67,89,12};

        System.out.println(linearSearch(arr,67));

    }

}
```

Output

```
2
```

---

# Dry Run

```
Array

23 45 67 89 12

Target = 67
```

|Index|Value|Match|
|---|---|---|
|0|23|No|
|1|45|No|
|2|67|Yes|

Return

```
2
```

---

# Time Complexity

Best Case

```
O(1)
```

(Target found at first position)

Worst Case

```
O(n)
```

(Target at last position or absent)

Average Case

```
O(n)
```

Space Complexity

```
O(1)
```

---

# Advantages

- Simple to understand
- Works on sorted and unsorted arrays
- No preprocessing required

---

# Disadvantages

- Slow for large datasets
- Scans every element

---

# 3. Linear Search in String

## Problem

Find whether a character exists in a string.

Example

```
String = "SANGRAM"

Target = 'G'
```

---

## Java Program

```
public class Main {

    static boolean search(String str, char target){

        if(str.length() == 0){
            return false;
        }

        for(int i = 0; i < str.length(); i++){

            if(str.charAt(i) == target){
                return true;
            }

        }

        return false;
    }

    public static void main(String[] args){

        System.out.println(search("SANGRAM",'G'));

    }

}
```

Output

```
true
```

---

# Using For-Each Style

```
for(char ch : str.toCharArray()){

    if(ch == target){
        return true;
    }

}
```

---

# 4. Search in Range

## Problem

Search only within a given range.

Example

```
Array

18 12 -7 3 14 28

Search = 3

Range

Index 2 to 5
```

---

## Java Program

```
static int search(int[] arr, int target, int start, int end){

    for(int i = start; i <= end; i++){

        if(arr[i] == target){
            return i;
        }

    }

    return -1;
}
```

Calling

```
search(arr,3,2,5);
```

Output

```
3
```

---

# Dry Run

```
Array

18 12 -7 3 14 28

Search Starts

Index 2

↓

-7

↓

3

↓

Found
```

---

# 5. Minimum Number

## Problem

Find the smallest number in an array.

Example

```
Array

18 12 7 3 14 28
```

Output

```
3
```

---

## Java Program

```
static int min(int[] arr){

    int min = arr[0];

    for(int i = 1; i < arr.length; i++){

        if(arr[i] < min){
            min = arr[i];
        }

    }

    return min;
}
```

---

# Dry Run

```
18

↓

12

↓

7

↓

3

↓

Minimum
```

---

# Time Complexity

```
O(n)
```

---

# 6. Search in 2D Array

## Problem

Find an element inside a matrix.

Example

```
1 2 3

4 5 6

7 8 9

Target = 8
```

---

## Java Program

```
static int[] search(int[][] arr, int target){

    for(int row = 0; row < arr.length; row++){

        for(int col = 0; col < arr[row].length; col++){

            if(arr[row][col] == target){

                return new int[]{row,col};

            }

        }

    }

    return new int[]{-1,-1};
}
```

Calling

```
int[] ans = search(arr,8);
```

Output

```
Row = 2

Column = 1
```

---

# Dry Run

```
1 2 3

↓

4 5 6

↓

7 8 9

↓

Found at

(2,1)
```

---

# Time Complexity

```
Rows × Columns

O(m × n)
```

---

# 7. Even Number of Digits

## Problem

Find how many numbers contain an even number of digits.

Example

```
12

345

2

6

7896
```

Even digits

```
12

7896
```

Answer

```
2
```

---

## Logic

Count digits.

If

```
digits % 2 == 0
```

Increase count.

---

## Java Program

```
static int findNumbers(int[] nums){

    int count = 0;

    for(int num : nums){

        if(even(num)){
            count++;
        }

    }

    return count;
}

static boolean even(int num){

    return digits(num) % 2 == 0;
}

static int digits(int num){

    if(num < 0){
        num = -num;
    }

    if(num == 0){
        return 1;
    }

    int count = 0;

    while(num > 0){

        count++;

        num /= 10;

    }

    return count;
}
```

---

# Time Complexity

```
O(n × digits)
```

---

# Interview Tip

Alternative:

```
int digits = (int)Math.log10(num)+1;
```

Works only for positive numbers greater than zero.

---

# 8. Richest Customer Wealth

(LeetCode 1672)

## Problem

Each row represents one customer's bank accounts.

Find the customer with the maximum wealth.

Example

```
1 2 3

3 2 1
```

Customer 1

```
1+2+3 = 6
```

Customer 2

```
3+2+1 = 6
```

Output

```
6
```

---

## Java Program

```
static int maximumWealth(int[][] accounts){

    int max = Integer.MIN_VALUE;

    for(int[] customer : accounts){

        int sum = 0;

        for(int money : customer){

            sum += money;
        }

        if(sum > max){
            max = sum;
        }

    }

    return max;
}
```

---

# Time Complexity

```
Rows × Columns

O(m × n)
```

---

# Common Searching Problems

|Problem|Complexity|
|---|---|
|Linear Search|O(n)|
|Search in String|O(n)|
|Search in Range|O(end-start)|
|Minimum Element|O(n)|
|Search in 2D Array|O(m × n)|
|Even Digits|O(n × digits)|
|Richest Customer Wealth|O(m × n)|

---

# Frequently Asked Interview Questions

### 1. What is Linear Search?

Linear Search checks each element one by one until the target is found or the collection ends.

---

### 2. Does Linear Search require a sorted array?

No. It works on both sorted and unsorted arrays.

---

### 3. What is the worst-case time complexity?

```
O(n)
```

---

### 4. What is the space complexity?

```
O(1)
```

---

### 5. What does Linear Search return if the element is not found?

Typically:

```
-1
```

---

### 6. How do you search in a String?

By iterating through each character using `charAt()` or `toCharArray()`.

---

### 7. How do you search in a 2D array?

Use nested loops to check every row and column.

---

### 8. How do you find the minimum element in an array?

Initialize `min` with the first element and update it whenever a smaller value is found.

---

### 9. How do you count numbers with an even number of digits?

Count the digits of each number and check if the digit count is even.

---

### 10. What is the Richest Customer Wealth problem?

Find the maximum sum among all rows in a 2D array representing customers' bank accounts.

---

# Quick Revision

```
Searching
   ↓
Find Target Element

Linear Search
   ↓
Check Every Element

Time
   ↓
Best: O(1)
Average: O(n)
Worst: O(n)

Space
   ↓
O(1)

Applications
   ↓
Array
String
2D Array

Common Problems
   ↓
Search in String
Search in Range
Minimum Element
Search in 2D Array
Even Digits
Richest Customer Wealth
```

# Summary

- Searching is the process of locating a target element in a data structure.
- Linear Search works by checking elements one by one and does not require the array to be sorted.
- It has **O(n)** time complexity in the average and worst cases and **O(1)** space complexity.
- The same concept extends to strings, subarrays, and 2D arrays.
- Problems like **Minimum Number**, **Even Number of Digits**, and **Richest Customer Wealth** strengthen your understanding of iteration and searching.
- Linear Search is a foundational topic that is frequently tested in coding interviews and serves as a stepping stone to more advanced searching algorithms like Binary Search.