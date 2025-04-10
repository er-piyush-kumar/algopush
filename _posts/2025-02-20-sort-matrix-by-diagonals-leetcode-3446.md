---
layout: post
title: Sort Matrix by Diagonals - Leetcode 3446
author: Piyush
description: Learn how to solve Leetcode 3446 - Sort Matrix by Diagonals. We explore both brute-force and optimized hash map approaches with C++ code and multiple examples.
author: Piyush
date: 2025-02-20 10:30:00 +0530
categories: [Leetcode, Matrix, Leetcode Question of the Day, Programming Question]
tags: [leetcode, matrix, sorting, hash-map, c++, algorithms]
toc: true
toc_label: "Table of Contents"
toc_sticky: true


---
Sorting a matrix by its diagonals is an interesting problem that tests your understanding of data grouping and sorting. In this post, we will solve **[Leetcode 3446 - Sort the Matrix by Diagonals](https://leetcode.com/problems/sort-matrix-by-diagonals/description/)**, using both brute force and optimized approaches in **C++, Python and Java**.

---

# **🧠 Problem Statement**

You are given a **square matrix `grid` of size `n x n`**. You need to **sort all the diagonals** in the following manner:

- If a diagonal lies in the **bottom-left triangle** (including the main diagonal), sort it in **non-increasing** order.
- If a diagonal lies in the **top-right triangle**, sort it in **non-decreasing** order.

---

### 🔍 Example 1

**Input:**
```
grid = [[1, 7, 3],
        [9, 8, 2],
        [4, 5, 6]]
```

**Output:**
```
grid = [[8, 2, 3],
        [9, 6, 7], 
        [4, 5, 1]]
```
**Explanation:**

![image.png](https://assets.leetcode.com/uploads/2024/12/29/4052example1drawio.png)

The diagonals with a black arrow (bottom-left triangle) should be sorted in non-increasing order:

- `[1, 8, 6]`becomes` [8, 6, 1]`.
- `[9, 5]` and `[4]`remain unchanged.

The diagonals with a blue arrow (top-right triangle) should be sorted in non-decreasing order:

- `[7, 2]` becomes`[2, 7]`.
- `[3]` remains unchanged.


### Example 2:

**Input:** 
```
grid = [[0,1],
        [1,2]]
```

**Output:** 
```
grid = [[2,1],
        [1,0]]
```

**Explanation:**

![image.png](https://assets.leetcode.com/uploads/2024/12/29/4052example2adrawio.png)

The diagonals with a black arrow must be non-increasing, so `[0, 2]` is changed to `[2, 0]`. The other diagonals are already in the correct order.

### Example 3:

**Input:**
```
grid = [[1]]
```

**Output:** 
```
grid = [[1]]
```

**Explanation:**

Diagonals with exactly one element are already in order, so no changes are needed.

---

**Constraints:**

- `grid.length == grid[i].length == n`
- `1 <= n <= 10`
- `105 <= grid[i][j] <= 105`

---

## 💡**Approach 1: Brute Force**

This approach works, but it's inefficient for large matrices.

### **Steps:**
  - [x] Extract all diagonals one by one.
  - [x] Sort diagonals based on their position.
  - [x] Reinsert sorted values into the matrix.


### **✅ C++ Solution**

```cpp
#include <vector>
#include <algorithm>

using namespace std;

class Solution {
public:
    vector<vector<int>> sortMatrix(vector<vector<int>>& grid) {
        int n = grid.size();

        for (int d = -(n - 1); d <= (n - 1); d++) {
            vector<int> diag;

            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (i - j == d) {
                        diag.push_back(grid[i][j]);
                    }
                }
            }

            if (d >= 0)
                sort(diag.begin(), diag.end(), greater<int>());
            else
                sort(diag.begin(), diag.end());

            int index = 0;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (i - j == d) {
                        grid[i][j] = diag[index++];
                    }
                }
            }
        }

        return grid;
    }
};
```

### **✅ Python Solution**

```python
class Solution:
    def sortMatrix(self, grid):
        n = len(grid)

        for d in range(-(n - 1), n):
            diag = []

            for i in range(n):
                for j in range(n):
                    if i - j == d:
                        diag.append(grid[i][j])

            if d >= 0:
                diag.sort(reverse=True)
            else:
                diag.sort()

            index = 0
            for i in range(n):
                for j in range(n):
                    if i - j == d:
                        grid[i][j] = diag[index]
                        index += 1

        return grid
```

### **✅ Java Solution**

```java
import java.util.*;

public class Solution {
    public int[][] sortMatrix(int[][] grid) {
        int n = grid.length;

        for (int d = -(n - 1); d <= (n - 1); d++) {
            List<Integer> diag = new ArrayList<>();

            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (i - j == d) {
                        diag.add(grid[i][j]);
                    }
                }
            }

            if (d >= 0) {
                diag.sort(Collections.reverseOrder());
            } else {
                Collections.sort(diag);
            }

            int index = 0;
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    if (i - j == d) {
                        grid[i][j] = diag.get(index++);
                    }
                }
            }
        }

        return grid;
    }
}
```

### **❌ Drawbacks:**
- Time-consuming due to repeated traversals.
- Requires extra space and sorting overhead.

**Time Complexity:** `O(n² log n)`  
**Space Complexity:** `O(n²)`

---

## **🚀 Optimized Approach: Using Hash Map**

We can group elements diagonally using `i - j` as a unique key. This value remains constant for all elements in the same diagonal.

### **Algorithm:**
1. Use `unordered_map<int, vector<int>>` where the key is `i - j`.
2. Traverse the matrix and populate the map.
3. Sort each vector in the map:
   - Descending if `i - j >= 0`
   - Ascending otherwise
4. Traverse the matrix again to place the sorted values back.

### **✅ C++ Solution**
```cpp
#include <vector>
#include <algorithm>
#include <unordered_map>

using namespace std;

class Solution
{
public:
    vector<vector<int>> sortMatrix(vector<vector<int>> &grid)
    {
        int n = grid.size();
        unordered_map<int, vector<int>> diagMap;

        // Collect all diagonals
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++)
            {
                diagMap[i - j].push_back(grid[i][j]);
            }
        }

        // Sort diagonals based on their positions
        for (auto &[key, values] : diagMap)
        {
            if (key >= 0)
                sort(values.begin(), values.end(), greater<int>()); // Descending
            else
                sort(values.begin(), values.end()); // Ascending
        }

        // Fill sorted values back into the matrix
        for (int i = 0; i < n; i++)
        {
            for (int j = 0; j < n; j++)
            {
                grid[i][j] = diagMap[i - j].back();
                diagMap[i - j].pop_back();
            }
        }

        return grid;
    }
};
```
### **✅ Python Solution**
```python
class Solution:
    def sortMatrix(self, grid):
        n = len(grid)
        diag_map = {}

        # Collect all diagonals
        for i in range(n):
            for j in range(n):
                diag_map.setdefault(i - j, []).append(grid[i][j])

        # Sort diagonals based on their positions
        for key, values in diag_map.items():
            if key >= 0:
                values.sort(reverse=True)  # Descending
            else:
                values.sort()  # Ascending

        # Fill sorted values back into the matrix
        for i in range(n):
            for j in range(n):
                grid[i][j] = diag_map[i - j].pop()

        return grid

```

### **✅ Java Solution**
```java
import java.util.*;

public class Solution {
    public int[][] sortMatrix(int[][] grid) {
        int n = grid.length;
        Map<Integer, Deque<Integer>> diagMap = new HashMap<>();

        // Collect all diagonals
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                diagMap.computeIfAbsent(i - j, k -> new ArrayList<>()).add(grid[i][j]);
            }
        }

        // Sort diagonals based on their positions
        for (Map.Entry<Integer, List<Integer>> entry : diagMap.entrySet()) {
            List<Integer> values = entry.getValue();
            if (entry.getKey() >= 0) {
                values.sort(Collections.reverseOrder()); // Descending
            } else {
                Collections.sort(values); // Ascending
            }
            diagMap.put(entry.getKey(), new ArrayDeque<>(values));
        }

        // Fill sorted values back into the matrix
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                grid[i][j] = diagMap.get(i - j).removeLast();
            }
        }

        return grid;
    }
}
```

-**Time Complexity:**
- `Collecting diagonals: O(n²)`
- `Sorting diagonals: O(n log n)`
- `Reconstructing matrix: O(n²)`

Total Complexity: *`O(n² log n)`, but it reduces redundant computations.*

**Space Complexity:** `O(n²)`

---

- *We explored two solutions: a simple brute force approach and an optimized hash map approach.*
- *The optimized method reduces unnecessary operations and sorts diagonals efficiently.*
- *Understanding how to identify diagonals using (i - j) is the key to solving this problem effectively.*

---

### FAQs
**1. Can we solve this problem in `O(n)` time?**
Not exactly, since sorting takes `O(n log n)`. However, using bucket sort (if constraints allow) could make sorting nearly `O(n)`.

**2. Why did we use an `unordered_map`?**
It helps in efficiently storing and retrieving diagonal elements using `(i - j)` as a unique key.



