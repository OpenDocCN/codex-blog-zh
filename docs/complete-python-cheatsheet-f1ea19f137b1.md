# 完整的 Python 备忘单

> 原文：<https://medium.com/codex/complete-python-cheatsheet-f1ea19f137b1?source=collection_archive---------4----------------------->

## 为了快速参考

## 用于数据科学的 Python

![](img/1107ba9fece054413f9c359ea92d7eaf.png)

在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上由 [Hitesh Choudhary](https://unsplash.com/@hiteshchoudhary?utm_source=medium&utm_medium=referral) 拍摄的照片

# 基础

python 编程语言的基本语法

## 向用户显示输出

打印功能用于显示或打印输出

```
print(“Content that you wanna print on-screen”)
```

## 接受用户的输入

input 函数用于接受用户的输入。

```
var1 = input(“Enter your name: “)
```

## 空列表

此方法允许您创建一个空列表

```
my_list = []
```

## 空字典

通过加上两个花括号，你可以创建一个空白的字典

```
my_dict = {}
```

## 距离函数

Range 函数返回一系列数字，例如 range(0，n)中从 0 到 n-1 的数字

```
range(int_value)
```

## 评论

注释是用来让程序员更容易理解代码的，它们不是由编译器或解释器执行的。

> 单行注释

```
#This is a single line comment
```

> 多行注释

```
’’’This is a
multi-line
comment’’’
```

# 换码顺序

转义序列是一个字符序列；在字符串文字或字符中使用时，它不表示自身。

## 新行

换行符

```
\n
```

## 反斜线符号

它添加了一个反斜杠

```
\\
```

## 单引号

它添加了一个单引号

```
\’
```

## 标签

它提供了一个制表符空间

```
\t
```

## 退格

它添加了一个退格

```
\b
```

## 八进制值

它表示一个八进制数的值

```
\ooo
```

## 十六进制值

它表示一个十六进制数的值

```
\xhh
```

## 回车

回车或\r 是 Python 的一个独特功能。

```
\r
```

# 用线串

Python 字符串是一个字符序列，每个字符都可以单独访问。使用它的索引。

## 线

您可以通过用两种形式的引号(单引号或双引号)将文本括起来来创建字符串。

```
variable_name = “String Data”
```

## 限幅

切片是指从给定的字符串中获取子字符串。

```
var_name[n : m]
```

## 字符串方法是 isalnum()方法

如果字符串中的所有字符都是字母数字，则返回 True。

```
string_variable.isalnum()
```

## isalpha()方法

如果字符串中的所有字符都是字母，则返回 True。

```
string_variable.isalpha()
```

## isdecimal()方法

如果字符串中的所有字符都是小数，则返回 True。

```
string_variable.isdecimal()
```

## isdigit()方法

如果字符串中的所有字符都是数字，则返回 True。

```
string_variable.isdigit()
```

## islower()方法

如果字符串中的所有字符都是小写，则返回 True。

```
string_variable.islower()
```

## isspace()方法

如果字符串中的所有字符都是空格，则返回 True。

```
string_variable.isspace()
```

## isupper()方法

如果字符串中的所有字符都是大写，则返回 True。

```
string_variable.isupper()
```

## lower()方法

将字符串转换成小写。

```
string_variable.lower()
```

## upper()方法

将字符串转换成大写。

```
 string_variable.upper()
```

## strip()方法

它删除字符串中的前导和尾随空格。

```
string_variable.strip()
```

# 目录

Python 中的列表表示方括号中任何数据类型的逗号分隔值的列表。

## 目录

```
var_name = [element1, element2, and so on]
```

## 列表方法索引方法

返回具有指定值的第一个元素的索引。

```
list.index(element)
```

## 附加方法

在列表末尾添加一个元素。

```
list.append(element)
```

## 扩展方法

将列表(或任何可迭代的)的元素添加到当前列表的末尾。

```
list.extend(iterable)
```

## 插入方法

在指定位置添加元素。

```
list.insert(position, element)
```

## Pop 方法

移除指定位置的元素并返回它。

```
list.pop(position)
```

## 移除方法

remove()方法从列表中移除给定项目的第一个匹配项。

```
list.remove(element)
```

## 清除方法

从列表中移除所有元素。

```
list.clear()
```

## 计数方法

返回具有指定值的元素数量。

```
list.count(value)
```

## 反向方法

反转列表的顺序。

```
list.reverse()
```

## 排序方法

对列表进行排序。

```
list.sort(reverse=True|False)
```

# 元组

元组表示为括号内任何数据类型的逗号分隔值的列表。

## 元组创建

```
variable_name = (element1, element2, …)
```

# 元组方法:

## 计数方法

它返回指定值在元组中出现的次数。

```
tuple.count(value)
```

## 指数法

它在元组中搜索指定的值，并返回位置。

```
tuple.index(value)
```

# 设置

集合是多个值的集合，既无序又无索引。它写在大括号里。

## 布景创建:方式 1

```
var_name = {element1, element2, …}
```

## 布景创建:方式 2

```
var_name = set([element1, element2, …])
```

# 设置方法:

## Add()方法

向集合中添加元素。

```
set.add(element)
```

## Clear()方法

从集合中移除所有元素。

```
set.clear()
```

## Discard()方法

从集合中移除指定的项。

```
set.discard(value)
```

## 交集()方法

返回两个或更多集合的交集。

```
set.intersection(set1, set2 … etc)
```

## issubset()方法

检查一个集合是否是另一个集合的子集。

```
set.issubset(set)
```

## pop()方法

从集合中移除元素。

```
set.pop()
```

## 移除()方法

从集合中移除指定元素。

```
set.remove(item)
```

## union()方法

返回集合的并集。

```
set.union(set1, set2…)
```

# 字典

字典是一个由逗号分隔的键:值对组成的无序集合，在{}中，要求在一个字典中，没有两个键是相同的。

## 词典

```
<dictionary-name> = {<key>: value, <key>: value …}
```

## 向字典中添加元素

通过这种方法，人们可以向词典中添加新元素。

```
<dictionary>[<key>] = <value>
```

## 更新字典中的元素

如果指定的键已经存在，那么它的值将被更新。

```
<dictionary>[<key>] = <value>
```

## 从字典中删除元素

del let 用于从字典中删除指定的键:值对。

```
del <dictionary>[<key>]
```

# 字典函数和方法:

## len()方法

它返回字典的长度，即字典中元素(键:值对)的计数。

```
len(dictionary)
```

## clear()方法

从字典中移除所有元素。

```
dictionary.clear()
```

## get()方法

返回指定键的值。

```
dictionary.get(keyname)
```

## items()方法

返回包含每个键值对的元组的列表。

```
dictionary.items()
```

## keys()方法

返回包含字典关键字的列表。

```
dictionary.keys()
```

## values()方法

返回字典中所有值的列表。

```
dictionary.values()
```

## update()方法

用指定的键值对更新字典。

```
dictionary.update(iterable)
```

# 条件语句

if 语句是 Python 中的条件语句，它们实现选择结构(决策结构)。

## 如果语句

```
if(conditional expression):
statements
```

## if-else 语句

```
if(conditional expression):
statements
else:
statements
```

## if-elif 语句

```
if (conditional expression) :
statements
elif (conditional expression) :
statements
else :
statements
```

## 嵌套的 if-else 语句

```
if (conditional expression):
if (conditional expression):
statements
else:
statements
else:
statements
```

# 迭代语句

迭代语句或循环重复执行一条语句，称为循环体，直到控制表达式为假(0)。

## For 循环

Python 的 for 循环旨在逐个处理任何序列的项目，例如列表或字符串。

```
for <variable> in <sequence>:
statements_to_repeat
```

## While 循环

while 循环是一个条件循环，只要条件保持为真，它就会重复自身内部的指令。

```
while <logical-expression> :
loop-body
```

## break 语句

break 语句使程序能够跳过一部分代码。break 语句终止它所在的循环。

```
for <var> in <sequence> :
statement1
if <condition> :
break
statement2
statement_after_loop
```

## 连续语句

continue 语句跳过其余的循环语句，并导致下一次迭代发生。

```
for <var> in <sequence> :
statement1
if <condition> :
continue
statement2
statement3
statement4
```

# 功能

函数是执行特定任务的代码块。您可以将参数传递给函数。它帮助我们使代码更有组织性和可管理性。

## 函数定义

```
def my_function(parameters):
# Statements
```

# 文件处理

文件处理是指从文件中读取或写入数据。Python 提供了一些函数，允许我们操作文件中的数据。

## open()函数

```
var_name = open(“file name”, “opening mode”)
```

## close()函数

```
var_name.close()
```

## Read()函数

read 函数包含不同的方法，read()、readline()和 readlines()

```
read() #return one big string
```

它返回一个行列表。

```
read-lines
```

它一次返回一行。

```
readline
```

# Write()函数

该函数将一系列字符串写入文件。

```
write () #Used to write a fixed sequence of characters to a file
```

它被用来写一个字符串列表。

```
writelines()
```

## Append()函数

append 函数用于追加到文件中，而不是覆盖它。要追加到现有文件，只需在追加模式下打开文件(a):

```
file = open(“Hello.txt”, “a”)
```

# 异常处理

异常是导致程序流程中断的异常情况。

## 试着除了

python 中的一个基本 try-catch 块。当 try 块抛出错误时，控制转到 except 块。

```
try:
[Statement body block]
raise Exception()
except Exception as e:
[Error processing block]
```

# 面向对象的程序设计系统(Object-Oriented Programming System)

这是一种主要关注使用对象和类的编程方法。对象可以是任何现实世界的实体。

## 班级

用 python 写类的语法。

```
class class_name:
#Statements
```

## 用构造函数初始化

使用 python 中的构造函数编写类的语法。

```
class Medium:
# Default constructor
def __init__(self):
self.name = “Medium”
# A method for printing data members
def print_me(self):
print(self.name)
```

## 目标

实例化一个对象。

```
<object-name> = <class-name>(<arguments>)
```

## 滤波函数

filter 函数允许您处理 iterable 并提取满足给定条件的那些项。

```
filter(function, iterable)
```

## issubclass 函数

用于确定一个类是否是给定类(classinfo)的子类。

```
issubclass(class, classinfo)
```

# 迭代器和生成器

## 迭代程序

用于在可迭代对象上创建迭代器。

```
iter_list = iter([‘Shubhro’, ‘Taplu’, ‘Jhaplu’])
print(next(iter_list))
print(next(iter_list))
print(next(iter_list))
```

## 发电机

用于动态生成值。

```
def my_gen():
n = 1
print(‘This is printed first’)
# Generator function contains yield statements
yield n
n += 1
print(‘This is printed second’)
yield n
n += 1
print(‘This is printed at last’)
yield n
```

# 装修工

装饰器用于修改函数或类的行为。它们通常在你想要修饰的函数定义之前被调用。

## 属性装饰器(getter)

```
@property
def name(self):
return self.__name
```

## 二传手装饰工

它用于设置属性“名称”。

```
@name.setter
def name(self, value):
self.__name=value
```

## 装饰工

它用于删除属性“名称”。

```
@name.deleter #property-name.deleter decorator
def name(self, value):
print(‘Deleting..’)
del self.__name
```