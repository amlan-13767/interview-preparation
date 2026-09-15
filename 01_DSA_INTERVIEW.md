# DSA --- Software Engineer Interview Notes

> Goal: be able to **solve, explain, optimize and defend** common
> interview problems, not merely recite definitions.

## 1. Complexity Analysis

### Asymptotic notation

-   **O(f(n))** --- upper bound / growth ceiling.
-   **Ω(f(n))** --- lower bound.
-   **Θ(f(n))** --- tight asymptotic bound.

Typical growth:

``` text
O(1) < O(log n) < O(n) < O(n log n) < O(n²) < O(n³) < O(2ⁿ) < O(n!)
```

### Common examples

  Pattern                       Complexity
  ------------------------- --------------
  Array index access                  O(1)
  One loop over n                     O(n)
  Nested n × n loops                 O(n²)
  Binary search                   O(log n)
  Merge sort                    O(n log n)
  Hash lookup                 O(1) average
  BFS/DFS                           O(V+E)
  Heap insertion/deletion         O(log n)

### Amortized complexity

A dynamic array append can occasionally be O(n) when capacity grows, but
over many appends the **amortized** cost is O(1).

### Space complexity

Count additional memory. Usually distinguish:

-   input space
-   auxiliary space
-   recursion stack

------------------------------------------------------------------------

# 2. Arrays

Arrays store elements in contiguous memory.

``` text
index:  0   1   2   3
       [10][20][30][40]
```

Address of `arr[i]` is computed from base address and index, so random
access is O(1).

### Operations

  Operation                         Typical complexity
  ------------------------------- --------------------
  Access                                          O(1)
  Linear search                                   O(n)
  Binary search on sorted array               O(log n)
  Insert at beginning                             O(n)
  Delete from beginning                           O(n)
  Append to dynamic array               O(1) amortized

## High-value array patterns

### Two pointers

Use when:

-   array is sorted
-   looking for a pair
-   reversing
-   partitioning
-   removing duplicates

``` cpp
int l = 0, r = n - 1;
while (l < r) {
    long long sum = a[l] + a[r];
    if (sum == target) break;
    if (sum < target) l++;
    else r--;
}
```

### Sliding window

Use for contiguous subarray/substring constraints.

Fixed window:

``` cpp
long long sum = 0;
for (int i = 0; i < k; ++i) sum += a[i];

long long best = sum;
for (int i = k; i < n; ++i) {
    sum += a[i] - a[i-k];
    best = max(best, sum);
}
```

Variable window is useful for conditions such as "longest substring with
at most k distinct characters."

### Prefix sum

``` text
prefix[i] = a[0] + ... + a[i]
```

Range sum:

``` text
sum(l,r) = prefix[r] - prefix[l-1]
```

### Difference array

Useful when many range updates are required.

### Kadane's algorithm

Maximum subarray sum:

``` cpp
long long cur = a[0], best = a[0];
for (int i = 1; i < n; ++i) {
    cur = max((long long)a[i], cur + a[i]);
    best = max(best, cur);
}
```

O(n) time, O(1) extra space.

------------------------------------------------------------------------

# 3. Strings

Common interview tasks:

-   palindrome
-   anagram
-   frequency counting
-   longest substring
-   pattern matching
-   character replacement
-   duplicate removal

### Palindrome

Two pointers from both ends.

### Anagram

Compare character frequencies or sort both strings.

-   frequency approach: O(n)
-   sorting: O(n log n)

### Important C++ string knowledge

Know:

``` cpp
s.size()
s[i]
s.substr(pos, len)
s.find(...)
stoi(...)
to_string(...)
```

Be aware that repeated string concatenation can become expensive
depending on the operation/pattern.

------------------------------------------------------------------------

# 4. Hashing

Hash tables map keys to buckets.

C++:

``` cpp
unordered_map<int,int> mp;
unordered_set<int> st;
```

Average lookup/insert/delete: O(1).

Worst case can degrade toward O(n).

### HashMap vs HashSet

-   `unordered_map<K,V>` stores key-value pairs.
-   `unordered_set<K>` stores unique keys.

### Classic: Two Sum

For each `x`, search for `target-x` in a hash map.

O(n) expected time, O(n) space.

### Frequency map

``` cpp
unordered_map<char,int> freq;
for(char c : s) freq[c]++;
```

### When hashing is the right choice

If the problem says:

-   "have we seen this before?"
-   "count occurrences"
-   "find pair/complement"
-   "first duplicate"
-   "frequency"

think **hashing**.

------------------------------------------------------------------------

# 5. Linked Lists

Node:

``` cpp
struct Node {
    int data;
    Node* next;
};
```

Unlike arrays, linked-list nodes do not require contiguous memory.

### Complexity

  Operation                                                   Complexity
  ------------------- --------------------------------------------------
  Access kth node                                                   O(n)
  Search                                                            O(n)
  Insert at head                                                    O(1)
  Delete known node     O(1) if predecessor/reference arrangement allows

### Fast/slow pointers

``` cpp
Node *slow = head, *fast = head;
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
```

Applications:

-   middle node
-   cycle detection
-   cycle entry
-   some palindrome problems

### Floyd cycle detection

If slow and fast meet, a cycle exists.

O(n) time, O(1) extra space.

### Reverse linked list

``` cpp
Node* prev = nullptr;
Node* cur = head;

while (cur) {
    Node* nxt = cur->next;
    cur->next = prev;
    prev = cur;
    cur = nxt;
}
return prev;
```

------------------------------------------------------------------------

# 6. Stack

LIFO --- Last In, First Out.

Operations:

-   push
-   pop
-   top

Usually O(1).

Applications:

-   parentheses
-   monotonic stack
-   recursion simulation
-   undo
-   expression evaluation
-   DFS

### Monotonic stack

Maintain increasing/decreasing order to solve:

-   next greater element
-   next smaller element
-   largest rectangle in histogram
-   stock span

Often O(n), because each element is pushed and popped at most once.

------------------------------------------------------------------------

# 7. Queue and Deque

Queue = FIFO.

Applications:

-   BFS
-   scheduling
-   buffering

Deque supports insertion/removal from both ends.

C++:

``` cpp
queue<int> q;
deque<int> dq;
```

------------------------------------------------------------------------

# 8. Binary Trees

Terms:

-   root
-   parent
-   child
-   leaf
-   depth
-   height
-   subtree

### Traversals

**Preorder**

``` text
Root → Left → Right
```

**Inorder**

``` text
Left → Root → Right
```

**Postorder**

``` text
Left → Right → Root
```

**Level order**

BFS with queue.

### Recursive traversal template

``` cpp
void inorder(Node* root) {
    if (!root) return;
    inorder(root->left);
    cout << root->data;
    inorder(root->right);
}
```

------------------------------------------------------------------------

# 9. Binary Search Tree

BST property:

``` text
all left values < root < all right values
```

Inorder traversal gives sorted order.

Balanced BST:

-   search \~ O(log n)
-   insertion \~ O(log n)
-   deletion \~ O(log n)

Skewed BST:

-   worst case O(n)

### Important question

**Why can a BST become O(n)?**

Because inserting sorted values can create a chain. Balanced trees such
as AVL/Red-Black trees maintain logarithmic height.

------------------------------------------------------------------------

# 10. Heap / Priority Queue

Heap is a complete binary tree.

Min heap:

``` text
parent <= children
```

Max heap:

``` text
parent >= children
```

Operations:

  Operation      Complexity
  ------------ ------------
  top                  O(1)
  push             O(log n)
  pop              O(log n)
  build heap           O(n)

Applications:

-   top K
-   kth largest/smallest
-   scheduling
-   Dijkstra
-   merge K sorted lists

C++:

``` cpp
priority_queue<int> maxHeap;

priority_queue<int, vector<int>, greater<int>> minHeap;
```

------------------------------------------------------------------------

# 11. Recursion

A recursive solution needs:

1.  Base case.
2.  Recursive case.
3.  Progress toward the base case.

When analyzing recursion, write the recurrence.

Example merge sort:

``` text
T(n) = 2T(n/2) + O(n)
```

=\> O(n log n).

------------------------------------------------------------------------

# 12. Backtracking

Pattern:

``` text
choose
explore
unchoose
```

Typical problems:

-   subsets
-   permutations
-   combinations
-   N-Queens
-   Sudoku
-   maze/path search

Template:

``` cpp
void backtrack(vector<int>& path) {
    if (/* complete */) {
        answer.push_back(path);
        return;
    }

    for (/* each choice */) {
        path.push_back(choice);
        backtrack(path);
        path.pop_back();
    }
}
```

------------------------------------------------------------------------

# 13. Sorting

## Bubble sort

Repeated adjacent swaps.

Worst O(n²).

## Selection sort

Repeatedly choose minimum.

O(n²).

## Insertion sort

Build sorted prefix.

-   best O(n)
-   worst O(n²)
-   useful for nearly sorted data

## Merge sort

Divide, recursively sort, merge.

-   O(n log n)
-   O(n) auxiliary space
-   stable

## Quick sort

Partition around pivot.

-   average O(n log n)
-   worst O(n²)
-   usually in-place apart from recursion
-   generally not stable

### Interview question: Merge vs Quick sort

Use merge sort when predictable O(n log n) and stability are important.
Quick sort can be very fast in practice due to locality and low
auxiliary memory, but pivot choice matters.

------------------------------------------------------------------------

# 14. Binary Search

Requires a monotonic/sorted search space.

Classic:

``` cpp
int l = 0, r = n - 1;
while (l <= r) {
    int mid = l + (r-l)/2;
    if (a[mid] == x) return mid;
    if (a[mid] < x) l = mid + 1;
    else r = mid - 1;
}
```

### Binary search on answer

Do not restrict binary search to arrays.

If a problem asks:

> minimum possible X such that condition(X) is feasible

define:

``` text
low = minimum possible answer
high = maximum possible answer
```

and binary-search the feasible region.

Common problems:

-   minimum shipping capacity
-   minimum eating speed
-   allocate books
-   aggressive cows
-   split array

------------------------------------------------------------------------

# 15. Greedy

Greedy chooses the best-looking local option.

Works when the problem has the appropriate greedy-choice property and
optimal substructure.

Examples:

-   activity selection
-   fractional knapsack
-   Huffman coding
-   interval scheduling

### Important distinction

0/1 Knapsack is not solved by the same simple greedy strategy that works
for fractional knapsack.

------------------------------------------------------------------------

# 16. Graphs

Graph:

``` text
G = (V,E)
```

Representations:

### Adjacency matrix

O(V²) memory.

Fast edge lookup.

### Adjacency list

O(V+E) memory.

Preferred for sparse graphs.

------------------------------------------------------------------------

# 17. BFS

Queue-based.

``` cpp
queue<int> q;
visited[src] = true;
q.push(src);

while (!q.empty()) {
    int u = q.front(); q.pop();
    for (int v : adj[u]) {
        if (!visited[v]) {
            visited[v] = true;
            q.push(v);
        }
    }
}
```

O(V+E).

For an unweighted graph, BFS gives shortest number-of-edges distance
from a source.

------------------------------------------------------------------------

# 18. DFS

Recursive or stack-based.

Applications:

-   connected components
-   cycle detection
-   topological sort
-   path exploration

O(V+E).

------------------------------------------------------------------------

# 19. Shortest Path

  Algorithm        Use
  ---------------- -----------------------------------------
  BFS              unweighted graph
  Dijkstra         non-negative weighted edges
  Bellman-Ford     negative edges; detects negative cycles
  Floyd-Warshall   all-pairs shortest path

Dijkstra with binary heap: approximately O((V+E) log V).

------------------------------------------------------------------------

# 20. Topological Sort

Only for DAGs.

Kahn's algorithm:

1.  calculate indegree
2.  push all indegree-0 vertices
3.  remove one
4.  decrease neighbors' indegree
5.  add newly zero-indegree vertices

If processed vertices \< V, graph contains a cycle.

------------------------------------------------------------------------

# 21. Dynamic Programming

DP requires:

-   overlapping subproblems
-   optimal substructure

Two styles:

### Memoization

Top-down recursion + cache.

### Tabulation

Bottom-up table.

### DP checklist

Ask:

1.  What is the state?
2.  What does `dp[i]` mean?
3.  What are the transitions?
4.  What is the base case?
5.  What is the iteration order?
6.  Can space be optimized?

### Common DP families

-   1D DP
-   2D/grid DP
-   knapsack
-   subsequence
-   partition
-   interval DP
-   tree DP
-   bitmask DP

------------------------------------------------------------------------

# 22. High-value coding patterns

``` text
Hashing → frequency / complement / duplicate
Two pointers → sorted pair / partition
Sliding window → contiguous range
Prefix sum → repeated range sum
Binary search → sorted/monotonic answer
Stack → nearest greater/smaller
Heap → top K / repeated min/max
BFS → unweighted shortest path / levels
DFS → exhaustive graph/tree exploration
Backtracking → enumerate valid choices
DP → repeated subproblems
```

------------------------------------------------------------------------

# 23. Most relevant interview Q&A

### Q1. Array vs linked list?

**Answer:** Arrays provide O(1) random access because elements are
indexed in contiguous storage. Linked lists provide easier
insertion/deletion at known positions but O(n) access and extra pointer
memory.

### Q2. Why is hash lookup O(1) average?

**Answer:** A hash function maps a key to a bucket, so the table can
directly locate the approximate storage position. Collisions require
additional handling, so worst-case performance can degrade.

### Q3. BFS vs DFS?

**Answer:** BFS explores level by level using a queue and is suitable
for shortest paths in unweighted graphs. DFS explores deeply using
recursion/stack and is useful for connectivity, cycle detection and
exhaustive exploration.

### Q4. Why can't Dijkstra handle negative edges safely?

**Answer:** Its greedy assumption is that once the smallest tentative
distance is finalized, it cannot later improve. A negative edge can
violate that assumption.

### Q5. When do you use DP?

**Answer:** When the problem can be expressed through repeated
subproblems and an optimal solution can be built from optimal
subsolutions.

### Q6. Why is merge sort O(n log n)?

**Answer:** There are log n levels of division and O(n) work to merge at
each level.

### Q7. How do you detect a linked-list cycle in O(1) space?

**Answer:** Floyd's slow/fast pointer algorithm.

### Q8. What is a monotonic stack?

**Answer:** A stack maintained in increasing or decreasing order. It
allows many nearest greater/smaller problems to be solved in O(n).

------------------------------------------------------------------------

# 24. Coding interview checklist

Before writing code:

-   clarify input constraints
-   ask whether array is sorted
-   identify duplicates/negative values
-   consider empty input
-   state brute force
-   optimize
-   state complexity
-   test edge cases


# 23. Additional high-value DSA topics

## Bit manipulation

Know:

```cpp
x & 1          // odd/even
x ^ x = 0
x ^ 0 = x
x << k         // approximately multiply by 2^k for suitable signed/unsigned cases
x >> k         // shift right
```

Common problems:

- single number using XOR
- power of two
- count set bits
- subset masks

### XOR trick

If every number appears twice except one:

```cpp
int ans = 0;
for (int x : a) ans ^= x;
```

All pairs cancel.

## Trie

A trie stores strings character-by-character.

Useful for:

- prefix search
- autocomplete
- dictionary matching
- word search

Typical operations are O(L), where L is key length, ignoring alphabet-factor details.

## Disjoint Set Union / Union-Find

Supports:

- `find(x)`
- `union(a,b)`

With path compression + union by rank/size, operations are effectively near-constant amortized time.

Applications:

- cycle detection in undirected graphs
- Kruskal's MST
- connected components
- dynamic connectivity

## Minimum Spanning Tree

### Kruskal

1. sort edges by weight
2. add edge if it joins two different components
3. use DSU

Complexity dominated by sorting: O(E log E).

### Prim

Grow a tree from a starting vertex using a minimum-priority queue.

## Segment Tree

Useful for range queries + updates.

Typical:

- range sum
- range minimum
- range maximum

Build: O(n)

Query/update: O(log n)

Space: O(n).

## Fenwick Tree / BIT

Supports prefix sums and point updates in O(log n) with O(n) memory.

## Topological sorting

Use for dependency ordering. If Kahn's algorithm cannot process all vertices, the directed graph has a cycle.

## Common complexity traps

- `unordered_map` is average O(1), not guaranteed O(1).
- BST is O(log n) only when height is logarithmic.
- Heap is not fully sorted; only root has min/max guarantee.
- Binary search requires a monotonic/sorted search condition.
- BFS shortest path guarantee assumes unweighted/equal edge cost unless using an appropriate weighted variant.
