# Binary Search 2 — Binary Search on Answer

> Notes on the "Binary Search on Answer" pattern: Painter's Partition I & II, Aggressive Cows.

## Contents
1. Painter's Partition I
2. Painter's Partition II
3. Aggressive Cows

## Problems to Practice
- Maximum shipping capacity within D days
- A Magical No.
- Koko eating Bananas
- Allocate min no. of pages / Allocate books

---

## Painter's Partition — Problem I

Given `N` boards with lengths, and some rules:

a) A painter takes `T` unit of time to paint 1 unit of length.
b) A board can only be painted by 1 painter.
c) A painter can only paint boards placed next to each other (i.e. a continuous segment).

**Question:** Find the **minimum number of painters** required to paint all boards within `X` units of time. Return `-1` if not possible.

### Example — not possible

```
N = 4
Boards: [2, 1, 1, 4]
T = 3 mins/unit
X = total time available for 1 painter
```

```
A[] = {5, 3, 6, 1, 9}   T = 2 min   X = 15 minutes

P1        P2      P3        P3    P4
[  5  ]  [ 3 ]  [   6   ]  [ 1 ]  [   9   ]  ← ✗
10min    6min     12mins     2      18
```

For the board of length 9, we at least need 18 minutes for 1 painter, but the maximum time allocated for one painter is 15 minutes — thus it's **not possible** to paint all boards, so return `-1`.

### Example — X = 30 min (feasible, greedy grouping)

```
A[] = {5, 3, 6, 1, 9}   T = 2 min   X = 30 min

P1: [5][3][6][1] → 10+6+12+2 = 30 min (within X)
P2: [9]           → 18 min

Ans = 2 painters
```

### Example — X = 20 min

```
A[] = {5, 3, 6, 1, 9}   T = 2 min   X = 20 min

P1: [5][3]  → 10+6 = 16 min
P2: [6][1]  → 12+2 = 14 min
P3: [9]     → 18 min

Ans = 3 painters
```

### Code: `minPainters`

```java
int minPainters(int[] A, int T, int X) {
    int painters = 1;
    int timeLeft = X;

    for (int i = 0; i < N; i++) {
        if (A[i] * T > X) return -1;   // single board too big for anyone

        if (A[i] * T <= timeLeft) {
            timeLeft = timeLeft - A[i] * T;
        } else {
            painters++;
            timeLeft = X - A[i] * T;
        }
    }
    return painters;
}
```

- **TC:** O(N)
- **SC:** O(1)

### Trace: A[] = {5, 3, 6, 1, 9}, T = 2 min, X = 20 min

```
Painter 1, then Painter 2, then Painter 3
TL = 20 - 10 = 10
   = 10 - 6  = 4        (still painter 1, boards 5 & 3)
TL = 20 - 12 = 8
   = 8 - 2   = 6         (painter 2, boards 6 & 1)
TL = 20 - 18 = 2          (painter 3, board 9)
```

---

## Painter's Partition — Problem II

Find the **minimum time** to paint all the boards if `P` painters are available.

### Example 1 — P = 1 painter

```
Boards: [5][3][6][1][9]   T = 2 min/unit
```

```
Min time = 5*2 + 3*2 + 6*2 + 1*2 + 9*2
         = (5 + 3 + 6 + 1 + 9) * 2
         = 24 * 2
         = 48 minutes
```

### Example 2 — P = 2 painters

Same boards `[5, 3, 6, 1, 9]`, T = 2 min/unit. Consider different ways of splitting the boards between the 2 painters:

| Case | P1 boards | P1 time | P2 boards | P2 time | Total time (max of the two) |
|---|---|---|---|---|---|
| 1 | 1 board | 5×2 = 10 min | 4 boards | 19×2 = 38 min | **38 min** |
| 2 | 2 boards | 8×2 = 16 min | 3 boards | 16×2 = 32 min | **32 min** |
| 3 | 3 boards | 14×2 = 28 min | 2 boards | 10×2 = 20 min | **28 min** ✅ best |
| 4 | 4 boards | 15×2 = 30 min | 1 board | 9×2 = 18 min | **30 min** |

**Best split found by brute force = Case 3, 28 minutes.**

### Why a greedy "equal split" approach fails

```
A[] = {1, 2, 3, 4, 100}   P = 2 painters

Total length of boards = 110
Naive idea: split boards length equally between 2 painters = 110 / 2 = 55
Total time = 55 minutes   ✗ (wrong / not achievable this way)
```

Actual optimal split:

```
P1 = [1, 2, 3, 4] → 10 min
P2 = [100]        → 100 min

Total time = 100 min
```

The greedy "split total length evenly" approach doesn't respect the constraint that boards must be painted as continuous segments — so it doesn't give the correct answer. This is why we use **binary search on the answer** instead.

### Binary Search on the Answer — Setup

```
Target = min time

Search Space:
  lo = max(element) * T     → minimum possible time (that one painter alone would need for the largest board)
  hi = sum(all elements) * T → maximum possible time (single painter paints everything)
```

```
[ Min time ]---------[ mid ]---------[ Max time ]
                        ↓
                 represents a candidate time
```

### Decision logic

For a candidate `mid` time value, find the minimum number of painters required to paint all boards within `mid` time → call it `cnt`.

| Condition | Meaning | Action |
|---|---|---|
| `cnt < P` | Fewer painters needed than available | We can **decrease** time → `hi = mid - 1` |
| `cnt == P` | Exact fit | `ans = mid`, `hi = mid - 1` (try for even less time) |
| `cnt > P` | More painters needed than available | We must **increase** time → `lo = mid + 1` |

### Trace: A[] = {5, 3, 6, 1, 9}, T = 2 min, P = 2 painters

```
lo = 18 (max element 9 * T=2), hi = 48 (sum=24 * T=2)
```

| lo | hi | mid | Min painters for `mid` time | Action |
|---|---|---|---|---|
| 18 | 48 | 33 | 2 | `ans = 33`, `hi = 32` |
| 18 | 32 | 25 | 3 → too many, decrease painters → increase time | `lo = 26` |
| 26 | 32 | 29 | 2 | `ans = 29`, `hi = 28` |
| 26 | 28 | 27 | 3 → increase time | `lo = 28` |
| 28 | 28 | 28 | 2 | `ans = 28`, `hi = 27` |
| 28 | 27 | — | — | search space exhausted → **stop** |

**Final answer: 28 minutes** (matches the brute-force result above)

### Code

```java
int minTime(int[] A, int T, int P) {
    int lo = maxEle * T;              // TC: O(N) to compute
    int hi = sumOfAllElements * T;    // TC: O(N) to compute
    int ans = -1;

    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        int painters = minPainters(A, T, mid);

        if (painters <= P) {
            ans = mid;
            hi = mid - 1;
        } else {
            lo = mid + 1;
        }
    }
    return ans;
}
```

- **TC:** O(log(search space) × N)  — computing `lo`/`hi` is O(N); each binary search step calls `minPainters` which is O(N)
- **SC:** O(1)

---

## Aggressive Cows

A farmer has built a barrier with `N` stalls.

- `A[i]` → location of the i-th stall, given in **increasing order**
- `M` → number of cows the farmer has, `2 ≤ M < N`
- Cows are aggressive towards each other.

**Goal:** Assign the cows to stalls such that the **minimum distance between any two cows is maximized**.

### Example 1

```
A = {1, 4, 8, 10}   cows (M) = 3
```

```
1        4        8       10
C1---3---C2---4---C3            → min gap = 3
C1-------7-------C2---2---C3    → min gap = 2
   C1---4---C2---2---C3         → min gap = 2
C1---3---C2------6------C3      → min gap = 3   ✅ best possible

Maximum possible minimum distance = 3 units
```

### Example 2

```
A = {0, 3, 4, 7, 9, 10}   K (cows) = 4
```

```
0    3   4       7       9   10
C1--3--C2-1-C3--3--C4              → min gap = 1
C1----4----C2--3--C3-2-C4          → min gap = 2
C1----4----C2-----5-----C3-1-C4    → min gap = 1

Ans = 3
```

### Example 3

```
A = {1, 2, 4, 8, 9}   C = 3
```

| Placement | Distances | Min gap |
|---|---|---|
| D = 1 | C1—1—C2—2—C3 | 1 |
| D = 2 | C1—3—C2—4—C3 | 2 |
| D = 3 | C1—3—C2—4—C3 | 3 ✅ |
| D = 4 | C1—7—C2—1—C3 | 1 |

**Ans = 3**

### Binary Search on the Answer — Setup

```
Target = maximum value of D (min distance between any two cows)

Search Space:
  lo (Min distance) = minimum of all differences between two adjacent stalls
  hi (Max distance)  = A[N-1] - A[0]
```

```
[ lo ]-----------[ mid ]-----------[ hi ]
```

For a candidate `mid` distance, check if it's possible to place all the cows **at least `mid` distance apart**.

| Result | Action |
|---|---|
| Yes | `ans = mid`, try for a larger distance → `lo = mid + 1` |
| No | Decrease the distance → `hi = mid - 1` |

### Trace: A = {2, 6, 11, 14, 19, 25, 30, 39, 43}, C = 4

```
lo = 3 (min adjacent gap), hi = 41 (43 - 2)
```

| lo | hi | mid | Can we place 4 cows ≥ mid apart? | Action |
|---|---|---|---|---|
| 3 | 41 | 22 | No | `hi = 21` |
| 3 | 21 | 12 | Yes | `ans = 12`, `lo = 13` |
| 13 | 21 | 17 | No | `hi = 16` |
| 13 | 16 | 14 | No | `hi = 13` |
| 13 | 13 | 13 | No | `hi = 12` |
| 13 | 12 | — | — | search space exhausted |

**Final answer: 12**

### Feasibility check — `check()`

```java
boolean check(int[] A, int dis, int C) {
    int cows = 1;
    int lastPos = A[0];

    for (int i = 1; i < N; i++) {
        if (A[i] >= lastPos + dis) {
            cows++;
            lastPos = A[i];
            if (cows == C) return true;
        }
    }
    return false;
}
```

- **TC:** O(N)
- **SC:** O(1)

### Main binary search code

```java
int lo = /* find minimum of all adjacent element differences */;
int hi = A[N - 1] - A[0];
int ans = -1;

while (lo <= hi) {
    int mid = lo + (hi - lo) / 2;

    if (check(A, mid, C) == true) {
        ans = mid;
        lo = mid + 1;
    } else {
        hi = mid - 1;
    }
}
```

- **TC:** O(log(search space) × N)
- **SC:** O(1)

---

## Recognizing "Binary Search on Answer" problems

These types of problems generally have the following characteristics:

- There are two or three parameters & constraints.
- The requirement is to **maximize or minimize** a given parameter.
- You see phrases like:
  - *Minimize the maximum*
  - *Maximize the minimum*
  - *Smallest X such that...*
  - *Largest X such that...*
  - *Can we achieve X?*
- The problem should be **monotonic** in nature — i.e., after one point it's no longer feasible to solve (or vice versa), which is what makes binary search valid on the answer space.
