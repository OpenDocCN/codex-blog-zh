# Python 编程

> 原文：<https://medium.com/codex/building-blocks-of-python-b8430e5c318d?source=collection_archive---------3----------------------->

## [法典](http://medium.com/codex)

Python 的设计理念强调代码的可读性，并大量使用空白。

![](img/3e6f512bbb90458407c4498f1059471f.png)

由[克里斯蒂安·阿尔索夫](https://unsplash.com/@aarsoph?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

Python 是一种高级解释编程语言，由吉多·范·罗苏姆于 1991 年首次发布。它是一种通用语言，旨在易于使用，注重可读性和简单性。Python 通常用于 web 开发、数据分析、人工智能、科学计算和其他类型的软件开发。

Python 的一个关键特性是它的易用性。Python 的语法设计得简单直观，初学者和有经验的程序员都可以使用。Python 也是一种解释型语言，这意味着它不需要编译，这使得快速编写和测试代码变得很容易。

Python 的另一个重要特性是支持面向对象编程。Python 允许用户创建类和对象，并在这些对象上定义方法。这使得它成为构建复杂软件系统的强大工具。

Python 还包括一个庞大且不断增长的内置模块和包库。这些模块提供了广泛的功能，从处理字符串、数组和字典到处理数据库、web 框架和机器学习工具。Python 的开源生态系统是其最大的优势之一，因为它允许开发人员轻松访问和集成各种第三方库和工具。

用 Python 构建的最流行的 web 开发框架之一是 Django。Django 是一个全栈的 web 框架，它提供了一组用于快速、轻松地构建 web 应用程序的约定和工具。Django 专注于开发人员的生产力，已经成为初创公司、小型企业和大型企业的热门选择。

Python 在数据分析和科学计算中的应用也推动了它的流行。通过像 NumPy、Pandas 和 Matplotlib 这样的包，Python 已经成为数据分析和可视化的主要语言。近年来，Python 也成为人工智能和机器学习的流行语言，TensorFlow、PyTorch 和 Scikit-learn 等软件包为构建机器学习模型提供了强大的工具。

大多数现代编程语言都有一套类似的构建模块，例如

1.  接收来自用户的输入并向用户显示输出
2.  在变量中存储值的能力(通常是不同种类的变量，如整数、浮点或字符)
3.  可以存储姓名、地址或任何其他类型文本的字符串
4.  一些高级数据类型，如数组，可以存储一系列常规变量(如一系列整数)
5.  循环代码的能力从某种意义上说，你想从一个用户那里接收 10 个名字，你将为此写 10 次代码，但只写一次，并告诉计算机循环 10 次
6.  有条件地执行代码语句的能力，例如，如果分数超过 40 分，则学生通过，否则失败
7.  将您的代码放在函数中
8.  高级数据类型是通过一种或多种基本数据类型(如结构或类)的组合形成的
9.  从磁盘读取文件并将文件保存到磁盘
10.  能够对您的代码进行注释，以便您在以后再次访问它时能够理解它

让我们开始吧，看看我们如何用 Python 来做这些事情。

在我们开始用 Python 编写程序之前，我们需要安装 [Anaconda](https://www.anaconda.com/products/individual) 。这将安装 Anaconda 数据科学环境和 Spyder IDE，以便用 Python 进行编码。完成后，打开 Spyder 并尝试下面的代码，看看是否一切正常。

```
myString = "Hello, World!"
print (myString)
```

## 1.接收来自用户的输入并向用户显示输出

有几种方法可以向用户显示输出。让我们看看显示输出的一些方法:

```
name = input('please enter your name') # receiving character input
print("hello ", name, ",how are you?") # showing character outputage = input('please enter your age') # receiving numeric input
print('so you are', age, 'years old.')
```

## 2.在变量中存储值的能力(通常是不同种类的变量，如整数、浮点或字符)

Python 是动态类型的——在使用它之前不需要输入变量的数据类型。这有时会导致意想不到的问题，例如，如果用户输入了一个字符，而您期望的是一个数字。为了避免这种问题，可以使用 type()。或者，您可以通过给变量赋值来“定义变量”(比如年龄=20)。

基本数据类型:在 Python 中，我们有几种类型的对象，让我们看看其中重要的:

```
# Boolean / Logical
v = TRUE 
print(type(v)) # type funciton can be used to see the data type of the variable# Numeric
v = 23.5
print(type(v))# Integer
v = 2L
print(type(v))# Complex
v = 2+5i
print(type(v))# Character
v = "TRUE"
print(type(v))# Some common number functions:
hex(1) # hexadecimal representation of numbers
bin(1) # binary representation of numbers
2**3 # 2^3, 2 to the power 3
pow(2,3) # 2**3
pow(2,3,4) # 2**3 % 4
abs(-2.33)
round(3.14)
round(3.14159,2) # only till 2 decimal places
import math
sq_rt = math.sqrt(variable) # returns the square root of the variable
```

高级数据类型:Python 的强大之处在于它允许我们访问一些高级变量类型，而不是前面提到的基本类型。让我们来看看一些高级变量类型:

```
# Lists - A list can contain many different types of elements inside it such as character, numeric, etc. and even another list inside it.# Create a list through enumeration.
a=[] # with this we initialize a list element
a=range(1,10) # with this we insert a range of values from 1-10 in the list
print(list(a)) # to show the list as a list, we need to tell the print function that we are passing it a list
# Output: [1, 2, 3, 4, 5, 6, 7, 8, 9] # 10 is excluded because upper bound is excluded in python# we can have mixed data types in a list
b=[1,2,3,'vivek',True,4,5]
print(list(b))
# index of list start with 0, 1, 2 ..
# so vivek is present at index 3
print(b[3])# slicing - [start:stop:step]
a[1:6:2] # starts from 1 and goes up until 6 and selects every second element# reversing a list
L[::-1] # this would take a lot more effort to do in C++!# tuples - immutable list, cant be changed
t = (1,2,3) # use () instead of []# dict - d = {'key':'value', ..} is an unordered mutable key:value pairs {"name":"frankie","age":33}# Dictionary is quite useful in matrix indexing
m=np.array([[1,2,3],[4,5,6],[7,8,9]])
col_names={'age':0, 'weight':1, 'height':2}
row_names={'aa':0, 'cc':1, 'bb':2}
# now we can get weight of ale using actual indexes or dict indexes
m[1,1] # 5
m[row_names['cc'],col_names['weight']] # 5# set - s=set('a','b','c',..) - unordered collection of unique objects
# It looks like a dictionary {"a","b"} when python shows output, but it is not because it doesn’t have key:value pairs
set([1,1,2,3]) # output: {1,2,3} , List can be passed to set()
set("Mississippi") # output: {'M', 'i', 'p', 's'} , Even strings can be passed to set# Matrices - A matrix is a two-dimensional rectangular data set. It can be created using .array() function.
# Create a matrix
import numpy as np # we need to import the numpy libabry which provides tools for numerical computing. 
m=np.array([[1,2,3],[4,5,6],[7,8,9]])
print(type(m))# Arrays - while matrices are confined to two dimensions, arrays can be of any number of dimensions. # Create an array.
import numpy as np # we need to import the numpy libabry which provides tools for numerical computing. 
a=np.array([1,2,3]) # this is a 1 dimentional array
print(type(a))# Convert a list to an array
a=[1,2,3,4]
a=np.array(a) # array([1, 2, 3, 4])# DataFrame - this is an advanced object that can be used by installing the pandas library. If you are familiar with R, this is similar to data.frame. If you are familiar with excel, you can think of a dataframe as a table with rows and column where rows and colums can potentially have names/labels. You can access data within the dataframe using row/column number (indexing starts from 0) or their labels. import pandas as pd
# From dict
pd.DataFrame({'col_1': [3, 2, 1, 0], 'col_2': ['a', 'b', 'c', 'd']})# from list
pd.DataFrame(['orange', 'mango', 'grapes', 'apple'], index=['a', 'b', 'c', 'd'], columns =['Fruits'])# from list of lists
pd.DataFrame([['orange','tomato'],['mango','potato'],['grapes','onion'],['apple','chilly']], index=['a', 'b', 'c', 'd'], columns =['Fruits', 'Vegetables'])# from multiple lists
pd.DataFrame(
        list(zip(['orange', 'mango', 'grapes', 'apple'],
                ['tomato', 'potato', 'onion', 'chilly']))
        , index=['a', 'b', 'c', 'd']
        , columns =['Fruits', 'Vegetables'])
```

## 3.可以存储姓名、地址或任何其他类型文本的字符串

在 Python 中，写在一对单引号或双引号内的任何值都被视为字符串。

这里的关键思想是学习如何操作字符串变量

我们将关注一些常见的操作:a .连接字符串

```
# Concatenate strings
str1 + str2 + " " + str3
```

b.计算字符串中的字符数

```
# Counting number of characters in a string
str1 = "vivek"
len(str1)
```

c.更改大小写— toupper() & tolower()函数

```
str1.upper() # convert string to upper case (.lower() for lower case)
str1.isupper(), str1.islower() # check if a string or a character is upper or lower
```

d.拆分字符串

```
s.split('e') # returns list of strings before and after e. if there are multiple e's, then split happens for all instances of e
```

e.字符串的回文

```
str1 = "vivek"
str1[::-1]
```

## 4.一些高级数据类型，比如可以存储一系列常规变量(比如一系列整数)的列表

列表是存储在一个变量中的一系列变量。列表可以是一维的，也可以是多维的。使用 list()函数创建一个列表。它接受变量(甚至其他列表)作为输入。列表不同于字符串，因为元素可以变异/改变。

```
# Defining
L=[0,0,0] # [0, 0, 0]
L1=[0]*3 #shorthand way of defining a list with repeated elements# Supports indexing and slicing
L1=['one', 'two', 'three']
L1[0] # 'one'
L1[1:2] # ['two'], upper bound is excluded
L1[1:3] # ['two', 'three']# Indexing nested lists
L1 = ['one', 'two', ['three', 'four'], 'five']
L1[2][0] # 'three'# Elements can be added
L1.append('six')# Elements can be removed
L1.pop() # last element gets popped, we can save it in a variable also# Sort
L1.sort() # sorts the list in-place, the actual list gets sorted
sorted(L1) #returns the sorted version of L3 list# Reverse
L1=['c','a','b']
L1.reverse() # reverses the list in-place, the actual list gets reversed# Multi dimentional list indexing
L1=[[1,2,3],[4,5,6],[7,8,9]]
L1[0][:] # returns first row
```

## 5.循环代码的能力从某种意义上说，你想从一个用户那里接收 10 个名字，你将为此写 10 次代码，但只写一次，并告诉计算机循环 10 次

Python 有几个循环选项，比如‘for’和‘while’。还有嵌套选项(单个、两个、三个，..)循环。a . While 循环反复执行相同的代码，直到满足停止条件:

```
# Syntax
while test:
    code statements
else:
    final code statements# Example
x = 0
while x < 10:
    print('x is currently: ',x)
    print(' x is still less than 10, adding 1 to x')
    x+=1
```

b.for 循环:在 Python 中充当迭代器；它遍历序列中的项或任何其他可迭代的项。我们已经知道可以迭代的对象包括字符串、列表、元组，甚至是字典内置的可迭代对象，比如键或值。

```
# Syntax
for item in object:
    statements to do stuff# Example
list1 = [1,2,3,4,5,6,7,8,9,10]
for num in list1:
    print(num)
```

Python 还提供了 break、continue 和 pass 语句，允许我们进一步改变循环。以下是它们的用法:break:脱离当前最近的封闭循环。继续:转到最近的封闭循环的顶部。不予处理:不执行任何操作。

```
# Thinking about break and continue statements, the general format of the while loop looks like this:
while test: 
    code statement
    if test: 
        break
    if test: 
        continue 
else:
```

break 和 continue 语句可以出现在循环体中的任何地方，但是我们通常会将它们与 if 语句进一步嵌套，以便根据某种条件执行某个操作。

## 6.有条件地执行代码语句的能力，例如，如果分数超过 40 分，则学生通过，否则失败

Python 提供了如果..，如果..其他..，如果..其他..如果..应用条件逻辑的语句。让我们来看看它们:a .创建和 if 语句的基本语法是:

```
if False:
    print('It was not true!')
```

b.创建 if…else 语句的基本语法是:

```
x = False
if x:
    print('x was True!')
else:
    print('I will be printed in any case where x is not true')
```

c.创建 if…else if…else 语句的基本语法是:

```
loc = 'Bank'
if loc == 'Auto Shop':
    print('Welcome to the Auto Shop!')
elif loc == 'Bank':
    print('Welcome to the bank!')
else:
    print('Where are you?')
```

## 7.将您的代码放在函数中

函数允许我们创建一个可以多次执行的代码块，而不需要重写。

```
# Syntax
def name_of_function(argument_name='default value'): #snake casing for name, all lower case alphabets with underscores
    '''
    what funciton does
    '''
    print ('hello',argument_name)
    print (f'hello {argument_name}') #both print do the same thing# Example
def add_function(a=0,b=0):
    return a+b
```

我们可以通过以下两种方式调用该函数:

```
# option 1
add_function(2,3)    
# option 2
c=add_function(3,4)
```

*args 和**kwargs 代表参数和关键字参数，允许我们扩展函数的功能。*args 允许函数接受任意数量的参数。所有参数都作为一个元组接收，例如— (a，b，c，..).args 可以改名为别的，真正重要的是*。

```
def myfunc(*args):
    return args
'''
myfunc(1,2,3,4,5,6,7,8,9)
Out[30]: (1, 2, 3, 4, 5, 6, 7, 8, 9)
'''
```

**kwargs 允许函数接受任意数量的关键字参数。所有参数都作为键/值对的字典接收。kwargs 可以改名为别的，真正重要的是**。

```
def myfunc(**kwargs):
    print(kwargs)
'''
myfunc(name='vivek', age=34, height=186)
{'name': 'vivek', 'age': 34, 'height': 186}
'''
```

## 8.通过一种或多种基本数据类型(如结构或类)的组合形成的高级数据类型

Python 允许用户创建类。这些可以是变量和对这些变量进行操作的函数的组合。让我们看看如何定义和使用它们。

```
# Define a class
class Person:
    "This is a person class"
    age = 10 def greet(self):
        print('Hello')# Using class
print(Person.age) # Output: 10
print(Person.greet) # Output: <function Person.greet>
print(Person.__doc__) # Output: 'This is my second class'# Creating an object of the class and using that
vivek = Person() # create a new object of Person class
print(vivek.greet) # Output: <bound method Person.greet of <__main__.Person object>>
vivek.greet() # Calling object's greet() method; Output: Hello
```

## 9.从磁盘读取文件并将文件保存到磁盘

让我们看看如何有条理地读写 csv 文件。CSV 是数据科学中最常用的文件类型，但是 python 也可以直接从网站上读取其他几种文件类型和数据。

```
import pandas# read a csv using the pandas package
df = pandas.read_csv('student_data.csv')
print(df)# write data to a csv using pandas package
df.to_csv('student_data_copy.csv')
```

## 10.能够对您的代码进行注释，以便您在以后再次访问它时能够理解它

我们可以告诉 Python，一行代码以#开头就是一个注释。

```
# this is a comment
```

我们可以通过用三个倒单引号将多行文本块括起来来判断它是注释。

```
'''
this
is
a
comment
block
''' 
```

总的来说，Python 是一种功能强大的通用编程语言，非常适合各种编程任务。Python 强调简单性、面向对象的设计以及一个庞大且不断增长的第三方库和工具生态系统，对于初学者和有经验的程序员来说都是一个有价值的工具。无论是构建 web 应用程序、分析数据，还是从事人工智能项目，Python 都提供了快速、灵活和愉快的开发体验。

最后，我将强调在学习任何新东西时练习的重要性。坚持不懈，尝试这些积木的不同组合，先解决较简单的问题，再解决较复杂的问题，是变得流利的唯一途径。

欢迎评论！