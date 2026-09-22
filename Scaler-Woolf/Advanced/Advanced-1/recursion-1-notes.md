# Recursion

## Today's Content
- Recursion Introduction
- Function Call Tracing
- Factorial of No.
- Increasing
- Decreasing
- Fibonacci no.

---

## Recursion Introduction

```
Recursion → Function calling itself
          → Repeatable task
          → Solve a problem by using a subproblem
                                    (a smaller instance of same problem)
```

### Sum of First 5 Natural Numbers

```
sum(5) = 1 + 2 + 3 + 4 + 5
sum(4) = 1 + 2 + 3 + 4

sum(5) = sum(4) + 5
              ↑
          subproblem
```

```
sum(N) = [1 + 2 + 3 + ... + (N-1)] + N
                   ↑
               sum(N-1)

sum(N) = sum(N-1) + N
```

---

## How to Write Recursive Code

1. **Expectations** → Decide what our function is supposed to do.
2. **Main logic** → Breaking main problem into subproblem & then using it to get final answer.
3. **Base Case** → Last valid input for which recursion needs to stop.

### Q: Write a function that calculates & returns sum of N natural numbers. (N ≥ 1)

**Expectation:** calculate & return sum of N.

```java
int sum(int N) {
    if (N == 1) return 1;
    int rec = sum(N - 1) + N;
    return rec;
}
```
- **TC:** O(N)
- **SC:** O(N)

```java
void main() {
    N = 4;
    int ans = sum(N);   // 10
    print(ans);
}
```

### Trace: sum(4)

```
sum(N=4): rec = sum(N-1) + N = 6 + 4 = 10
    ↓
sum(N=3): rec = sum(N-1) + N = 3 + 3 = 6
    ↓
sum(N=2): rec = sum(N-1) + N = 1 + 2 = 3
    ↓
sum(N=1): base case → return 1
```

---

## Function Call Tracing

### Code Block

```java
int add(int x, int y) { return x + y; }
int mul(int x, int y) { return x * y; }
int sub(int x, int y) { return x - y; }
void print(int x) { print(x); }
```

### Program

```java
main() {
    n = 10, y = 20;
    print( sub( mul( add(n, y), 30 ), 75 ) );
}
```

### Trace

```
add(n, y) = add(10, 20) = 30
mul(add(n,y), 30) = mul(30, 30) = 900
sub(mul(...), 75) = sub(900, 75) = 825
print(825)
```

### Conclusion
- Function call will go & get stacked at the top.
- The child call will go & return its answer to the parent.
- Parent will wait for the children to return their answer before moving forward.

---

## Factorial of N

**Problem:** Given a non-negative number N, find factorial of N. (N ≥ 0)

```
fact(5) = 5 * 4 * 3 * 2 * 1 = 120
fact(5) = 5 * fact(4)

fact(N) = N * (N-1) * (N-2) * ... * 2 * 1
fact(N) = N * fact(N-1)
```

```
0! = 1
```

### Code

```java
int fact(int N) {
    if (N == 0) return 1;
    int rec = fact(N - 1);
    return N * rec;
}
```
- **TC:** O(N)
- **SC:** O(N)

```java
void main() {
    ans = fact(4);   // 24
}
```

### Trace: fact(4)

```
fact(N=4): rec = fact(3) = 6, return 4 * 6 = 24
    ↓
fact(N=3): rec = fact(2) = 2, return 3 * 2 = 6
    ↓
fact(N=2): rec = fact(1) = 1, return 2 * 1 = 2
    ↓
fact(N=1): rec = fact(0) = 1, return 1 * 1 = 1
    ↓
fact(N=0): base case → return 1
```

---

## Problem: Print all numbers from 1 to N in increasing order

```
Inc(5): 1 2 3 4 5
Inc(4): 1 2 3 4
```

**Main logic:**
```
Inc(4)
print(5);
```

```
Inc(5) ⇒ Inc(4)
          print(5);

Inc(N) ⇒ Inc(N-1)
          print(N)
```

### Code

```java
void Incre(int N) {
    if (N == 0) return;
    Incre(N - 1);
    System.out.print(N);
}
```
- **TC:** O(N)
- **SC:** O(N)

> Equivalent base case style: `if (N == 1) { SOP(N); return; }`

### Trace: Incre(3)

```
Incre(N=3): Incre(N-1) → then print(3)
    ↓
Incre(N=2): Incre(N-1) → then print(2)
    ↓
Incre(N=1): Incre(N-1) → then print(1)
    ↓
Incre(N=0): base case, return
```

**Output:** `1 2 3`

### Question: What if we have to print from N to 1?

```java
void Decr(int N) { ... }
```

(Left as a follow-up exercise — the answer is to print *before* the recursive call instead of after.)

---

## Print Array Using Recursion

```
A[] = {1, 2, 3, 4, 5}
output = 1 2 3 4 5
```

```java
void printAr(int[] A, int idx) {   // called initially with idx = 0
    if (idx == N) return;
    System.out.print(A[idx]);
    printAr(A, idx + 1);
}
```
- **TC:** O(N)
- **SC:** O(N)

---

## Time & Space Complexity for Recursion

```
Time complexity  = Time taken by our recursive code
Space complexity = Amount of space taken by function call in stack
```

```
Time Complexity  = O(Number of function calls * Time per function call)
Space Complexity = O(Maximum depth of recursion tree/stack space * Space per function call)
```

### Deriving TC for `fact(N)`

```java
int fact(int N) {
    if (N == 0) return 1;
    int rec = fact(N - 1);
    return N * rec;
}
```

```
T(N)   = Total time taken by fact(N)
T(N-1) = Total time taken by fact(N-1)

T(N) = T(N-1) + 1      ┐
T(0) = 1                ┴─ Recurrence relation
```

**Substitution:**
```
T(N) = T(N-1) + 1                             ... 1st equation
T(N-1) = T(N-2) + 1

T(N) = T(N-2) + 1 + 1
T(N) = T(N-2) + 2                             ... 2nd equation
T(N-2) = T(N-3) + 1

T(N) = T(N-3) + 1 + 2
T(N) = T(N-3) + 3                             ... 3rd equation

After k-th substitution:
T(N) = T(N-k) + k         T(0) = 1

N - k = 0  ⇒  N = k

T(N) = 1 + k = 1 + N

T(N) = O(N)
```

### Recursive Tree for Factorial

```
5!
5 * 4!
4 * 3!
3 * 2!
2 * 1!
1
```

**SC:** O(N) — the stack depth equals N.

---

## Fibonacci Series

```
N:      0 1 2 3 4 5 6 7
fib(N): 0 1 1 2 3 5 8 13
```

```
fib(N) = sum of previous two fibonacci numbers
fib(5) = fib(4) + fib(3)

fib(N) = fib(N-1) + fib(N-2)
```

### Code

```java
int fib(int N) {
    if (N <= 1) return N;
    return fib(N - 1) + fib(N - 2);
}
```
> Equivalent to: `if (N == 0) return 0; if (N == 1) return 1;`

### Base case reasoning

Base case → last valid input for which we know the answer & we want recursion to stop.

```
N = 0 → fib(-1) + fib(-2)   (invalid — these calls should never actually be made)
N = 1 → fib(0) + fib(-1)    (invalid)
N = 2 → fib(1) + fib(0)     (valid — both are within N ≥ 0)
```

### Recursion Tree for fib(4)

```
                    fib(4)
              2 ↙          ↘ 1
         fib(3)             fib(2)
       1 ↙    ↘ 1          1 ↙  ↘ 0
   fib(2)     fib(1)   fib(1)  fib(0)
 1 ↙   ↘ 0
fib(1) fib(0)

Ans = 2 + 1 = 3
```

### Counting total calls (level by level)

```
On level 1 = 2^0
On level 2 = 2^1
On level 3 = 2^2
...
On level x (last level) = 2^(x-1) ⇒ 2^(N-1)     (x and N are the same)

Total calls = 2^0 + 2^1 + 2^2 + 2^3 + ... + 2^(N-1)

sum = a(r^N - 1)/(r - 1) ⇒ 2^0 * (2^N - 1) / (2 - 1)
    ⇒ 2^N - 1
```

```
TC: O(2^N + 1) ≈ O(2^N)
SC: O(N)
```
