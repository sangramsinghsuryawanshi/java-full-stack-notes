### 1. What is Two Pointer Technique?

Two Pointer is an algorithmic technique where two indices (pointers) move through an array or string to solve problems efficiently.

Instead of using nested loops (O(n²)), many problems can be solved in O(n).

### Why use it?

Interview Favorite

- Reduces time complexity
    
- Avoids extra loops
    
- Uses constant extra space
    
- Common in array and string problems
    

### 2. Basic Idea

Use:

- left pointer
    
- right pointer
    

Move them according to conditions.

L

0

1

2

3

4

R

left = 0, right = n-1

### 3. Types of Two Pointer Problems

|Type|Example|
|---|---|
|Opposite Direction|Pair Sum in Sorted Array|
|Same Direction|Remove Duplicates|
|Sliding Window|Longest Substring|
|Fast & Slow Pointer|Linked List Cycle|

### 4. Opposite Direction Example (Pair Sum)

### Problem

Find if two numbers add up to target.

### Array

[1, 2, 3, 4, 6, 8, 9]

Target = 10

### Java Code

```
public class Main {

    static boolean pairSum(int[] arr, int target) {

        int left = 0;
        int right = arr.length - 1;

        while (left < right) {

            int sum = arr[left] + arr[right];

            if (sum == target) {
                return true;
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }

        return false;
    }

    public static void main(String[] args) {
        int[] arr = {1,2,3,4,6,8,9};
        System.out.println(pairSum(arr, 10));
    }
}
```

### Output

true

### Dry Run

|Left|Right|Sum|
|---|---|---|
|1|9|10 ✅|

### Time Complexity

Time

O(n)

Space

O(1)

### 5. Same Direction Example (Remove Duplicates)

### Problem

Remove duplicates from a sorted array.

### Input

[1,1,2,2,3,4,4]

### Java Code

```
public class Main {

    static int removeDuplicates(int[] nums) {

        int i = 0;

        for (int j = 1; j < nums.length; j++) {

            if (nums[i] != nums[j]) {
                i++;
                nums[i] = nums[j];
            }
        }

        return i + 1;
    }

    public static void main(String[] args) {
        int[] arr = {1,1,2,2,3,4,4};
        int k = removeDuplicates(arr);
        System.out.println(k);
    }
}
```

### Output

4

### Unique Elements

[1,2,3,4]

### 6. Reverse an Array

### Java Code

```
public class Main {

    static void reverse(int[] arr) {

        int left = 0;
        int right = arr.length - 1;

        while (left < right) {

            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;

            left++;
            right--;
        }
    }

    public static void main(String[] args) {
        int[] arr = {1,2,3,4,5};
        reverse(arr);

        for (int n : arr) {
            System.out.print(n + " ");
        }
    }
}
```

### Output

5 4 3 2 1

### 7. Valid Palindrome (String)

### Java Code

```
public class Main {

    static boolean isPalindrome(String s) {

        int left = 0;
        int right = s.length() - 1;

        while (left < right) {

            if (s.charAt(left) != s.charAt(right)) {
                return false;
            }

            left++;
            right--;
        }

        return true;
    }

    public static void main(String[] args) {
        System.out.println(isPalindrome("madam"));
    }
}
```

### Output

true

### 8. Fast & Slow Pointer

### Used In

- Linked List Cycle Detection
    
- Middle of Linked List
    
- Happy Number

### Idea

- slow moves 1 step
    
- fast moves 2 steps

1

2

3

4

5

slow

fast

### 9. Common Interview Problems

|Problem|Pointers|
|---|---|
|Two Sum (sorted)|Left & Right|
|Reverse Array|Left & Right|
|Palindrome|Left & Right|
|Remove Duplicates|i & j|
|Move Zeros|i & j|
|Container With Most Water|Left & Right|
|Linked List Cycle|Fast & Slow|

### 10. Move Zeros to End

```
public class Main {

    static void moveZeros(int[] arr) {

        int j = 0;

        for (int i = 0; i < arr.length; i++) {

            if (arr[i] != 0) {
                int temp = arr[i];
                arr[i] = arr[j];
                arr[j] = temp;
                j++;
            }
        }
    }

    public static void main(String[] args) {
        int[] arr = {0,1,0,3,12};
        moveZeros(arr);

        for (int n : arr) {
            System.out.print(n + " ");
        }
    }
}
```

### Output

1 3 12 0 0

### 11. Time Complexity Advantage

|Approach|Complexity|
|---|---|
|Nested Loops|O(n²)|
|Two Pointers|O(n)|

### 12. Interview Tips

### Remember These Patterns

High Value

Sorted array + target sum → Two Pointers

Reverse array/string → Left & Right

Remove duplicates → Slow & Fast Index

Linked list cycle → Fast & Slow Pointer

Sliding window problems → Two moving pointers

### Quick Revision

Two Pointers

O(n)

Left & Right

Reverse / Palindrome

i & j

Duplicates / Zeros

Fast & Slow

Linked List Cycle

Space Complexity

O(1)

### Most Asked Interview Question

### Q: Why is Two Pointer better than nested loops?

Answer:

Because it reduces time complexity from O(n²) to O(n) by processing the array in a single pass while using only O(1) extra space.

### Most Important Problems to Practice

- Two Sum (sorted array)
    
- Remove Duplicates
    
- Move Zeroes
    
- Reverse Array
    
- Valid Palindrome
    
- Container With Most Water
    
- 3Sum
    
- Linked List Cycle
    

If you master these 8 problems, you will understand almost all beginner-to-intermediate Two Pointer interview questions in Java.

This topic is extremely important for DSA rounds, coding interviews, and LeetCode problems.