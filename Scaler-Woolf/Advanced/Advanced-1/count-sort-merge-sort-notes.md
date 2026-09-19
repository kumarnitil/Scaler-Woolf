# Sorting: Count Sort & Merge Sort

## Contents
1. Count Sort — Smallest number from digits
2. Merge Two Sorted Arrays
3. Merge Sort

---

## DSA Contest 1 — Arrays, Bit Manipulation, Recursion, Math & Hashing
*(11th Sept 2026)*

- Contest held after every module.
- **Timing:** 90 minutes (7:00 – 8:30) + Contest discussion (8:30 – 9:30 AM)
- **Questions:** 4 total → 1 Easy, 2 Medium, 1 Difficult

**Rules for allocating rank:**
- For every failed submission, marks will be deducted.

**Clearing the contest:** requires 80% marks. If not cleared:
- Reattempt 1 → within 2 days
- Reattempt 2 → within 7 days
- Reattempt 3 → within 45 days

---

## Count Sort

**Problem:** Rearrange the given digits and find the smallest number that can be formed.

> Note: this is an array containing all the digits.

```
A[] = {1, 3, 5, 2, 3}
Ans[] = {1, 2, 3, 3, 5}

A[] = {3, 4, 0, 1, 1, 4, 9}
Ans[] = {0, 1, 1, 3, 4, 4, 9}
```

### Approach 1 — Built-in sort
```
Arrays.sort(A)
```
- **TC:** O(N log N)

### Approach 2 — Count Sort

All the elements lie between `0` and `9`.

```
[ 0 0 0 ... 0 ] [ 1 1 1 ... 1 ] [ 2 2 2 ... 2 ] ... [ 9 9 9 ... 9 ]
   freq of 0        freq of 1       freq of 2            freq of 9
```

- Size of frequency array = 10
- `freq[9]` = count of the digit `9` in the array

### Worked example

```
A[] = {1, 3, 8, 3, 2, 6, 5, 3, 8}

freq[10] = [0, 1, 1, 3, 0, 1, 1, 0, 2, 0]
  index:     0  1  2  3  4  5  6  7  8  9

Output = {1, 2, 3, 3, 3, 5, 6, 8, 8}
```

### Code

```java
int[] freq = new int[10];
for (int ele : A) {
    freq[ele]++;                 // TC: O(N)
}

for (int i = 0; i < 10; i++) {
    int ele = i;
    int cnt = freq[i];
    for (int j = 1; j <= cnt; j++) {
        print(ele);
    }
}
```
- **TC:** O(N)
- **SC:** O(1) — the freq array size is fixed at 10, independent of N

---

### Variation 1 — Elements not restricted to single digits (can be > 10)

```
A[] = {10, 13, 7, 4, 23, 49}
```

```java
int maxi = -1;
for (int ele : A) maxi = Math.max(maxi, ele);

int[] freq = new int[maxi + 1];
for (int ele : A) {
    freq[ele]++;
}

for (int i = 0; i < 10; i++) {      // (iterate up to maxi in practice)
    int ele = i;
    int cnt = freq[i];
    for (int j = 1; j <= cnt; j++) {
        print(ele);
    }
}
```
- **TC:** O(N)
- **SC:** O(maxi)

### Issues with Count Sort

**1. Range of elements is too large (> 10⁶)**

If `1 ≤ A[i] ≤ 10⁹`, it's not feasible to create a frequency array of that size — practically, arrays are only feasible in the `10⁵ – 10⁶` size range.

**2. Sparse large values**

```
A[] = {3, 100}
```
Just to store the frequency of two elements, you'd have to create an array of size 100 — very wasteful.

> **Note:** Count sort works best for a smaller range of values.

---

### Variation 2 — Count Sort on negative elements

```
A[] = {-3, 2, 2, 1, -2, 5}
Output = {-3, -2, 1, 2, 2, 5}
```

**Idea:** To bring the most negative element to index `0`, shift every element on the right side by the most negative element's absolute value.

```
Range = maximum - minimum + 1
      = 5 - (-3) + 1
      = 9
```

```
A[] =  {-3   2    2    1   -2    5}
Shift: +3   +3   +3   +3   +3   +3
idx:    0    5    5    4    1    8

freq[] index:  -3  -2  -1   0   1   2   3   4   5
freq[] value:   1   1   0   0   1   2   0   0   1
```

**To map element → index:** `A[i] + abs(mini)`

### Code

```java
int mini = Integer.MAX_VALUE;
int maxi = Integer.MIN_VALUE;
for (int ele : A) {
    mini = Math.min(mini, ele);
    maxi = Math.max(maxi, ele);
}

int[] freq = new int[maxi - mini + 1];
for (int ele : A) {
    int id = ele + Math.abs(mini);
    freq[id]++;
}

for (int i = mini; i <= maxi; i++) {
    int cnt = freq[i + Math.abs(mini)];
    for (int j = 1; j <= cnt; j++) {
        print(i);
    }
}
```
- **TC:** O(N)
- **SC:** O(range)

---

## Merge Two Sorted Arrays

### Scenario: Gmail's "All Inboxes" Feature

Google's Gmail offers an "All Inboxes" feature that allows users to view emails from multiple email accounts in one seamless interface — useful for users managing personal and professional communications through separate accounts. Emails from all accounts are merged into a single feed sorted by date and time.

**Problem:** Develop a function to emulate the "All Inboxes" feature.
- You're given two sorted arrays representing timestamps of emails from two different email accounts.
- Merge them into a single list, sorted by timestamp, in chronological order.

```
Account A Email Times: [1, 5, 6, 9]
Account B Email Times: [2, 4, 8]
Output: [1, 2, 4, 5, 6, 8, 9]
```

### Trace example

```
A[] = {1, 5, 6, 9, 10, 11}
B[] = {2, 4, 8}

Ans[] = {1, 2, 4, 5, 6, 8, 9, 10, 11}
```

### Code

```java
int[] merge(int[] A, int[] B) {
    int[] ans = new int[N + M];
    int i = 0, j = 0, k = 0;

    while (i < N && j < M) {
        if (A[i] < B[j]) {
            ans[k] = A[i];
            i++;
            k++;
        } else {
            ans[k] = B[j];
            j++;
            k++;
        }
    }

    while (i < N) {
        ans[k] = A[i];
        i++; k++;
    }

    while (j < M) {
        ans[k] = B[j];
        j++; k++;
    }

    return ans;
}
```
- **TC:** O(N + M)
- **SC:** O(1) extra (excluding the output array)

---

### Merging within a single array (in-place segments)

**Problem:** Given `A[N]` elements and 3 indices `s`, `m`, `e`, where:
- subarray `[s, m]` is sorted
- subarray `[m+1, e]` is sorted

Sort the entire `A[]` from `s` to `e`.

### Trace example

```
A[] = {-1, 2, 6, 9, 11, 3, 4, 7, 13, 10}
Indices:  0  1  2  3  4    5  6  7  8   9

s = 0, m = 4, e = 7

Left half  [s..m]  = {-1, 2, 6, 9, 11}
Right half [m+1..e] = {3, 4, 7}

ans[] = {-1, 2, 3, 4, 6, 7, 9, 11}
```

Once the `ans[]` array is built, push it back into `A[]` from indices `s` to `e`.

### Code

```java
void merge(int[] A, int s, int m, int e) {
    int[] ans = new int[e - s + 1];
    int i = s, j = m + 1, k = 0;

    while (i <= m && j <= e) {
        if (A[i] < A[j]) {
            ans[k] = A[i];
            i++;
            k++;
        } else {
            ans[k] = A[j];
            j++; k++;
        }
    }

    while (i <= m) {
        ans[k] = A[i];
        i++; k++;
    }

    while (j <= e) {
        ans[k] = A[j];
        j++; k++;
    }

    for (int idx = 0; idx < ans.length; idx++) {
        A[idx + s] = ans[idx];
    }
}
```
- **TC:** O(N)
- **SC:** O(N)

### Second trace example

```
s = 2, m = 4, e = 7

A[] = {-1, 3, [2, 4, 6], [1, 3, 5]}
Indices:  0   1   2  3  4   5  6  7

ans[] = {1, 2, 3, 4, 5, 6}
```

---

## Merge Sort

**Problem:** Given `A[N]` elements, sort them without using any inbuilt function. (N → up to 100 for illustration)

### Comparing naive divide strategies (why split into 2, not 4)

| Split strategy | Time complexity | Iterations for N=100 |
|---|---|---|
| No split (single block) | O(N²) | (100)² = 10⁴ |
| Split into 2 halves, merge | (N/2)² + (N/2)² + N = N²/2 + N | 10000/2 + 100 = **5100** |
| Split into 4 quarters, merge | 4×(N/4)² + N/2×2 + N = N²/4 + 2N | (100)²/4 + 2×100 = **2700 iterations** |

*(In practice, recursively halving all the way down — the classic merge sort — gives the best O(N log N) result, as derived below.)*

### Idea for Merge Sort

Split your entire array into two equal halves recursively, until it reaches a point where splitting is no longer possible (a single element) — then start merging back.

```
A[] = {10, 3, 7, 6, 8, 2, 17}
                    ↓ split
         {10, 3, 7, 6}        {8, 2, 17}
              ↓                    ↓
       {10, 3}   {7, 6}      {8, 2}   {17}
          ↓          ↓          ↓
       {10} {3}   {7} {6}   {8} {2}
          ↓ merge     ↓         ↓ merge
        {3, 10}    {6, 7}    {2, 8}
              ↓                  ↓
         {3, 6, 7, 10}      {2, 8, 17}
                    ↓ merge
         {2, 3, 6, 7, 8, 10, 17}  ← Ans
```

### Code

```java
void mergeSort(int[] A, int s, int e) {
    if (s == e) return;
    int m = (s + e) / 2;
    mergeSort(A, s, m);
    mergeSort(A, m + 1, e);
    merge(A, s, m, e);
}
```
- **TC:** O(N log N)
- **SC:** O(N)

### Time complexity derivation

```
Total time taken by mergeSort = f(n)

f(n) = f(n/2) + f(n/2) + n
f(n) = 2*f(n/2) + n            ... (1st equation)
```

Expanding `f(n/2) = 2*f(n/4) + n/2`:

```
f(n) = 2*(2*f(n/4) + n/2) + n
     = 4*f(n/4) + n + n
     = 2² * f(n/4) + 2n         ... (2nd equation)
```

Expanding `f(n/4) = 2*f(n/8) + n/4`:

```
f(n) = 2² * (2*f(n/8) + n/4) + 2n
     = 2³ * f(n/8) + 3n         ... (3rd equation)
```

**After the k-th substitution:**

```
f(n) = 2^k * f(n / 2^k) + k*n        with f(1) = 1

n / 2^k = 1  ⟺  n = 2^k  ⟺  k = log(n)

f(n) = n * 1 + log(n) * n
f(n) = n + n·log(n)
```

**Time Complexity: O(N log N)**

---

## In-place Sorting Algorithm

> A sorting algorithm which does not take any extra space is an **in-place sorting** algorithm.

**Merge sort is NOT an in-place sorting algorithm** (it needs O(N) extra space for merging).

---

## Stable Sorting Algorithm

> If we have two data points which are equal (in the sort key), and after sorting we're able to maintain the same relative order they had before sorting, the algorithm is **stable**.

### Example

| Person | Marks |
|---|---|
| Ram | 60 |
| Yogi | 60 |
| Shyam | 100 |
| Nitil | 90 |

**Sort 1 (Stable):**
```
Ram
Yogi
Nitil
Shyam
```
Ram and Yogi (both 60) keep their original relative order → **Stable**

**Sort 2 (Unstable):**
```
Yogi
Ram
Nitil
Shyam
```
Yogi and Ram's relative order got swapped → **Unstable sorting algorithm**

> **Merge sort is not an in-place sorting algorithm, but it IS a stable sort algorithm.**

---

## Problem Statement — Sort by Color

Given an array with N objects colored red, white, or blue, represented by the integers:

- Red → 0
- White → 1
- Blue → 2

**Task:** Sort the array so that all the red objects come first, followed by white objects, and then blue objects.
