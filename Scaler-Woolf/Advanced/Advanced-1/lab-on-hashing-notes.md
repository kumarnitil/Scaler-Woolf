# Lab Session on Hashing

## Content
1. Longest substring without repeat
2. First non-repeating element
3. Check subarray with sum = 0
4. Check subarray with sum = k
5. Longest consecutive sequence

**Flow for each problem:** Problem statement → Time to implement code → Teach that particular question.

---

## Problem 1: Longest Substring Without Repeat

**Problem Statement:** Given a string `str`, find the longest substring without a repeating character.

```
str = "abcbdeab"  → Ans = 5   (e.g. "cbdea")
str = "bbb"        → Ans = 1
str = "pwwkew"      → Ans = 3   (e.g. "kew")
```

### Brute Force
1. Generate every possible substring of the string.
2. For a particular substring, check if it has all unique characters or not.

```java
ans = 0;
for (i = 0 → n) {
    for (j = i → n) {
        // substr = [i, j]
        HashSet<Character> hs;
        for (k = i → j) {
            hs.add(str.charAt(k));
        }
        if (hs.size() == j - i + 1) ans = Math.max(ans, j - i + 1);
    }
}
```
- **TC:** O(n³)
- **SC:** O(n)

### Optimal Solution — Sliding Window

**Steps:**
- Initialize two pointers `start` and `end` to represent the current window.
- Use a hash set to keep track of characters in the current window.
- Move the `end` pointer to expand the window and include new characters.
- If a duplicate character is encountered, move the `start` pointer to reduce the window until the duplicate is removed.
- Update the maximum length of substrings without repeating characters as you expand the window.

```
str = "abcbdea"
       ↑      ↑
       i      j

Candidate windows: "e", "b", "c", "d", "a" → ans grows as: ∅, 1, 2, 3, 4, 5
```

### Code

```java
int i = 0, j = 0;
int ans = 0;
HashSet<Character> hs = new HashSet<>();

while (j < str.length()) {
    char ch = str.charAt(j);

    while (i < j && hs.contains(ch)) {
        hs.remove(str.charAt(i));
        i++;
    }

    hs.add(ch);

    if (j - i + 1 > ans) {
        ans = j - i + 1;
    }
    j++;
}
return ans;
```

### Variation — What if we want the maximum substring **itself** (not just its length)?

```java
int st = -1;
int i = 0, j = 0;
int ans = 0;
HashSet<Character> hs = new HashSet<>();

while (j < str.length()) {
    char ch = str.charAt(j);

    while (i < j && hs.contains(ch)) {
        hs.remove(str.charAt(i));
        i++;
    }

    hs.add(ch);

    if (j - i + 1 > ans) {
        st = i;
        ans = j - i + 1;
    }
    j++;
}

return str.substring(st, st + ans);
```

---

## Problem 2: First Non-Repeating Element

**Problem Statement:** Given N elements, find the first non-repeating element.

**Example 1:**
```
N = 6
[1, 2, 3, 1, 2, 5]
Output: ans = 3
```

**Example 2:**
```
N = 8
[4, 3, 3, 2, 5, 6, 4, 5]
Output: ans = 2
```

**Idea:** Build a HashMap for frequency, then search for the first non-repeating character.

### Code

```java
HashMap<Integer, Integer> hm = new HashMap<>();
for (int ele : A) {
    hm.put(ele, hm.getOrDefault(ele, 0) + 1);
}

for (int ele : A) {
    if (hm.get(ele) == 1) {
        return ele;
    }
}
return -1;   // no unique element
```
- **TC:** O(N)
- **SC:** O(N)

---

## Problem 3: Subarray with Sum = 0

**Problem Statement:** Given an array containing N elements, check if there exists a subarray with sum = 0.

```
A[] = {2, 2, 1, -3, 4, 3, 1, -2, -3, 2}   → true
```

### Brute Force
Generate all subarrays & then, for a particular subarray, iterate & get the sum of the subarray.
- **TC:** O(N³)
- **SC:** O(1)

### Optimal Idea — Prefix Sums

```
A[]    = {2, 2, 1, -3, 4, 3, 1, -2, -3, 2}
psum[] = {2, 4, 5,  2, 6, 9, 10, 8,  5, 7}
```

If a prefix sum value **repeats**, the elements between the last occurrence of that prefix sum and the current one contributed 0 to the total.

```
A[]    = {2, 1, -3, 4}
psum[] = {2, 3,  0, 4}
                 ↑
     if psum[i] == 0 → one subarray with sum = 0 (the prefix itself)
```

### Code

```java
int sum = 0;
HashSet<Integer> hs = new HashSet<>();
for (int i = 0; i < n; i++) {
    sum += A[i];
    if (sum == 0 || hs.contains(sum)) {
        return true;
    }
    hs.add(sum);
}
```
- **TC:** O(N)
- **SC:** O(N)

**OR (equivalent, by seeding the set with 0):**

```java
int sum = 0;
HashSet<Integer> hs = new HashSet<>();
hs.add(0);
for (int i = 0; i < n; i++) {
    sum += A[i];
    if (hs.contains(sum)) {
        return true;
    }
    hs.add(sum);
}
```
- **TC:** O(N)
- **SC:** O(N)

---

## Problem 4: Subarray with Sum K

**Problem Statement:** Given an array `arr[n]`, check if there exists a subarray with sum = K.

**Example:**
```
Index: 0 1 2 3  4 5 6 7 8
arr[]: 2 3 9 -4 1 5 6 2 5
```

Possible subarrays for the following values of K:
- k = 11: `{2, 3, 9, -4, 1}`, `{5, 6}`
- k = 10: `{2, 3, 9, -4}`
- k = 15: `{-4, 1, 5, 6, 2, 5}`

### Check example — sum = 110 in `A = [5, 10, 20, 100, 105]`

```
[<---- x - k ---->][<---- k ---->]
                                  sum = x
```
**Answer: No** such subarray exists.

**Idea:** For a particular running sum `= x`, we need to search if `(x - k)` was present previously. If it is there, this confirms there's a subarray with sum = k.

```
A[] = {2, 3, 9, -4, 1, -6, 6, 2, 5}   k = 6

running sum (x):  2  5  14  10  11   5  11
for (x - k):      -4 -1  8   4   5  -1  5   ← if hs.contains(5) → True
```

### Code

```java
int sum = 0;
HashSet<Integer> hs = new HashSet<>();
hs.add(0);
for (int i = 0; i < n; i++) {
    sum += A[i];
    if (hs.contains(sum - k)) {
        return true;
    }
    hs.add(sum);
}
```
- **TC:** O(N)
- **SC:** O(N)

---

## Problem 5: Longest Consecutive Sequence

**Problem Statement:** Given an array of length N, find the longest consecutive subsequence such that elements are consecutive integers.

**Example 1:**
```
A[] = {1, 2, 100, 101, 3, 7, 9}

1   → 1, 2, 3
7   → 7
9   → 9
100 → 100, 101

Ans = 3
```

**Example 2:**
```
A[] = {7, 9, 11, 13, 8, 6, 4}

4  → 4
6  → 6, 7, 8, 9
11 → 11
13 → 13

Ans = 4
```

### Approach
- The idea is to use Hashing.
- We first insert all elements in a Set.
- Then, traverse over all the elements and check if the current element can be a starting element of a consecutive subsequence.
- To check if the current element, say `X`, can be a starting element, check if `(X - 1)` is present in the set.
  - If `(X - 1)` is present in the set, then `X` cannot be the start of a consecutive subsequence.
  - Else if `(X - 1)` is not present, then start from `X` and keep on checking for elements `X + 1, X + 2, ...` to find the length of the consecutive subsequence.

### Code

```java
HashSet<Integer> hs = new HashSet<>();
for (int ele : A) hs.add(ele);

int ans = 0;
for (int ele : A) {
    if (hs.contains(ele - 1)) continue;   // not a starting element

    int end = ele;
    while (hs.contains(end)) {
        end = end + 1;
    }

    ans = Math.max(ans, end - ele);
}
```
- **TC:** O(N)
- **SC:** O(N)
