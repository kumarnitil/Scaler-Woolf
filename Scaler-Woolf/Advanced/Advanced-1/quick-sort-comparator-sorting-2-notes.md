# Sorting 2 — Partition, Quick Sort & Comparators

## Agenda
1. Partition of Array
2. Quicksort
3. Comparators
   - Sort based on factors
   - Largest number

---

## Partition of Array

**Problem:** Given an integer array, consider the **first element as pivot**, and rearrange the elements such that:

```
∀i,  A[i] ≤ P → move left
     A[i] > P → move right
```

```
[ <= P | P | > P ]
```

### Example 1

```
A[] = {54, 26, 93, 17, 77, 31, 44, 55, 20}
Ans[] = {26, 17, 31, 44, 20, 54, 55, 77, 93}
```

### Example 2

```
A = [10, 13, 7, 8, 25, 20, 23, 5]
Ans = {7, 8, 5, 10, 23, 20, 25, 13}
```

### Approaches

**1. Built-in sort** — `Arrays.sort(A)` → `{5, 7, 8, 10, 13, 20, 23, 25}` (this just fully sorts, doesn't preserve the "partition around pivot" requirement)

**2. Two Pointers Approach**

Place two pointers at index `0` and index `N-1`. If a smaller element (≤ pivot) comes, place it toward the front (`L`). If there's a larger element (> pivot), place it toward the back (`R`).

### Trace example 1

```
A[] = {54, 26, 93, 17, 77, 31, 44, 55, 20}
              L                    R
```

Elements > pivot(54) get swapped toward the right end (`R` decrements), elements ≤ pivot stay/move left (`L` increments). After processing:

```
⇒ swap pivot element (idx 0) with R's final index

Ans[] = {31, 26, 20, 17, 44, 54, 55, 77, 93}
```

### Trace example 2

```
A[] = {10, 13, 7, 8, 25, 20, 23, 5}
Ans[] = {8, 5, 7, 10, 20, 23, 25, 13}
```

### Code

```java
int pivot = A[0];
int L = 1, R = N - 1;

while (L <= R) {
    if (A[L] <= pivot) L++;
    else {
        swap(A[], L, R);
        R--;
    }
}

swap(A[], 0, R);   // place pivot element at its correct position
```
- **TC:** O(N)
- **SC:** O(1)

---

## Quick Sort

> Sorting is the process of organizing elements in a structured manner.

**Quicksort** is one of the most popular sorting algorithms, using `n log n` comparisons to sort an array of `n` elements in a typical situation. Quicksort is based on the **divide-and-conquer** strategy.

- A quick sort first selects a value, called the **pivot value**.
- Although there are many different ways to choose the pivot value, we'll simply use the first item in the list.
- The role of the pivot value is to assist with splitting the list.
- The actual position where the pivot value belongs in the final sorted list — called the **split point** or **pivot index** — is used to divide the list for subsequent recursive calls to quick sort.

### Full trace

```
A[] = {54, 26, 93, 17, 77, 31, 44, 55, 20}
                    ↓ partition around pivot 54
A[] = {31, 26, 20, 17, 44, 54, 55, 77, 93}
```

Recurse on the left `{31, 26, 20, 17, 44}` and right `{55, 77, 93}` segments (excluding the now-fixed pivot 54):

```
Left segment {31, 26, 20, 17, 44}
  → partition around 31 → {17, 26, 20, 31, 44}
       → recurse on {17, 26, 20}
             → partition around 17 → {17, 26, 20}  (17 is already in place, no left)
                   → recurse on {26, 20}
                         → partition around 20 → {20, 26}
                               → {26} placed
       → recurse on {44} (already a single element, placed)

Right segment {55, 77, 93}
  → partition around 55 → {55, 77, 93}  (55 already smallest → in place)
       → recurse on {77, 93}
             → partition around 93 → {77, 93}  (93 stays, recurse on {77})
```

Final sorted array: `{17, 20, 26, 31, 44, 54, 55, 77, 93}`

### Code

```java
void quicksort(int[] A, int L, int R) {
    if (L >= R) return;
    int pi = partition(A, L, R);
    quicksort(A, L, pi - 1);
    quicksort(A, pi + 1, R);
}

int partition(int[] A, int i, int j) {
    int pivot = A[i];
    int L = i + 1, R = j;

    while (L <= R) {
        if (A[L] <= pivot) L++;
        else {
            swap(A[], L, R);
            R--;
        }
    }

    swap(A[], 0, R);
    return R;
}
```
- **TC:** O(N log N)
- **SC:** O(log N)

### Best case vs Worst case

**Best Case** — pivot always splits the array roughly in half:

```
N → N/2, N/2 → N/4, N/4, N/4, N/4 → ... → 1

TC: O(N log N)
SC: O(log N)
```

**Worst Case** — pivot always ends up being the smallest (or largest) element, so each partition only removes one element:

```
N → N-1, ✗ → N-2, ✗ → N-3, ✗ → ... → 1

TC: O(N * N)
SC: O(N)
```

### Randomized Quick Sort

To avoid the worst case, choose the pivot randomly using `Math.random()`.

**Probability analysis of always picking the minimum element as pivot (the worst case):**

```
Given N elements, probability of picking pivot as minimum element = 1/N
Given N-1 elements, probability of picking pivot as minimum element = 1/(N-1)
                                                                      = 1/(N-2) ...

Overall probability = 1/N * 1/(N-1) * 1/(N-2) * ... = 1/N!
```

Since `N!` in the denominator drastically reduces the overall value, choosing the minimum element as pivot every single time has a vanishingly small probability. This is why randomized pivot selection makes the worst case extremely unlikely in practice.

```
Randomized Quick Sort:
TC: O(N log N)
SC: O(log N)
```

### Sorting in Java (reference)

**Sorting Algorithm:** TimSort (for objects), Dual-Pivot Quicksort (for primitives)

- `Arrays.sort()`:
  - Uses **Dual-Pivot Quicksort** for primitive types (`int[]`, `double[]`, etc.)
  - Uses **TimSort** for objects (`String[]`, `Integer[]`, etc.)
- `Collections.sort()`:
  - Uses **TimSort** (an optimized hybrid of Merge Sort and Insertion Sort)

---

## Comparators

> A **Comparator** is an interface which helps in custom comparison.

### Default sort behavior

```java
A[] = {5, 10, 3, 8, 4};
Arrays.sort(A) → {3, 4, 5, 8, 10}     // Ascending
```

```java
A[] = {"abc", "yogi", "karthick"};
Arrays.sort(A) → {"abc", "karthick", "yogi"}   // Dictionary/lexicographic order
```

### The `compare` function

A Comparator has a function known as `compare` — you write the logic to compare values according to the need, and it returns an integer:

| Return value | Meaning |
|---|---|
| Negative (`-ve`) | move `a` before `b` |
| Positive (`+ve`) | move `a` to the right of `b` |
| `0` | leave values as they are |

### Example — Sort in descending order

```
Q: Sort given array in descending order
A[] = {10, 3, 7, 2, 6}
```

```java
Arrays.sort(A, new logic());

public class logic implements Comparator<Integer> {
    public int compare(Integer a, Integer b) {
        if (a < b) return 1;
        else if (a > b) return -1;
        else return 0;
    }
}
```

```
Ans[] = {10, 7, 6, 3, 2}
```

---

## Problem 2: Sorting based on Factors

**Problem Statement:** Given an array of size `n`, sort the data in ascending order of the count of factors. If the count of factors is equal, sort those elements on the basis of their magnitude.

### Example 1

```
A[] = {9, 3, 10, 6, 4}
cf  = {3, 2,  4, 4, 3}    (count of factors for each element)

Ans[] = {3, 4, 9, 6, 10}
```

### Example 2

```
A = [10, 4, 5, 13, 1]
cf =  4  3  2   2  1

Ans[] = {1, 5, 13, 4, 10}
```

### Code — counting factors

```java
int factors(int x) {
    int c = 0;
    for (int i = 1; i * i <= x; i++) {
        if (x % i == 0) {
            if (i == x / i) c++;
            else c += 2;
        }
    }
    return c;
}
```

### Code — sort by factor count

```java
int[] sortByFactors(int[] A) {
    int[] cf = new int[N];
    for (int i = 0; i < n; i++) {
        cf[i] = factors(A[i]);     // O(N log(maxi)) overall for this loop
    }

    Arrays.sort(A, new logic());   // O(N log N)

    public class logic implements Comparator<Integer> {
        public int compare(Integer x, Integer y) {
            int fx = cf[x];
            int fy = cf[y];
            if (fx < fy) return -1;
            else if (fx > fy) return 1;
            else {
                if (x < y) return -1;
                else if (x > y) return 1;
                else return 0;
            }
        }
    }
}
```
- **TC:** O(N log(maxi) + N log N)
- **SC:** O(N)

---

## Problem 3: Largest Number

**Problem Statement:** Given a list of non-negative integers `nums`, arrange them such that they form the largest number, and return it. Since the result may be very large, return a **string** instead of an integer.

### Examples

```
A[] = {10, 3, 2}        → "3210"
A[] = {3, 30, 34, 5, 9}  → "9534330"
nums = [10, 5, 2, 8, 200] → "85220010"
```

### Core idea — comparing two numbers by concatenation

```
A[] = {53, 402}

53402  vs  40253
(x + y) compare (y + x)
```

Since `53402 > 40253`, `53` should come before `402`.

- Compare two numbers by **concatenating** them both ways: `x` appends `y`, compared with `y` appends `x`.
- For strings, this comparison is done using `compareTo()`.

```
"9".compareTo("8") → positive
"8".compareTo("9") → negative
"9".compareTo("9") → 0
```

### Trace example

```
a = 92, b = 23

String ab = a + "" + b = "9223"
String ba = b + "" + a = "2392"

value = ab.compareTo(ba)
9223 > 2392 → return -ve  (a should come first, i.e. "a before b")
```

### Code

```java
Arrays.sort(A, new logic());

public class logic implements Comparator<Integer> {
    public int compare(Integer a, Integer b) {
        String ab = (a + "" + b);
        String ba = (b + "" + a);

        int value = ab.compareTo(ba);

        if (value > 0) return -1;
        else if (value < 0) return 1;
        else return 0;
    }
}

StringBuilder ans = new StringBuilder();
for (int val : A) {
    ans.append(val);
}

if (ans.charAt(0) == '0') return "0";
else return ans.toString();
```

> The final `if (ans.charAt(0) == '0') return "0"` handles the edge case where all numbers are zero (e.g., `[0, 0]`), to avoid returning something like `"00"`.
