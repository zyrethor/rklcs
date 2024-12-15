# Recursion Complexity

計算遞迴的複雜度是一個分析遞迴函數運行時間和空間的過程，通常使用**遞歸關係式**和**主定理**來進行分析。以下是具體步驟：

------

### 1. **理解遞歸函數的結構**

先明確遞歸函數的基本結構，包括：

- 基本情況（Base Case）的時間複雜度
- 遞歸情況如何分解問題，以及每次分解需要的額外操作時間。

例如，考慮經典的遞歸函數 `T(n)`：

```python
def rec_function(n):
    if n <= 1:
        return 1  # Base case, O(1)
    else:
        return rec_function(n // 2) + n  # Recursive case
```

### 2. **建立遞歸關係式**

根據遞歸函數，建立其運行時間的遞歸關係式。

- 基本情況：`T(1) = O(1)`。
- 遞歸情況：根據分解次數和額外操作時間，推導出關係式。

對上述函數，遞歸關係式是： $T(n) = T(n/2) + O(n)$

------

### 3. **解遞歸關係式**

有三種常用的方法來解遞歸關係式：

#### 方法 1: 主定理（Master Theorem）

主定理適用於以下形式的遞歸關係式： $T(n) = aT\left(\frac{n}{b}\right) + O(n^d$) 其中：

- $a$: 遞歸子問題的數量
- $b$: 問題縮小的比例
- $d$: 每層的非遞歸工作量的指數

比較 $\log⁡_b a$ 和 $d$：

1. 如果 $\log_b a < d$，則 $T(n) = O(n^d)$
2. 如果 $\log_b a = d$，則 $T(n) = O(n^d \log n)$
3. 如果 $\log_b a > d$，則 $T(n) = O(n^{\log_b a})$

對於例子 $T(n) = T(n/2) + n$：

- $a = 1$, $b = 2$, $d = 1$
- $\log_2 1 = 0$, 比較 $0 < 1$

因此，時間複雜度為 $O(n)$。

#### 方法 2: 展開法（Substitution Method）

將遞歸式展開多次，直到基準情況。 例如，展開 $T(n) = T(n/2) + n$：

$$T(n) = T(n/2) + n      = T(n/4) + n/2 + n      = T(n/8) + n/4 + n/2 + n$$

觀察模式，總計為：

$$T(n) = n + n/2 + n/4 + \dots + 1$$

這是等比級數，總和為 $O(n)$。

#### 方法 3: 替代法（Substitution and Guessing）

猜測一個解 $T(n) = O(f(n))$，然後用數學歸納法證明。 例如，猜測 $T(n) = O(n)$，將其代入遞歸式驗證即可。

------

### 4. **分析空間複雜度**

空間複雜度主要取決於遞歸調用棧的深度：

- 如果每次遞歸縮小問題的大小為 $n/b$，則調用棧的深度為 $O(\log_b n)$。
- 如果每次遞歸不縮小問題，則調用棧的深度為 $O(n)$。

------

### 綜合實例分析

#### 範例 1: 二分搜尋

```python
def binary_search(arr, low, high, target):
    if low > high:
        return -1
    mid = (low + high) // 2
    if arr[mid] == target:
        return mid
    elif arr[mid] > target:
        return binary_search(arr, low, mid - 1, target)
    else:
        return binary_search(arr, mid + 1, high, target)
```

- 遞歸關係式：$T(n) = T(n/2) + O(1)$
- 用主定理：$a = 1$, $b = 2$, $d = 0$，比較 $\log_2 1 = 0 = d$ 複雜度：$O(\log n)$
- 空間複雜度：調用棧深度為 $O(\log n)$。

#### 範例 2: 合併排序

```python
def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)
```

- 遞歸關係式：$T(n) = 2T(n/2) + O(n)$
- 用主定理：$a = 2$, $b = 2$, $d = 1$，比較 $\log_2 2 = 1 = d$ 複雜度：$O(n \log n)$
- 空間複雜度：調用棧深度為 $O(\log n)$。

------

### 總結

分析遞迴複雜度的關鍵是：

1. 寫出遞歸關係式。
2. 使用主定理、展開法或替代法解決時間複雜度。
3. 確定調用棧深度以計算空間複雜度。