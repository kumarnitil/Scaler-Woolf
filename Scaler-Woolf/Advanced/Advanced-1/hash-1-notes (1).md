# HashMap — Introduction

## Today's Agenda
1. HashMap Introduction
2. Frequency of each query
3. Count distinct elements
4. Check if pair sum = k
5. Count pair sum = k

---

## Motivating Example: Hotel Room Booking

**5 rooms — manual register:**

| Room No. | Occupied |
|---|---|
| 1 | ✔ |
| 2 | ✗ |
| 3 | ✔ |
| 4 | ✔ |
| 5 | ✗ |

**Scaling to 100 rooms:**

```
Room no. → {1 ... 100}
boolean[] check = new boolean[101]

True  → room is occupied
False → room is free
```

To check if a particular room is available → **O(1)**

### The problem: sparse / huge key ranges

The hotel is hit by a pandemic — footfall drastically reduces. The manager, in a strange turn, consults a numerologist, who assigns each room a number from the range `{1 to 10^9}` — a one-to-one mapping.

```
boolean[] check = new boolean[10^9 + 1]
```

To store info for just 100 actual rooms, we'd need memory of size `10^9 + 1` → **a lot of memory wastage**.

### Issues with a plain array/boolean-array approach
1. Lot of memory wastage.
2. Can't create an array of size > 10⁶ in practice.

### The solution: HashMap

> A **HashMap** is a data structure which holds information in the form of a **key-value pair**.

```
HashMap<Key, Value>
        ↓            ↓
   Room no.      True/False
```

```
HM = {
  1   → True
  10  → false
  100 → false
  ...
  10^9 → false
}
```

Max size of the HM is now only what's actually needed (e.g., 100 entries) — not the whole key range.

```java
if (hm.get(1) == false)
    // 1st room is available & can be given to customer
```
- **TC:** O(1)

### Properties of HashMap
1. Keys must be unique.
2. Values can be anything.
3. One `null` key is allowed.
4. HashMap doesn't maintain the order of insertion.

---

## More Motivating Examples (Scaler-style)

**1. Population of every country**

| Country | Population |
|---|---|
| 🇮🇳 India | 145 |
| 🇺🇸 USA | 65 |
| 🇷🇺 Russia | 14 |
| 🇨🇳 China | 147 |
| 🇬🇧 United Kingdom | 80 |

```
HashMap<Key, Value>
   Country Name (String) → Population (Long)
```

**2. Number of states of every country**

| Country | No. of States |
|---|---|
| 🇮🇳 India | 29 |
| 🇺🇸 USA | 50 |
| 🇨🇳 China | 25 |
| 🇷🇺 Russia | 21 |

```java
HashMap<String, Integer> hm = new HashMap<>();
// Key: Country Name, Value: No. of states
```

**3. Name of all the states of every country**

| Country | States |
|---|---|
| 🇮🇳 India | Andhra Pradesh, Arunachal Pradesh, U.P, M.P, Karnataka, ... |
| 🇺🇸 USA | New York, Washington, Texas, ... |
| 🇷🇺 Russia | Moscow, Kazan, Samara, ... |
| 🇨🇳 China | X, Y, Z, ... |

```java
HashMap<String, List<String>> hm = new HashMap<>();
// Key: Country Name, Value: List of state names
```

**4. Population of each state in each country**

```
🇮🇳 India:  Karnataka → 50, Maharashtra → 72, Himachal Pradesh → 36
🇺🇸 USA:    Texas → 18, Florida → 16, Washington → 32
🇷🇺 Russia: Kazan → 3, Samara → 5, Moscow → 7
```

This is a **nested** HashMap — for every country, we need another HashMap mapping state name → population:

```java
HashMap<String, HashMap<String, Long>> hm = new HashMap<>();
// Outer Key: Country Name
// Outer Value: HashMap<State Name, Population>
```

### HashSet

> Used **to store unique keys** (no associated value).

```java
HashSet<KeyType> hs = new HashSet<>();
```

> **Both HashMap and HashSet have every operation running at constant O(1).**

---

## HashMap Operations

```java
HashMap<KeyType, ValueType> hm = new HashMap<>();
```

| Operation | Effect |
|---|---|
| `hm.size()` | No. of keys in HM |
| `hm.put(key, value)` | Insert key with value |
| `hm.containsKey(key)` | Search if key is present & return true/false |
| `hm.get(key)` | Get value of key |
| `hm.remove(key)` | Remove entire entry from HM |
| `hm.put(key, newValue)` | Update — put again with a new value |
| `hm.keySet()` | Iterate on HM & get keys — O(N) |
| `hm.valueSet()` | Iterate on HM & get values — O(N) |

## HashSet Operations

```java
HashSet<KeyType> hs = new HashSet<>();
```

| Operation | Effect |
|---|---|
| `hs.size()` | To get HashSet size |
| `hs.add(key)` | To add key in hs |
| `hs.contains(key)` | To search key in hs |
| `hs.remove(key)` | To remove a key |
| `hs.keySet()` | To iterate on all keys — O(N) |

---

## Problem: Frequency of Each Query

**Problem Statement:** Given N elements & Q queries, find the frequency of elements provided in the query.

```
N = 11
arr = [2, 6, 3, 8, 2, 8, 2, 3, 8, 10, 6]     (1 ≤ N ≤ 10^6)
Q = 4                                          (1 ≤ Q ≤ 10^5)
```

**Queries and answers:**

| Query | Answer |
|---|---|
| 2 | 3 |
| 8 | 3 |
| 3 | 2 |
| 5 | 0 |

### Brute Force Idea
For every query, iterate on the array & get its count.
- **TC:** O(Q * N)
- **SC:** O(1)

### Idea 2 — Use HashMap

Build frequency of every element using a HashMap:
```
Key   → Distinct element
Value → Frequency
```

```
arr = [2, 6, 3, 8, 2, 8, 2, 3, 8, 10, 6]

HM = {
  2  → 3
  6  → 6
  3  → 2
  8  → 3
  10 → 1
}
```

**Queries:**
```
hm.get(2) ⇒ 3
hm.get(8) ⇒ 3
hm.get(5) ⇒ Null Error (5 not in the map)
```

> Always guard with `if (hm.containsKey(key))` before calling `get`, to avoid a null/lookup error on a key that was never inserted.

### Steps
1. Iterate on array & build HashMap.
2. If an element is already present in HM → `hm.get(key) + 1`.
   Else → add element with `freq = 1`.

### Code

```java
public static void Queryfreq(int[] A, int[] Q) {
    HashMap<Integer, Integer> hm = new HashMap<>();

    for (int i = 0; i < A.length; i++) {
        int ele = A[i];
        if (hm.containsKey(ele) == true) {
            int of = hm.get(ele);
            hm.put(ele, of + 1);
        } else {
            hm.put(ele, 1);
        }
    }

    for (int i = 0; i < Q.length; i++) {
        int Queryele = Q[i];
        if (hm.containsKey(Queryele)) {
            System.out.println(hm.get(Queryele));
        } else {
            System.out.println(0);
        }
    }
}
```
- **TC:** O(N + Q)
- **SC:** O(N)

### Cleaner version using `getOrDefault`

```java
// hm.getOrDefault(ele, 0)
// If ele is present → hm.get(ele)
// else → 0

hm.put(ele, hm.getOrDefault(ele, 0) + 1);
```

---

## Problem: Count of Distinct Elements

```
N = 5   [3, 5, 6, 5, 4]  → Ans = 4
N = 3   [3, 3, 3]        → Ans = 1
N = 5   [1, 1, 1, 2, 2]  → Ans = 2
```

**Idea:** Insert all elements in a HashSet — a HashSet has the unique property of storing only distinct elements.

```java
HashSet<Integer> hs = new HashSet<>();
for (int ele : A) {
    hs.add(ele);
}
return hs.size();
```
- **TC:** O(N)
- **SC:** O(N)

---

## Problem: Check if Pair Sum = K

**Problem Statement:** Given an `A[N]` and `K`. Check if there exists a pair `(i, j)` such that `A[i] + A[j] = K && i != j`.

```
arr = [8, 9, 1, -2, 4, 5, 11, -6, 4]

K = 6  → A[0] + A[3] → True
K = 22 → False
K = 8  → A[4] + A[8] → True
```

**Quiz:**
```
arr = [3, 5, 1, 2, 1, 2]
K = 7  → A[1] + A[3] → True
K = 10 → False
```

### Brute Force Idea
Get all unique pairs & check if their sum == K.

```
A[i] + A[j] = K
    x     y
y = K - x

For every A[i], search if K - A[i] is present.
```

### Code (brute force)

```java
for (int i = 0; i < n; i++) {
    int x = A[i];
    int y = K - x;
    for (int j = i + 1; j < n; j++) {
        if (A[j] == y) return true;
    }
}
return false;
```
- **TC:** O(N²)
- **SC:** O(1)

### Idea 2 — Use HashSet (has an issue)

Insert all elements in a HashSet & then search for `x` & `y`.

```
A[] = {8, 9, 2, -2, 4, 5, 11}   K = 9

x = 8, 9, 2, -2
y = 1, 0, 7, 11

hs = {8, 9, 2, -2, 4, 5, 11}

For x=8, y=1 → hs doesn't contain 1
For x=-2, y=11 → hs.contains(11) → True → return true
```

**Issue — false positive when K = 2 * x:**

```
A[] = {8, 9, 2, -2, 4, 5, 11}   K = 16

x = 8
y = 16 - 8 = 8
if (hs.contains(8)) → True   ✗ (WRONG — this is matching the element with itself)
```

Since the HashSet only stores values (no counts), it can't tell whether the `8` found is a *different* array element or the *same* one — this breaks the `i != j` requirement.

### Idea 3 — Use HashMap<distinct ele, freq> (fixes the issue)

```
A[] = {8, 9, 2, -2, 9, 8}   K = 16

HM = {
  8  → 2
  9  → 2
  2  → 1
  -2 → 1
}
```

**Check if `y` is present in HM:**
```
if (x != y) → return true;
if (x == y) → if (hm.get(y) > 1) → return true;
```

This correctly handles the case where `x == y` — we only count it as a valid pair if that value appears **more than once** in the array (so there really are two distinct indices).

### Code

```java
HashMap<Integer, Integer> hm = new HashMap<>();
for (int ele : A) {
    hm.put(ele, hm.getOrDefault(ele, 0) + 1);
}

for (int i = 0; i < A.length; i++) {
    int x = A[i], y = K - x;
    if (hm.containsKey(y)) {
        if (x != y) return true;
        if (x == y) {
            if (hm.get(x) > 1) return true;
        }
    }
}
return false;
```
- **TC:** O(N + N) = O(N)
- **SC:** O(N)

---

## Next Class
- How to make a HashSet approach work correctly?
- Count pair sum = K (counting all such pairs, not just checking existence)
