# Arrays 1

> "It always seems impossible until it's done." — Nelson Mandela

## Topics
- Max subarray sum
- Zero Queries I
- Zero Queries II
- Rain Water Trapping

## DSA Roadmap
- Arrays 1: One Dimensional
- Arrays 2: Two Dimensional
- Lab Session on Arrays
- Bit Manipulation
- Lab Session on Bit Manipulation
- Recursion
- Lab Session on Recursion
- Maths: Modular Arithmetic & GCD
- Hashing
- Lab Session on Hashing
- Sorting 1: Count Sort & Merge Sort
- Sorting 2: Quick Sort & Comparator Problems
- Contest 1: Arrays, Bit Manipulation, Recursion, Math & Hashing
- Searching 1: Binary Search on Array
- Searching 2: Binary Search on Answer
- Lab Session on Searching
- Classes, Objects & Linked List Introduction
- Linked List: Basic Problems
- Linked List: Sorting and Problems
- Linked List: Doubly Linked List & Detecting Loop
- Stacks
- Lab Session on Stacks
- Queues: Implementation & Problems
- Revision of DSA 1 & 2
- Contest 2: Sorting, Searching, Linked List, Stacks, Queues

## How to solve a problem
1. Start a 35-minute timer.
2. Read the statement, examples, inputs, and outputs carefully.
3. Think of a brute-force approach.
4. Use constraints to optimize if needed.
5. Dry run with pen and paper.
6. If not solved in 35 minutes, bookmark it for revision.
7. Ask what concept was missing and revise it.
8. Use hints from left to right.
9. Refer class notes or the video explanation.
10. Post doubts in the WA group and raise a TA request if needed.

---

## 1. Maximum Subarray Sum

Given an array, find the maximum sum among all subarrays.

### Examples
- `A = [-3, 2, 4, -1, 3, -4, 3]` → `8`
- `A = [-2, 3, 4, -1, 5, -10, 7]` → `11`
- `A = [-3, 4, 6, 8, -10, 2, 7]` → `18`
- `A = [4, 5, 2, 1, 6]` → `18`
- `A = [-4, -3, -6, -9, -2]` → `-2`

### Brute force (O(N^3))

Example trace for `A = [-3, 2, 4, -1, 3, -4, 3]`:

- Subarrays:
  - `[-3]` → sum = -3
  - `[-3, 2]` → sum = -1
  - `[-3, 2, 4]` → sum = 3
  - …
  - `[2, 4, -1, 3]` → sum = 8 (maximum)

```java
ans = -∞

for (i = 0; i < n; i++) {
    for (j = i; j < n; j++) {
        sum = 0;
        for (k = i; k <= j; k++) {
            sum += A[k];
        }
        if (sum > ans) ans = sum;
    }
}

return ans;
```

- Time: `O(N^3)`
- Space: `O(1)`

### Prefix sum (O(N^2))

Example idea:

- Prefix array for `A = [-3, 2, 4, -1, 3, -4, 3]`:
  - `pf = [-3, -1, 3, 2, 5, 1, 4]`
- Subarray sum from `i` to `j`:
  - If `i == 0`: `sum = pf[j]`
  - Else: `sum = pf[j] - pf[i - 1]`

```java
// prefix sum array pf[]
ans = -∞

for (i = 0; i < n; i++) {
    for (j = i; j < n; j++) {
        if (i == 0) {
            sum = pf[j];
        } else {
            sum = pf[j] - pf[i - 1];
        }
        if (sum > ans) ans = sum;
    }
}

return ans;
```

- Time: `O(N^2)`
- Space: `O(N)`

### Carry forward (O(N^2))

Example trace for `A = [-3, 2, 4, -1, 3, -4, 3]`:

- Start from each `i`, keep adding next elements:
  - `i = 0`: `[-3] = -3`, `[-3, 2] = -1`, `[-3, 2, 4] = 3`, …
  - `i = 1`: `[2] = 2`, `[2, 4] = 6`, `[2, 4, -1] = 5`, …
  - Track maximum among all these.

```java
ans = -∞

for (i = 0; i < N; i++) {
    sum = 0;
    for (j = i; j < N; j++) {
        sum += A[j];
        if (sum > ans) ans = sum;
    }
}

return ans;
```

- Time: `O(N^2)`
- Space: `O(1)`

### Kadane's algorithm (O(N))

For large `N` (e.g., `10^5`), use Kadane's algorithm.

Key idea:
- If running sum becomes negative, restart from current element.
- If all elements are negative, answer is the maximum element.

Example trace for `A = [-2, 3, 4, -1, 5, -10, 7]`:

- Running sum and max:
  - Start: `sum = 0`, `ans = -∞`
  - `-2`: `sum = -2` → `ans = -2` → `sum < 0` ⇒ reset `sum = 0`
  - `3`: `sum = 3` → `ans = 3`
  - `4`: `sum = 7` → `ans = 7`
  - `-1`: `sum = 6` → `ans = 7`
  - `5`: `sum = 11` → `ans = 11`
  - `-10`: `sum = 1` → `ans = 11`
  - `7`: `sum = 8` → `ans = 11`

Another example: `A = [-2, -3, -1, -4]`:

- `sum` becomes negative at each step, so `ans` ends up as `-1` (max element).

```java
ans = -∞
sum = 0

for (i = 0; i < N; i++) {
    sum += A[i];
    if (sum > ans) ans = sum;
    if (sum < 0) sum = 0;
}

return ans;
```

- Time: `O(N)`
- Space: `O(1)`

### Homework
Find the subarray with maximum sum (start and end indices).

---

## 2. Zero Queries I

Given an array of all zeros, perform queries of the form `(i, val)`:
- Add `val` to all elements from index `i` to `N-1`.

### Example

Initial array (size 6):

```text
Index:  0  1  2  3  4  5
A[] = [ 0, 0, 0, 0, 0, 0 ]
```

Queries:

```text
(3, 4)
(1, 3)
(4, -2)
(2, 2)
```

Apply each query naively:

- `(3, 4)`: add 4 from index 3 to 5
  - `[0, 0, 0, 4, 4, 4]`
- `(1, 3)`: add 3 from index 1 to 5
  - `[0, 3, 3, 7, 7, 7]`
- `(4, -2)`: add -2 from index 4 to 5
  - `[0, 3, 3, 7, 5, 5]`
- `(2, 2)`: add 2 from index 2 to 5
  - `[0, 3, 5, 9, 7, 7]`

Final array: `[0, 3, 5, 9, 7, 7]`

### Brute force (O(Q * N))

For every query, iterate from `i` to `N-1` and add `val`.

- Time: `O(Q * N)`
- Space: `O(1)`

### Optimized using prefix idea (O(Q + N))

Store the value at the query index, then take prefix sum once.

Example with queries:

```text
(1, 3)
(0, 2)
(4, 1)
```

Step 1: apply updates at start index:

```text
Index:  0  1  2  3  4
A[] = [ 0, 0, 0, 0, 0 ]

(1, 3) → A[1] += 3
(0, 2) → A[0] += 2
(4, 1) → A[4] += 1

A[] = [ 2, 3, 0, 0, 1 ]
```

Step 2: prefix sum:

```text
A[0] = 2
A[1] = 2 + 3 = 5
A[2] = 5 + 0 = 5
A[3] = 5 + 0 = 5
A[4] = 5 + 1 = 6

Final: [2, 5, 5, 5, 6]
```

```java
// A[] initially all zeros
for (i = 0; i < Q.length; i++) {
    int idx = Q[i][0];
    int val = Q[i][1];
    A[idx] += val;
}

// Convert to prefix sum array
for (i = 1; i < N; i++) {
    A[i] = A[i - 1] + A[i];
}
```

- Time: `O(Q + N)`
- Space: `O(1)` (in-place)

---

## 3. Zero Queries II

Given an all-zero array, perform queries `(i, j, val)`:
- Add `val` to all elements from index `i` to `j`.

### Example

Initial array (size 7):

```text
Index:  0  1  2  3  4  5  6
A[] = [ 0, 0, 0, 0, 0, 0, 0 ]
```

Queries:

```text
(0, 1, 3)
(1, 5, -1)
(2, 2, 5)
(3, 0, 1)
```

Using difference-array idea:

- For each `(i, j, val)`:
  - `A[i] += val`
  - If `j + 1 < N`, then `A[j + 1] -= val`

Apply:

1. `(0, 1, 3)`:
   - `A[0] += 3`
   - `A[2] -= 3`
2. `(1, 5, -1)`:
   - `A[1] += -1`
   - `A[6] -= -1` → `A[6] += 1`
3. `(2, 2, 5)`:
   - `A[2] += 5`
   - `A[3] -= 5`
4. `(3, 0, 1)`:
   - `A[3] += 1`
   - `A[1] -= 1`

After all updates (before prefix):

```text
A[] = [ 3, -2, 2, -4, 0, 0, 1 ]
```

Prefix sum:

```text
A[0] = 3
A[1] = 3 + (-2) = 1
A[2] = 1 + 2 = 3
A[3] = 3 + (-4) = -1
A[4] = -1 + 0 = -1
A[5] = -1 + 0 = -1
A[6] = -1 + 1 = 0
```

Final: `[3, 1, 3, -1, -1, -1, 0]`  
(You can adjust with your exact query set; the idea is the same.)

### Optimized implementation

```java
// A[] initially all zeros
for (each query (i, j, val)) {
    A[i] += val;
    if (j + 1 < N) {
        A[j + 1] -= val;
    }
}

// Prefix sum to get final array
for (i = 1; i < N; i++) {
    A[i] = A[i - 1] + A[i];
}
```

- Time: `O(Q + N)`
- Space: `O(1)` (in-place)

---

## 4. Rain Water Trapping

Given building heights, find total trapped rain water.

### Example

Heights:

```text
A = [2, 1, 3, 2, 1, 2, 4, 3, 2, 1, 3, 1]
```

Water trapped above each building depends on:

```text
water[i] = min(leftMax[i], rightMax[i]) - height[i]
```

Another example:

```text
Buildings: [6, 2, 7]
leftMax:   [6, 6, 7]
rightMax:  [7, 7, 7]

At index 1 (height 2):
water = min(6, 7) - 2 = 4
```

### Brute force (O(N^2))

For every building, compute left max and right max.

Example for `A = [4, 1, 3, 6, 2]`:

- For `i = 1` (height 1):
  - `lmax = max(4) = 4`
  - `rmax = max(3, 6, 2) = 6`
  - `water = min(4, 6) - 1 = 3`
- For `i = 2` (height 3):
  - `lmax = max(4, 1) = 4`
  - `rmax = max(6, 2) = 6`
  - `water = min(4, 6) - 3 = 1`

Total water = `3 + 1 = 4`.

```java
water = 0
for (i = 1; i <= N - 2; i++) {
    int lmax = 0;
    for (j = 0; j < i; j++) {
        lmax = Math.max(lmax, ht[j]);
    }

    int rmax = 0;
    for (j = i + 1; j < N; j++) {
        rmax = Math.max(rmax, ht[j]);
    }

    water += Math.min(lmax, rmax) - ht[i];
}

return water;
```

- Time: `O(N^2)`
- Space: `O(1)`

### Optimal with prefix arrays (O(N))

Build `leftMax[]` and `rightMax[]` arrays, then compute trapped water.

Example for `A = [4, 1, 3, 6, 2]`:

- `leftMax = [4, 4, 4, 6, 6]`
- `rightMax = [6, 6, 6, 6, 2]`

Water:

- `i = 1`: `min(4, 6) - 1 = 3`
- `i = 2`: `min(4, 6) - 3 = 1`
- Others: 0

Total = `4`.

```java
int[] leftMax(int[] A) {
    int[] left = new int[N];
    left[0] = A[0];
    for (int i = 1; i < N; i++) {
        left[i] = Math.max(left[i - 1], A[i]);
    }
    return left;
}

int[] rightMax(int[] A) {
    int[] rit = new int[N];
    rit[N - 1] = A[N - 1];
    for (int i = N - 2; i >= 0; i--) {
        rit[i] = Math.max(rit[i + 1], A[i]);
    }
    return rit;
}

public int trappingRain(int[] ht) {
    int[] left = leftMax(ht);
    int[] right = rightMax(ht);
    int ans = 0;
    for (int i = 1; i <= N - 2; i++) {
        ans += Math.min(left[i], right[i]) - ht[i];
    }
    return ans;
}
```

- Time: `O(N)`
- Space: `O(N)`

### Homework
Solve trapping rain water in `O(1)` extra space.
