# Modulus & GCD

## Content
- Modulus
1. Modular Arithmetic
2. (numbering as in source)
3. Fast pow function
4. Count pair with sum mod = 0
5. Intro to GCD
6. Properties of GCD

---

## Modulus Operator

`A % B` = returns remainder when A is divided by B.

```
Dividend = Divisor * quotient + remainder
rem = Dividend - Divisor * quotient
                 (largest multiple of divisor ≤ dividend)
```

### Examples

```
1. 10 % 4 = 2
   = 10 - (largest mul of 4 ≤ 10)
   = 10 - 8 = 2

2. 13 % 5 = 3
   = 13 - (largest mul of 5 ≤ 13)
   = 13 - 10 = 3

3. -40 % 7 = 2
   = -40 - (largest mul of 7 ≤ -40)
   = -40 - (-42) = 2

4. -60 % 9 = -60 - (-63)
   = -60 + 63 = 3
```

### Range of modulus output

```
x % M = [min: 0, max: M-1]
```

⇒ Modulus operator is used to limit/restrict the range of output.

```
-∞
 :  } % M = {0 to M-1}
 :
 ∞
```

```
M = 10^9 + 7 → very close to int range, and it's a prime number
```

---

## Modular Arithmetic (`%` with `+`, `-`, `*`, `/`)

### 1. `(a + b) % M ⇒ (a%M + b%M) % M`

```
a = 13, b = 9   (max value I can store = 15)
m = 5

(13 + 9) % 5 = (13%5 + 9%5) % 5
   22 % 5    = (3 + 4) % 5
   ⇒ 2       = 7 % 5 ⇒ 2
```

### 2. `(a * b) % M ⇒ (a%M * b%M) % M`

```
a = 13, b = 9   (max value I can store = 15)
m = 15

(13 * 9) % 5   = (13%15 * 9%15) % 15
117 % 15       ⇒ (3 * 9) % 15
⇒ 12           ⇒ 27 % 15 = 12
```

> Whenever we have to calculate `%`, we should first let it go beyond the range of M (i.e., don't prematurely reduce operands past what's needed — apply `%M` at each multiplication/addition step, not skip it).

### 3. `(a - b) % M = (a%M - b%M + M) % M`

```
a = 13, b = 9   (max value I can store = 15)
m = 5

(13 - 9) % 5 = (13%5 - 9%5) % 5
= 4 % 5      ⇒ (3 - 4) % 5
⇒ 4          ⇒ -1 % 5 ⇒ -1
             ⇒ -1 + 5 = 4   (+M)
```

> **Note:** Either add `+M` at the last step if the answer is negative, OR add it directly in the formula as `(a%M - b%M + M)`.

### 4. `(a / b) % M ⇒ (a * b⁻¹) % M`

This uses **Inverse Modulo** (via **Fermat's Little Theorem**).

### 5. `((a%M) % M) % M = a % M`
(Repeated modulus on an already-reduced value doesn't change it.)

### 6. `(a^b) % M = ((a%M)^b) % M`

### Worked example

**Evaluate:** `(37^103 - 1) % 12`

```
⇒ (37^103 % 12 - 1 % 12 + 12) % 12
⇒ ((37 % 12)^103 % 12 - 1 + 12) % 12
⇒ ((1)^103 % 12 + 11) % 12
⇒ (1 + 11) % 12
⇒ 12 % 12 ⇒ 0
```

---

## Fast Power with Mod using Recursion

**Problem Statement:** Given three integers `a`, `n` and `m`. Find `a^n % m` using recursion.

**Constraints:**
```
1 <= a <= 10^9
1 <= n <= 10^9
2 <= m <= 10^9
```

### Idea 1 — Calculate `a^n` first, then take `%M` ❌

```
(10^9)^(10^9)   ← way too large, impossible to compute directly
```

### Approach 2 — Use Fast Pow Function

```
2^6 → 2^3 * 2^3         a^n = a^(n/2) * a^(n/2)   (n is even)
2^9 → 2^4 * 2^4 * 2     a^n = a^(n/2) * a^(n/2) * a   (n is odd)
```

### First attempt (has overflow issues)

```java
int powmod(int a, int n, int m) {
    if (n == 0) return 1;
    long y = powmod(a, n / 2, m);
    if (n % 2 == 0) return (y * y) % M;
    else return (y * y * a) % M;
}
```
> Will this work? → **No.**

```
long y = powmod(a, n/2, m);   // max y = 10^9
if (n % 2 == 0) return (y * y) % M;   // max answer = 10^9 * 10^9 = 10^18 — overflows int
```

### Fix — apply `%M` before multiplying further

```
return (y * y * a) % M
        j   * k
(j * k) % M = (j%M * k%M) % M

((y*y) % M * a%M) % M
   ⇒ (10^9 * 10^9) % M
   ⇒ 10^18 % M    (still needs a `long` intermediate, but result fits after mod)
```

### Correct code

```java
int powmod(int a, int n, int m) {
    if (n == 0) return 1;
    long y = powmod(a, n / 2, m);
    if (n % 2 == 0) return (int)(y * y) % M;
    else return (int)((y * y) % M * a % M) % M;
}
```

---

## Problem: Count Pairs with (A[i] + A[j]) % M == 0

**Problem Statement:** Given N array elements, find count of pairs such that `(A[i] + A[j]) % m == 0`.

> Note: `i != j` && `pair(i, j) = pair(j, i)` (unordered pairs)

```
A = {4, 7, 6, 5, 5, 3}   m = 3   → Ans = 5
```

| i | j | A[i] | A[j] | (A[i]+A[j]) % m |
|---|---|---|---|---|
| 0 | 3 | 4 | 5 | (4+5)%3 = 0 |
| 0 | 4 | 4 | 5 | (4+5)%3 = 0 |
| 1 | 3 | 7 | 5 | (7+5)%3 = 0 |
| 1 | 4 | 7 | 5 | (7+5)%3 = 0 |
| 2 | 5 | 6 | 3 | (6+3)%3 = 0 |

### Brute Force
Consider all pairs & get the sum of each pair & take modulus. If `modulus answer == 0`, `count++`.
- **TC:** O(N²)
- **SC:** O(1)

### Idea 2 — Remainder pairing

```
(A[i] + A[j]) % M = 0
⇓
(A[i]%M + A[j]%M) % M = 0
```

**Edge case / remainder pairing table:**
```
0  + 0
1  + (M-1)
2  + (M-2)
3  + (M-3)
...
K  + (M-K)
```

**Example: M = 4**
```
A[]  = {13, 14, 22, 3, 32, 19, 16}
rem[] = {1,  2,  2, 3,  0,  3,  0}

Ans = 4
```

**Example: M = 5**
```
A[]  = {6, 7, 5, 11, 19, 20, 9, 15, 14, 13, 12, 23}
rem   = {1, 2, 0, 1,  4,  0,  4, 0,  4,  3,  2,  3}

count[rem] = {3, 2, 2, 2, 3}   (indexed by rem value 0..4)
```

**Pairing remainder counts:**
```
cnt[1] * cnt[5-1] = 2 * 3 = 6
cnt[2] * cnt[5-2] = 2 * 2 = 4
cnt[0] & cnt[0]   = C(3,2) = 3*(3-1)/2 = 3

Total = 13 pairs
```

### Code

```java
int countPairs(int[] A, int M) {
    int[] cnt = new int[M];
    for (int i = 0; i < n; i++) {
        int rem = A[i] % M;
        cnt[rem]++;
    }

    int ans = 0;
    int x = cnt[0];
    ans = ans + x * (x - 1) / 2;

    if (M % 2 == 0) {
        int y = cnt[M / 2];
        ans = ans + y * (y - 1) / 2;
    }

    int i = 1, j = M - 1;
    while (i < j) {
        ans = ans + (cnt[i] * cnt[j]);
        i++; j--;
    }

    return ans;
}
```
- **TC:** O(N + M)
- **SC:** O(M)

---

## GCD

```
GCD = Greatest Common Divisor
HCF = Highest Common Factor

GCD(A, B) = largest number that divides both A & B.
```

### Examples (by listing divisors)

```
GCD(15, 25):
  Divisors of 15: 1, 3, 5, 15
  Divisors of 25: 1, 5, 25
  Ans = 5

GCD(12, 30):
  Divisors of 12: 1, 2, 3, 4, 6, 12
  Divisors of 30: 1, 2, 3, 5, 6, 10, 15, 30
  Ans = 6

GCD(-10, 25):
  Divisors of -10: -10, -5, -2, -1, 1, 2, 5, 10
  Divisors of 25: 1, 5, 25
  Ans = 5

GCD(4, 7):
  Divisors of 4: 1, 2, 4
  Divisors of 7: 1, 7
  Ans = 1

GCD(0, 3):
  Divisors of 0: 1, 2, 3, 4, ..., ∞
  Divisors of 3: 1, 3
  Ans = 3
```

---

## Properties of GCD

1. `GCD(A, B) = GCD(B, A)`
2. `GCD(A, B) = GCD(|A|, |B|)`
3. `GCD(0, A) = |A|`
4. `GCD(A, B, C) = GCD(A, GCD(B, C)) = GCD(GCD(A, B), C) = GCD(B, GCD(A, C))`
5. `GCD(A, B) = GCD(A - B, B)`

### Verifying property 5

```
GCD(10, 2) = 2
GCD(8, 2)  = 2
GCD(6, 2)  = 2
```

```
GCD(A, B) = GCD(A-B, B)
          = GCD(A-2B, B)
          :
          = GCD(A-xB, B)
             (A - greatest mul of B ≤ A)
```

This leads to: **`GCD(A, B) = GCD(A % B, B)`**

---

## Writing a Function for GCD(A, B)

### Naive (wrong) attempt — infinite loop risk

```
GCD(A, B) = GCD(A%B, B)
```

```
GCD(24, 16) → GCD(8, 16) → GCD(8, 16) → infinite loop
```
(Because the arguments are being applied in the wrong order — the modulus needs to be paired with the *other* value to keep shrinking.)

### Correct recurrence

```
GCD(A, B) = GCD(B, A % B)
```

### Traces

```
GCD(24, 16) → GCD(16, 8) → GCD(8, 0)   Ans = 8

GCD(14, 21) → GCD(21, 14) → GCD(14, 7) → GCD(7, 0)   Ans = 7

GCD(105, 10) → GCD(10, 5) → GCD(5, 0)   Ans = 5

GCD(23, 25) = GCD(25, 23) → GCD(23, 2) → GCD(2, 1) → GCD(1, 0)   Ans = 1
```

### Code

```java
int GCD(int A, int B) {
    if (B == 0) return A;
    return GCD(B, A % B);
}
```
- **TC:** O(log(max(A, B)))
- **SC:** O(log(max(A, B)))
