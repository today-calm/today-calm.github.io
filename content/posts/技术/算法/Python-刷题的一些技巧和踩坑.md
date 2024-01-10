---
title: "Python 刷题的一些技巧和踩坑"
date: 2024-01-10T21:34:30+08:00
lastmod: 2024-01-10T22:09:19+08:00
categories: ["技术"]
tags: ["算法", "Python"]
summary: ""
---

## 技巧

### 字符串

#### 字符串 split

```python
s = '   a    b c  '
print(s.split(' ')) # ['', '', '', 'a', '', '', '', 'b', 'c', '', '']
print(s.split()) # ['a', 'b', 'c']
```

空格参数与不传入参数有区别：  
如果字符串中含有多个连续的空格或者开头和结尾的空格，使用 `split()` 和 `split(' ')` 的结果会有所不同。`split()` 会自动忽略连续的空格并去除开头和结尾的空格，而 `split(' ')` 不会去除连续的空格和开头结尾的空格。

#### 字符串、列表相互转换

```python
s = "hello"
ls = list(s)
```

```python
ls = ['h', 'e', 'l', 'l', 'o']
s = ''.join(ls)
```

### 列表

#### 列表添加元素

```python
# 添加单个元素
ls.append(x)

# 添加多个元素
ls.extend(seq) # seq: 元素列表，可以是列表、元组、集合、字典，若为字典,则仅会将键(key)作为元素依次添加至原列表的末尾。
```

#### 使用推导式创建列表

不用列表推导式创建列表的代码
```python
squared = []
for x in range(10):
    squared.append(x**2)
```

用列表推导式简化
```python
squared = [x**2 for x in range(10)]
```

#### 判断某个值是否在列表中

判断是否存在：`x in ls`  
判断是否不存在：`x not in ls`

#### 列表切片整体替换

切片整体替换，而不是一个个替换

```python
ls[2:5] = [1, 2, 3]
```

#### 列表切片越界不会报错

```python
s = 'abc'  
print(s[1:9]) # bc
```

当指定的结束索引超出了字符串的长度时，Python 不会报错，而是会返回字符串中从开始索引到结束索引之间的子字符串。  
当你使用 `s[1:9]` 时，结束索引 `9` 超出了字符串 `'abc'` 的长度，因此 Python 会自动将结束索引调整为字符串的长度，即 `s[1:3]`，最终返回的结果是 `'bc'`。

切片越界时，赋值也没问题
```python
ls = [1, 2, 3]
ls[1:9] = [9, 9, 9]
print(ls) # [1, 9, 9, 9]
```

#### 列表排序

你可以使用内置的 sorted 函数或列表的 sort 方法对列表进行排序。

使用 sorted 函数：

```python
my_list = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
sorted_list = sorted(my_list)
```

使用 sort 方法：
```python
my_list = [3, 1, 4, 1, 5, 9, 2, 6, 5, 3, 5]
my_list.sort()
```

这两种方法都会对列表进行排序，sorted 函数返回一个新的已排序列表，而 sort 方法则会直接对原列表进行排序。排序的顺序默认是升序，如果需要降序排序，可以使用 `reverse=True` 参数。

默认情况下，它们使用 Python 的内置比较函数来比较元素，并按照升序顺序进行排序。如果想要自定义排序规则，可以使用 sorted() 函数的 key 参数或 list.sort() 方法的 key 参数来指定一个自定义的排序函数。这个自定义的排序函数可以接受一个元素作为参数，并返回一个用于排序的关键值。根据这个关键值来进行排序。

例如，如果想要按照元素的绝对值大小进行排序，可以使用如下的代码：
```python
sorted_list = sorted([3, -1, 5, -2, 0], key=lambda x: abs(x))
print(sorted_list)  # 输出：[0, -1, -2, 3, 5]
```

#### 列表反转

1.  使用reverse()方法：  
    reverse()方法会原地翻转列表中的元素，即修改原始列表而不创建新的列表。  
    注意：没有返回值！
```python
my_list = [1, 2, 3, 4, 5]
my_list.reverse()
print(my_list)  # 输出：[5, 4, 3, 2, 1]
```

2.  使用切片操作：  
    通过对列表使用切片操作，可以创建一个新的翻转后的列表。

```python
my_list = [1, 2, 3, 4, 5]
reversed_list = my_list[::-1]
print(reversed_list)  # 输出：[5, 4, 3, 2, 1]
```

3.  使用reversed()函数：  
    reversed()函数可以返回一个迭代器，通过list()函数将其转换为列表。

```python
my_list = [1, 2, 3, 4, 5]
reversed_list = list(reversed(my_list))
print(reversed_list)  # 输出：[5, 4, 3, 2, 1]
```

### 实现栈

用列表即可
```python
ls.append()
ls.pop() # 返回值为弹出元素，注意 ls 为空时会报错
ls[-1] # 取栈顶元素
```

### 实现队列

需要引入内置双端队列
```python
# LeetCode 已自动引入 collections
d = collections.deque()
d.appendleft(1)
d.append(2)
d.popleft()
d.pop()
```

deque 使用上和 list 差不多，但多了队列的一些方法

实现原理：双向链表，两端插入、删除时间复杂度 O(1)，查找时间复杂度 O(n)，因为栈、队列没有查找方法，所以 deque 很高效

ChatGPT：
- 当使用 `popleft` 方法从双端队列的队首移除元素时，其余元素不会像列表一样向前移动一位。`collections.deque` 内部实现采用了双向链表，因此在移除队首元素时，操作的时间复杂度为 O(1)。与列表不同，`collections.deque` 在两端进行插入和删除操作的时间复杂度都是 O(1)，因此它是一个高效的数据结构，特别适用于队列和栈的场景。
- 在 `collections.deque` 中，由于它是基于双向链表实现的，因此对于随机访问（根据索引查找第 n 位元素）的操作，时间复杂度是 O(n)。

### 集合

```python
# 从列表创建集合
set([1, 2, 3])

# 判断元素是否在集合中
x in s

# 添加元素
s.add( x )
s.update( x ) # 参数可以是列表，元组，字典（会将其中的元素一个个添加到集合中）

# 移除元素
s.remove( x ) # 如果元素不存在，会发生错误
s.discard( x ) # 如果元素不存在，不会发生错误

# 获取元素个数
len(s)

# 清空
s.clear()
```

### 字典

获取 value 时只能用 `d['a']`，不能用 `d.a`

```python
# 判断键是否在字典中
key in d

# 访问 value
d['Name'] # 在 key 不在字典中时，会触发 KeyError 异常
d.get('Name', default=None) # key 不在字典中时，可以返回默认值 None 或者设置的默认值

# 添加/修改键值对
d['Age'] = 8

# 设置默认值（仅当字典中不存在该 key 时生效）
d.setdefault('Sex', default=None)

# 删除键值对
del d['Name']

# 获取字典长度
len(d)

# 遍历
for key, value in d.items():
    pass
for key in d.keys(): # 或 for key in d: 效果一样
    pass
for value in d.values():
    pass

# 清空
d.clear()
```

### 其他

#### 英文字母构成的哈希表

由于英文字母只有 26 个，可以不创建字典，直接用 26 位数组即可  
不过要用 `ord()` 函数（ordinal 序数的简写），通过 `ord(x) - ord('a')` 来获取 x 在数组中的索引

#### range 步长

第三个参数表示步长  
第二个参数表示小于几，当步长不是 1 时，第二参数不是代表循环几次！我会有这种奇怪的误解！！！
```python
for i in range(0, 9, 2):
    pass
```

#### 更方便地遍历

```python
for index, value in enumerate(my_list):
    pass
```

#### 空数据结构视为 False

在 Python 中，任何空的数据结构（如空列表、空字典、空元组等）都会被视为 False，这点和 js 不同

判断栈是否为空可以这么写
```python
if not stack:
    pass
```

#### 取两个数中较大/小值

用 `max()`、`min()` 函数

#### 正无穷

`float('inf')`

## 踩坑

### 创建二维数组的坑

```python
# 错误写法！！！每个子数组都是相同的引用
[[0] * n] * n

# 正确写法
[[0] * n for _ in range(n)]
```

举个例子
```python
res = [[0] * 3] * 3  
res[0][1] = 1  
print(res) # [[0, 1, 0], [0, 1, 0], [0, 1, 0]]
```

输出结果并不是 `[[0, 1, 0], [0, 0, 0], [0, 0, 0]]`，因为 `[[0] * 3] * 3 ` 创建了 3 个引用相同的子数组  
`[0] * 3` 不会有引用的问题，因为是将 int 复制了 3 次

### 字典的 key 不能是可变类型

list 不能作为 key，但可以将 list 转换为 tuple，tuple 可作为 key

### 有负数时整除的坑

[为什么 Python 中的整除是向下取整？](https://zhuanlan.zhihu.com/p/72774354)  
可以用 `int()` 函数来截断小数，不过注意要传入浮点数类型，传非整数的字符串类型会报错  
也可以用 `math.trunc()`

### 定义递归函数报错

如果你在类中定义了递归函数，并且在函数内调用自身，确保使用 `self` 来调用函数。例如，应该使用 `self.reverseList()` 而不是 `reverseList()`

### 函数内修改外部变量

需在函数内声明 `nonlocal 变量名`
