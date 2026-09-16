# Binary Search

> Notes on binary search fundamentals and common problems.

## Contents
- Searching Basics
- Problems
  - Search in Sorted Array
  - First Occurrence
  - Finding Local Minima
  - Square Root of a Number

---

## Searching Basics

**The searching story:** Imagine you come back home and realize your sibling is missing. What's your first instinct? You search every room, every washroom, call their friends — but still can't find them. Eventually the police ask two key questions:

1. **Who is missing?** → This is the **Target**
2. **Where were they last seen?** → This is the **Search Space**

### Examples

| Target | Naive search space | Optimized search space |
|---|---|---|
| A word | Book, Newspaper, **Dictionary** | — |
| A phone number | Diary, Phone book, **Contact list** | — |

**Why does the search space matter?**
If the search space indicates the section where we can find the answer, that's a better (smaller/more targeted) search space.

### Searching "Dog" in a dictionary

```
Dict = { [A B C] [D E] [F G H ... Z] }
              ↑         ↑
```

This is the same idea as Binary Search — narrowing down which chunk of the dictionary to look in, rather than scanning page by page.

### Why split into halves (not thirds)?

- **Splitting into 3 parts:** Worst case discards only `N/3` elements → larger remaining search space.
- **Splitting into 2 parts:** Worst case discards `N/2` elements → smaller remaining search space.

**Conclusion:** Comparing the worst case of both scenarios, it's better to discard `N/2` elements at every step.

### When to apply Binary Search?

> After splitting the search space into two equal parts, if we are able to discard one half of the search space (with some logic), then we can apply Binary Search.

---

## Problem 1: Search for K (existence check)

Given a sorted array, check if `K` is present.

```
A[] = {3, 6, 9, 12, 14, 16, 20}

K = 12 → True
K = 17 → False
```

### Idea 1 — Linear Search
Check every element for `ele == K`.
- **TC:** O(N)
- **SC:** O(1)

### Idea 2 — Hashing
Push every element into a hash set, then check `hs.contains(K)`.
- **TC:** O(N)
- **SC:** O(N)

### Idea 3 — Binary Search

At each step, look at the middle element `A[m]`:

| Case | Condition | Action |
|---|---|---|
| 1 | `A[m] == K` | Return `True` |
| 2 | `A[m] > K` | Discard right half |
| 3 | `A[m] < K` | Discard left half |

### Worked example 1

```
A[] = {3, 6, 9, 12, 14, 19, 20, 23, 25}   K = 19
Indices:  0  1  2   3   4  5   6   7   8
Search space → 0 to 8
```

| lo | hi | mid | Compare A[mid] & K | Action |
|---|---|---|---|---|
| 0 | 8 | 4 | A[4]=14 < 19 | go to RHS, `lo = mid+1` |
| 5 | 8 | 6 | A[6]=20 > 19 | go to LHS, `hi = mid-1` |
| 5 | 5 | 5 | A[5]=19 == 19 | **return True** |

### Worked example 2

```
A[] = {3, 6, 9, 12, 14, 19, 20, 23, 25}   K = 13
Search space → 0 to 8
```

| lo | hi | mid | Compare A[mid] & K | Action |
|---|---|---|---|---|
| 0 | 8 | 4 | A[4]=14 > 13 | go to LHS |
| 0 | 3 | 1 | A[1]=6 < 13 | go to RHS |
| 2 | 3 | 2 | A[2]=9 < 13 | go to RHS |
| 3 | 3 | 3 | A[3]=12 < 13 | go to RHS |
| 4 | 3 | — | — | search space exhausted → **False** |

### Code

```java
boolean search(int[] A, int K) {
    int lo = 0;
    int hi = N - 1;

    while (lo <= hi) {
        int m = (lo + hi) / 2;
        if (A[m] == K) return true;
        else if (A[m] < K) lo = m + 1;
        else hi = m - 1;
    }
    return false;
}
```

- **TC:** O(log N)
- **SC:** O(1)

---

## Problem 2: First Occurrence of K

Given a sorted array, find the **first index** where `K` occurs.

```
A[] = {0, 0, 0, 0, 2, 2, 2, 2, 3, 4}
Index:  0  1  2  3  4  5  6  7  8  9

K = 2 → answer index 4
K = 0 → answer index 0
```

### Key idea

When `A[m] == K`, don't stop immediately — record it as a candidate answer and **keep searching the left half** (`hi = m - 1`) to see if an earlier occurrence exists.

| Case | Condition | Action |
|---|---|---|
| 1 | `A[m] == K` | `ans = m`, then `hi = m - 1` (keep looking left) |
| 2 | `A[m] > K` | go to LHS, `hi = m - 1` |
| 3 | `A[m] < K` | go to RHS, `lo = m + 1` |

> **Why this is correct:** Since we're looking for the *first* occurrence, once we find a match at `m`, we check `m-1` and keep checking leftward until we can't anymore. That way, we're guaranteed to land on the first occurrence once the search space is exhausted.
>
> **Time complexity:** O(log N) — not O(N), since we're still halving the search space each step, just tracking the best answer found so far.

### Trace example

```
A[] = {0, 0, 2, 2, 2, 2, 2, 2, 3, 4}   K = 2
                  ↑   ↑
                  hi  lo (converging toward first 2)

ans = index 2
```

### Code

```java
int search(int[] A, int K) {
    int lo = 0;
    int hi = N - 1;
    int ans = -1;

    while (lo <= hi) {
        int m = (lo + hi) / 2;
        if (A[m] == K) {
            ans = m;
            hi = m - 1;
        } else if (A[m] < K) {
            lo = m + 1;
        } else {
            hi = m - 1;
        }
    }
    return ans;
}
```

- **TC:** O(log N)
- **SC:** O(1)

---

## Problem 3: Finding a Local Minima

Given an **unsorted** array of distinct elements, return the index/value of any local minima.

**Definition:** An element `A[i]` is a local minima if it's less than both its adjacent elements:

```
A[i-1] > A[i] < A[i+1]
```

**Boundary rules:**
- `A[0]` is a local minima if `A[0] < A[1]`
- `A[N-1]` is a local minima if `A[N-1] < A[N-2]`

```
A[] = {9, 8, 7, 3, 6, 4, 1, 5, 2}
                 ↑        ↑     ↑
              local     local  local
              minima    minima minima
```

### Idea 1 — Brute Force

Iterate and check every element against both its left and right neighbor.
- **TC:** O(N)
- **SC:** O(1)

### Idea 2 — Binary Search

At middle index `m`, compare `A[m]` with `A[m-1]` and `A[m+1]`:

| Case | Shape | Action |
|---|---|---|
| 1 | `A[m] < A[m-1]` and `A[m] < A[m+1]` (valley) | Return `A[m]` — it's a local minima |
| 2 | Descending slope (`A[m-1] > A[m] > A[m+1]`) | Go to RHS |
| 3 | Ascending slope (`A[m-1] < A[m] < A[m+1]`) | Go to LHS |
| 4 | Peak (`A[m] > A[m-1]` and `A[m] > A[m+1]`) | Go to either side |

### Why this works — analyzing the descending case

```
A[] = {12, 10, 9, 6, 3}
```

**For the LHS (going left on a descending slope):** Worst case — we find no dip, hence no local minima on that side (array keeps decreasing monotonically toward the boundary).

**For the RHS (going right on a descending slope):** There will be **at least one** local minima, because the sequence must eventually stop decreasing (or hit the last element, which — if still decreasing — is itself a local minima by the boundary rule).

```
A[] = {12, 10, 9, 6, 3, 2, 1, 0}
                              ↑
                    guaranteed local minima at the end
```

### Code

```java
if (A.length == 1) return A[0];
if (A[0] < A[1]) return A[0];
if (A[N-1] < A[N-2]) return A[N-1];

int lo = 1, hi = N - 2;
while (lo <= hi) {
    int m = (lo + hi) / 2;
    if (A[m] < A[m-1] && A[m] < A[m+1]) {
        return A[m];
    } else if (A[m] < A[m-1] && A[m] > A[m+1]) {
        lo = m + 1;
    } else {
        hi = m - 1;
    }
}
```

### Trace example

```
A[] = {1, 0, 5, 4, 3, 6, 8, 9}
Index:  0  1  2  3  4  5  6  7
```

| lo | hi | m | Check | Action |
|---|---|---|---|---|
| 1 | 6 | 3 | A[3]=4 < A[2]=5 && A[3] > A[4]=3 | descending → `lo = m+1` |
| 4 | 6 | 5 | A[5]=6 > A[4]=3 && A[5] < A[6]=8 | ascending → `hi = m-1` |
| 4 | 4 | 4 | A[4]=3 < A[3]=4 && A[4] < A[5]=6 | valley → **return A[4] = 3** |

---

## Alternate mid formula (avoiding overflow)

```
Standard: m = (lo + hi) / 2

Example: lo = 98, hi = 99
m = (98 + 99) / 2 = 197 / 2   ← risk of overflow for large lo/hi
```

**Safer version:**

```
m = lo + (hi - lo) / 2
  = 98 + (99 - 98) / 2
  = 98 + 0.5
  = 98   (integer division)
```

This avoids overflow when `lo + hi` exceeds the integer range, since `hi - lo` is always smaller.

---

## Problem 4: Square Root of N

Given a number `N`, find `sqrt(N)` (integer floor value).

```
sqrt(25) → 5
sqrt(38) → 6
sqrt(50) → 7
sqrt(48) → 6
```

### Idea 1 — Linear Iteration

Start from `i = 1` and keep updating `ans` while `i * i <= N`.

**Example: N = 40**

| i | i*i ≤ N? | ans |
|---|---|---|
| 1 | 1 ≤ 40 | 1 |
| 2 | 4 ≤ 40 | 2 |
| 3 | 9 ≤ 40 | 3 |
| 4 | 16 ≤ 40 | 4 |
| 5 | 25 ≤ 40 | 5 |
| 6 | 36 ≤ 40 | 6 |
| 7 | 49 ≤ 40? No | **stop** |

**ans = 6**

### Idea 2 — Binary Search

**Target:** `√N` · **Search space:** `1` to `N`

| Case | Condition | Action |
|---|---|---|
| 1 | `m * m == N` | Return `m` |
| 2 | `m * m < N` | `ans = m`, go right (`lo = m + 1`) |
| 3 | `m * m > N` | go left (`hi = m - 1`) |

### Trace example: N = 40

| lo | hi | m | Compare m*m with N | Action |
|---|---|---|---|---|
| 1 | 40 | 20 | 400 > 40 | `hi = m-1` |
| 1 | 19 | 10 | 100 > 40 | `hi = m-1` |
| 1 | 9 | 5 | 25 < 40 | `ans = 5`, `lo = m+1` |
| 6 | 9 | 7 | 49 > 40 | `hi = m-1` |
| 6 | 6 | 6 | 36 < 40 | `ans = 6`, `lo = m+1` |
| 7 | 6 | — | — | **stop** |

**Final answer: 6**

### Trace example: N = 50 (answer = 7)

| Iter | l | r | m | m² | Comparison with N (50) | Action |
|---|---|---|---|---|---|---|
| 1 | 1 | 50 | 25 | 625 | 625 > 50 | go left (`r = 24`) |
| 2 | 1 | 24 | 12 | 144 | 144 > 50 | go left (`r = 11`) |
| 3 | 1 | 11 | 6 | 36 | 36 < 50 | go right (`l = 7`) |
| 4 | 7 | 11 | 9 | 81 | 81 > 50 | go left (`r = 8`) |
| 5 | 7 | 8 | 7 | 49 | 49 < 50 | go right (`l = 8`) |
| 6 | 8 | 8 | 8 | 64 | 64 > 50 | go left (`r = 7`) |
| 7 | 8 | 7 | — | — | `l > r` | **stop** |
| 8 | — | — | — | — | Break: `l > r` | **Answer = r = 7** |

### Code

```java
int ans = -1;
int lo = 1, hi = N;

while (lo <= hi) {
    int m = lo + (hi - lo) / 2;
    if (m * m == N) {
        return m;
    } else if (m * m < N) {
        ans = m;
        lo = m + 1;
    } else {
        hi = m - 1;
    }
}
return ans;
```

- **TC:** O(log N)
- **SC:** O(1)
