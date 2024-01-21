---
title: "N 数之和问题"
date: 2024-01-21T12:47:00+08:00
lastmod: 2024-01-21T21:58:43+08:00
categories: ["技术"]
tags: ["算法"]
summary: ""
---

## 算法思想

“N 数之和”问题是一个系列，题意是从数组中找出所有满足 `nums[a] + nums[b] + nums[c] + ... == target` 的 N 元组，要求 a、b、c 互不相同，并且 N 元组不能重复。

暴力的解法就是 N 层 for 循环，时间复杂度为 $O(N^k)$。优化的做法是用排序 + 双指针，时间复杂度为 $O(N^{k - 1})$。优化点在于：将最内层的两层 for 循环（查找最后两个数）转化为用双指针查找，将时间复杂度由 $O(N^2)$ 降至 $O(N)$。

双指针的步骤是：先将数组排序，当查找最后两个数时，在数组的两端分别放置左、右指针，判断当前 N 数之和 `sum` 与 `target` 的大小关系。
- 若 `sum < target`，将左指针右移
- 若 `sum > target`，将右指针左移
- 若 `sum == target`，记录这组数据，并将左指针右移、右指针左移。
- 重复以上过程，直至双指针重合。

## 去重逻辑

由于题目要求 N 元组不能重复，所以有去重逻辑。

以 3 数之和为例，假设三个数分别是 `nums[i]`、`nums[left]`、`nums[right]`：
- `nums[i]` 要去重：比如 `[-4, -1, -1, 0, 1, 2]`，`nums[i]` 会取两次 `-1`。去重条件是 `i > 0 and nums[i] == nums[i - 1]`（注意不要写成 `nums[i] == nums[i + 1]`，`[-1, -1, 2] 是有效的结果`）
- `nums[left]` 要去重：比如 `[-2,0,0,2,2]`，`nums[left]` 会取两次 `0`。去重条件是 `left > i + 1 and ls[left] == ls[left - 1]`。
- `nums[right]` 要去重：比如 `[-2,0,0,2,2]`，`nums[right]` 会取两次 `2`。去重条件是 `right < n - 1 and ls[right] == ls[right + 1]`。

## 相关题目

### [1. 两数之和](https://leetcode.cn/problems/two-sum/)

这题特殊一点，让返回下标，不是返回元素，所以不能排序，也就用不了双指针。  
正确的做法是用哈希表，将查找第二个数的时间复杂度由 $O(N)$ 优化至 $O(1)$。

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        dc = {}
        for i, x in enumerate(nums):
            if (target - x) in dc:
                return [i, dc[target - x]]
            else:
                dc[x] = i
        return [0, 0]
```

### [15. 三数之和](https://leetcode.cn/problems/3sum/)

```python
class Solution:
    def threeSum(self, nums: List[int]) -> List[List[int]]:
        ls = sorted(nums)
        n = len(ls)
        res = []
        for i in range(0, n - 2):
            if i > 0 and ls[i] == ls[i - 1]: # 去重
                continue
            left, right = i + 1, n - 1
            while left < right:
                if left > i + 1 and ls[left] == ls[left - 1]: # 去重
                    left += 1
                    continue
                if right < n - 1 and ls[right] == ls[right + 1]: # 去重
                    right -= 1
                    continue
                sum = ls[i] + ls[left] + ls[right]
                if sum < 0:
                    left += 1
                elif sum > 0:
                    right -= 1
                else:
                    res.append([ls[i], ls[left], ls[right]])
                    left += 1
                    right -= 1
        return res
```

[代码随想录](https://programmercarl.com/0015.%E4%B8%89%E6%95%B0%E4%B9%8B%E5%92%8C.html#%E5%85%B6%E4%BB%96%E8%AF%AD%E8%A8%80%E7%89%88%E6%9C%AC) 在去重 `nums[left]`、`nums[right]` 时用的是另一种写法，思想是一样的，只不过他是在内层写了个 while 处理的，我的代码内层没有 while。
```python
while right > left:
    sum_ = nums[i] + nums[left] + nums[right]
    
    if sum_ < 0:
        left += 1
    elif sum_ > 0:
        right -= 1
    else:
        result.append([nums[i], nums[left], nums[right]])
        
        # 跳过相同的元素以避免重复
        while right > left and nums[right] == nums[right - 1]:
            right -= 1
        while right > left and nums[left] == nums[left + 1]:
            left += 1
            
        right -= 1
        left += 1
```

### [18. 四数之和](https://leetcode.cn/problems/4sum/)

```python
class Solution:
    def fourSum(self, nums: List[int], target: int) -> List[List[int]]:
        ls = sorted(nums)
        res = []
        n = len(ls)
        for i in range(0, n - 3):
            if i > 0 and ls[i] == ls[i - 1]: # 去重
                continue
            for j in range(i + 1, n - 2):
                if j > i + 1 and ls[j] == ls[j - 1]: # 去重
                    continue
                left, right = j + 1, n - 1
                while left < right:
                    if left > j + 1 and ls[left] == ls[left - 1]: # 去重
                        left += 1
                        continue
                    if right < n - 1 and ls[right] == ls[right + 1]: # 去重
                        right -= 1
                        continue
                    sum = ls[i] + ls[j] + ls[left] + ls[right]
                    if sum < target:
                        left += 1
                    elif sum > target:
                        right -= 1
                    else:
                        res.append([ls[i], ls[j], ls[left], ls[right]])
                        left += 1
                        right -= 1
        return res
```

与三数之和类似，去重 `nums[left]`、`nums[right]` 时也可以用代码随想录的写法
