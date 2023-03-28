# 非常有用的 Python 数据结构食谱

> 原文：<https://medium.com/codex/very-useful-python-recipes-on-data-structure-a52aa01f3ad1?source=collection_archive---------11----------------------->

我们将讨论 Python 中数据结构的一些常用方法。

Python 中有多种数据结构，比如列表、元组和字典。虽然这些结构的使用很简单，但是在使用一些比较操作(如排序、排序和过滤)时会出现一些常见的问题。在这篇短文中，我们将讨论一些常见的数据结构和涉及数据的算法。没有进一步的到期，让我们跳转到代码:)

![](img/779ad1b927663a2820fb0c51e1e4fc70.png)

电机[变速杆](https://medicine.wustl.edu/news/scientists-discover-new-way-transform-human-skin-cells-directly-motor-neurons/)

# 星号算子

你需要从一个 iterable 中解包 *N* 个变量，但是 iterable 的长度可能会大于*N。*这样我们可以使用星号(*)操作来解决这个问题。

```
record = ('Math', 'math@someemail.com', '098-111-2345', '946-999-756')
name, email, *phone_numbers = user_record**>>>** name
'Math'
**>>>** email
'math@someemail.com'
**>>>** phone_numbers 
['098-111-2345', '946-999-756']record = ('PTE', 60, 435.45, (03, 24, 2015))name, *_, (*_, year) = record**>>>** name
'PTE'
**>>>** year
2012
```

# 使用 deque 保留最后的 *N 个*项

目标是在迭代或其他处理数据的过程中，保留最后几个项目的有限历史。为此，我们可以利用`collections`库中的`deque`。**虽然你可以用**`**list**` **`**append**`**的方法，但是，** `**deque**` **要快得多，效率高得多。**如何使用`deque`的一个例子如下:**

```
q = deque(maxlen=3) 
q.append(1)
q.append(2)
q.append(3)**>>>** q
deque([1, 2, 3], maxlen=3)q.append(4)**>>>** q
deque([2, 3, 4], maxlen=3)
```

**如果不指定`maxlen,`，队列将是一个无限的存储。您可以使用其他方法来控制队列。比如`pop`、`popleft,` 和`appendleft.`**

**我们可以开发一个基于优先级的队列，稍后将使用`heapq` 结构呈现。**

# **寻找 N 个项目中最大的和最小的— heapq**

**您有一个项目列表，并且想要检索列表中的 *n* 个最大或最小的元素。我们可以使用`heapq`结构。堆数据结构主要用来表示一个优先级队列。Python 中这种数据结构的属性是每次弹出**最小的堆元素(min heap)** 。每当元素被推入或弹出时，**堆结构被维护**。heap[0]元素每次也返回最小的元素。**

**`heapq, nlargest,` 和`smallest`两种方法可以用来检索`n`最小或最大的元素。**

```
import heapqnums = [1, 8, 2, 23, 7, -4, 18, 23, 42, 37, 2] heapq.nlargest(3, nums) 
*>>> [42, 37, 23]* heapq.nsmallest(3, nums)) 
*>>> [-4, 1, 2]*heapq.heappop(heap) 
>>> -4heapq.heappop(heap)
>>> 1heapq.heappop(heap) 
>>> 2
```

**在幕后，他们首先将数据转换成一个列表，列表中的项目作为一个堆进行排序。因此，如果`n`小于项目总数`N`，这种方法可以提供更好的性能。**

# **使用`heapq`实现基于优先级的队列**

**我们可以使用相同的结构通过`heapq`来实现基于优先级的队列。**

```
queue = []# the first element of tuple is priority and the second is the item
heapq.heappush(queue, (-1, 3))heapq.heappush(queue, (-2, 4))heapq.heappush(queue, (0, 5))heapq.heappop(queue)[-1]
>>> 4heapq.heappop(queue)[-1]
>>> 3heapq.heappop(queue)[-1]
>>> 5
```

**函数 heapq.heap push()和 heapq.heappop()在 list _queue 中插入和移除项目，使得列表中的第一个项目具有最小的优先级。这就是为什么我们将优先级索引(元组的第一个元素)作为负数插入。**

# **保持字典整洁**

**如果想让字典保持有序，可以使用`OrderDict`结构。当您希望构建一个以后可能要序列化或编码为不同格式的映射时，OrderedDict 可能特别有用。**

```
from collections importOrderedDictd = OrderedDict() d['foo'] = 1 
d['bar'] = 2 
d['spam'] = 3 
d['grok'] = 4for key in d: 
    print(key, d[key])*>>> "foo 1"
    "bar 2"
    "spam 3"
    "grok 4"*
```

# **用字典计算**

**可能会有这样的情况，您希望对字典数据结构进行计算，如最小值、最大值或排序。**

```
prices = {'ACME': 45.23, 
          'AAPL': 612.78, 
          'IBM': 205.55, 
          'HPQ': 37.20, 
            'FB': 10.75}min_price = min(zip(prices.values(), prices.keys())) 
*# min_price is (10.75, 'FB')*max_price = max(zip(prices.values(), prices.keys())) 
*# max_price is (612.78, 'AAPL')*prices_sorted = sorted(zip(prices.values(), prices.keys())) 
*# prices_sorted is [(10.75, 'FB'), (37.2, 'HPQ'),
# (45.23, 'ACME'), (205.55, 'IBM'),
# (612.78, 'AAPL')]*
```

**涉及`zip()`的解决方案通过将字典“反转”成一系列(值，键)对来解决这个问题。当对这样的元组执行比较时，首先比较值元素，然后是键。**

# **按公共关键字对字典列表进行排序**

**你有两本词典，想找出它们的共同点(相同的关键字，相同的值，等等。).**

```
a={'x' : 1, 'y' : 2, 'z' : 3 }b={'w' : 10, 'x' : 11, 'y' : 2 }*# Find keys in common* a.keys() & b.keys() *# { 'x', 'y' }**# Find keys in a that are not in b* a.keys() - b.keys() *# { 'z' }**# Find (key,value) pairs in common* a.items() & b.items() *# { ('y', 2) }**# Make a new dictionary with certain keys removed* c = {key:a[key] **for** key **in** a.keys() - {'z', 'w'}} 
*# c is {'x': 1, 'y': 2}*
```

**键视图的一个鲜为人知的特性是，它们还支持常见的集合运算，如并集、交集和差集。**

# **按公共关键字对字典列表进行排序**

**您有一个字典列表，并且希望根据一个或多个字典值对条目进行排序。**

```
rows = [{‘fname’: ‘Brian’, ‘lname’: ‘Jones’, ‘uid’: 1003}, 
        {‘fname’: ‘David’, ‘lname’: ‘Beazley’, ‘uid’: 1002}, 
        {‘fname’: ‘John’, ‘lname’: ‘Cleese’, ‘uid’: 1001}, 
        {‘fname’: ‘Big’, ‘lname’: ‘Jones’, ‘uid’: 1004}]from operator importitemgetter
rows_by_fname = sorted(rows, key=itemgetter('fname'))
rows_by_uid = sorted(rows, key=itemgetter('uid')) **print**(rows_by_fname)
**print**(rows_by_uid)#output 
>>>[{'fname': 'Big', 'uid': 1004, 'lname': 'Jones'}, {'fname': 'Brian', 'uid': 1003, 'lname': 'Jones'}, {'fname': 'David', 'uid': 1002, 'lname': 'Beazley'}, {'fname': 'John', 'uid': 1001, 'lname': 'Cleese'}]>>>[{'fname': 'John', 'uid': 1001, 'lname': 'Cleese'}, {'fname': 'David', 'uid': 1002, 'lname': 'Beazley'}, {'fname': 'Brian', 'uid': 1003, 'lname': 'Jones'}, {'fname': 'Big', 'uid': 1004, 'lname': 'Jones'}]
```

# **结论**

**在这篇短文中，在数据结构上下文中提供了一些有用的方法。希望有用:)**