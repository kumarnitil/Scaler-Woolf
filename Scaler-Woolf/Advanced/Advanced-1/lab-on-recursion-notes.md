# Lab on Recursion

## Content
- Power function
- Indices of Array
- Tower of Hanoi

---

## Power Function

**Problem:** Given two integers `a` & `n`, find `a^n` using recursion.

```
a = 2, n = 3 → 2^3 = 2*2*2 = 8
a = 4, n = 2 → 4^2 = 4*4 = 16
```

```
a^n   = a * a * a * ... * a     (n times)
a^n-1 = a * a * a * ... * a     (n-1 times)

a^n = a^(n-1) * a  →  pow(a, n) = pow(a, n-1) * a
```

### Idea 1 — Linear recursion

```java
long pow(int a, int n) {
    if (n == 0) return 1;
    int rec = pow(a, n - 1);
    return a * rec;
}
```
- **TC:** O(N)
- **SC:** O(N)

### Trace: pow(5, 3) = 125

```
pow(5,3): rec = pow(5,2) = 25, return 5*25 = 125
    ↓
pow(5,2): rec = pow(5,1) = 5, return 5*5 = 25
    ↓
pow(5,1): rec = pow(5,0) = 1, return 5*1 = 5
    ↓
pow(5,0): base case → return 1
```

### Idea 2 — Fast Power (halving)

```
2^10 → 2^5 * 2^5
2^11 → 2^5 * 2^5 * 2

3^12 → 3^6 * 3^6
3^13 → 3^6 * 3^6 * 3

a^n : n is even → a^n = a^(n/2) * a^(n/2)
a^n : n is odd  → a^n = a^(n/2) * a^(n/2) * a
```

### Code (naive fast-power — makes two recursive calls)

```java
long pow(int a, int n) {
    if (n == 0) return 1;
    if (n % 2 == 0)
        return pow(a, n / 2) * pow(a, n / 2);
    else
        return pow(a, n / 2) * pow(a, n / 2) * a;
}
```

### Deriving the time complexity

```
Total time taken by pow(a, n)    = f(n)
Total time taken by pow(a, n/2)  = f(n/2)

f(n) = f(n/2) + f(n/2) + 1
f(n) = 2f(n/2) + 1        f(0) = 1, f(1) = 1   ← Recurrence relation
```

**Substitution:**
```
f(n) = 2f(n/2) + 1 = 2^1 f(n/2^1) + 2^1 - 1            ...1
f(n/2) = 2f(n/4) + 1

f(n) = 2*(2f(n/4) + 1) + 1
f(n) = 4f(n/4) + 3 = 2^2 f(n/2^2) + 2^2 - 1             ...2
f(n/4) = 2f(n/8) + 1

f(n) = 4*(2f(n/8) + 1) + 3
     = 8f(n/8) + 7 = 2^3 f(n/2^3) + 2^3 - 1             ...3

After k-th substitution:
f(n) = 2^k f(n/2^k) + 2^k - 1        f(0) = 1 ✗, f(1) = 1 ✓

n/2^k = 0 → N = 0 ✗ (invalid, we want the base case to be n=1)
n/2^k = 1 → n = 2^k → k = log(n)

f(n) = n*1 + n - 1
     = 2n - 1

TC ≈ O(N)
SC = O(log N)
```

**Recursion tree — showing why this "naive" fast-power is still O(N) despite halving each time:**
```
              n
          ↙       ↘
        n/2       n/2
      ↙   ↘      ↙   ↘
    n/4  n/4   n/4  n/4      ← total nodes at each level double as n halves,
     :    :     :    :          net total work is still linear
   (Level log(n))
```

> This version still does O(N) total work because it makes **two** recursive calls per level even though the input halves — the redundant computation (`pow(a, n/2)` computed twice) cancels out the savings from halving.

### Idea 3 — True Fast Power (compute the half only once)

```java
long pow(int a, int n) {
    if (n == 0) return 1;
    int rec = pow(a, n / 2);
    if (n % 2 == 0) return rec * rec;
    else return rec * rec * a;
}
```
- **TC:** O(log N)
- **SC:** O(log N)

### Trace: pow(5, 11)

```
pow(a,n) 5,11
    ↓
pow(a,n) 5,5
    ↓
pow(a,n) 5,2
    ↓
pow(a,n) 5,1
    ↓
pow(a,n) 5,0
```

Unwinding (bottom-up):
```
pow(5,0): rec = pow(5,0) → base case = 1
pow(5,1): rec = pow(5,0) = 1, n odd → rec*rec*a
pow(5,2): rec = pow(5,1) = 5, n even → rec*rec = 25
pow(5,5): rec = pow(5,2) = 25, n odd → rec*rec*a = 25*25*5 = 3125
pow(5,11): rec = pow(5,5) = 3125, n odd → rec*rec*a = 3125*3125*5   (Ans = 25*25*5, shown for the 5,2 → 5,5 step)
```

---

## All Indices of Array

**Problem Statement:** Given an array of integers `A` with `N` elements and a target integer `B`, find all the indices at which `B` occurs in the array. It is guaranteed that the target `B` exists at least once in the array `A`.

```
A[] = {4, 5, 3, 1, 5, 4, 5}   B = 5
Indices:  0  1  2  3  4  5  6

Ans[] = {1, 4, 6}
```

**Another example:**
```
A = [1, 2, 3, 1, 1]
B = 1
Ans[] = [0, 3, 4]
```

### Observation
Need two variables:
1. Iterate on array
2. Count no. of elements equal to target

### Trace (building intuition): A = {1, 2, 3, 1, 1}, B = 1

```
recur(A, B, 5, 3)                      → base case, return new int[3]
recur(A, B, 4, 2) → sets res[2] = 4    (A[4] == B)
recur(A, B, 3, 1) → sets res[1] = 3    (A[3] == B)
recur(A, B, 2, 1)
recur(A, B, 1, 1)
recur(A, B, 0, 0) → sets res[0] = 0    (A[0] == B)

Ans = [0, 3, 4]
```

### Code

```java
int[] recur(int[] A, int B, int idx, int cnt) {
    if (idx == A.length) return new int[cnt];

    if (A[idx] == B) cnt = cnt + 1;

    int[] res = recur(A, B, idx + 1, cnt);

    if (A[idx] == B) {
        res[cnt - 1] = idx;
    }

    return res;
}
```
- **TC:** O(N)
- **SC:** O(N)

### Trace: A = {3, 2, 4, 2, 2, 6}, B = 2 (call in main with idx=0, cnt=0)

```
idx: 0  1  2  3  4  5  6
cnt: 0  0  1  1  2  3  3

Result array is allocated at idx==6 with size cnt=3,
then filled in on the way back up: res = [1, 3, 4]
```

---

## Tower of Hanoi

There are `n` disks placed on tower A, of different sizes.

**Goal:** Move all disks from tower A to tower C, using tower B if needed.

**Constraints:**
1. Only 1 disk can be moved at a time.
2. A larger disk can't be placed on a smaller disk at any step.

**Task:** Print the movement of disks from A to C in minimum steps.

### N = 1

```
A: [1]   B: []   C: []
     ↓
A: []   B: []   C: [1]

Output: 1: A → C
```

### N = 2

```
A: [1,2]   B: []   C: []

1: A → B    → A: [2]    B: [1]    C: []
2: A → C    → A: []     B: [1]    C: [2]
1: B → C    → A: []     B: []     C: [1,2]
```

### N = 3

```
A: [1,2,3]   B: []   C: []

1: A → C
2: A → B
1: C → B      → A: [3]      B: [1,2]    C: []
3: A → C      → A: []       B: [1,2]    C: [3]
1: B → A
2: B → C
1: A → C      → A: []       B: []       C: [1,2,3]
```

### General idea for N disks

1. Move `(N-1)` disks from `src` (A) to `helper` (B).
2. Move the `N`th disk from `src` to `dest`.
3. Move all `(N-1)` disks from `B` tower to `C` tower.

### Code

```java
void TOH(int n, A, B, C) {   // A=src, B=help, C=dest
    if (n == 0) return;
    TOH(n - 1, A, C, B);
    print(n: A → C);
    TOH(n - 1, B, A, C);
}
```

**General pattern for the tree:**
```
if (N == 0) return;
left call  = TOH(N-1, src, dest ↔ help swapped)
print(n: src → dest)
right call = TOH(N-1, help ↔ src swapped, dest)
```

### Recursion Tree for TOH(3, A, B, C)

```
                         TOH(3,A,B,C)
                    ↙                    ↘
          TOH(2,A,C,B)                TOH(2,B,A,C)
         ↙          ↘                ↙           ↘
   TOH(1,A,B,C)  TOH(1,C,A,B)   TOH(1,B,C,A)   TOH(1,C,B,A)
    ↙     ↘        ↙     ↘        ↙     ↘        ↙     ↘
 TOH(0) TOH(0)  TOH(0) TOH(0)  TOH(0) TOH(0)  TOH(0) TOH(0)
```

**Output sequence:**
```
1: A → C
2: A → B
1: C → B
3: A → C
1: B → A
2: B → C
1: A → C
```

### Complexity

```
TC: O(2^N)
SC: O(N)
```
