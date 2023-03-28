# 数字备忘单

> 原文：<https://medium.com/codex/numpy-cheatsheet-7f67b2eee5ab?source=collection_archive---------3----------------------->

## NumPy 代表数字 Python

![](img/d945fd02d4fae55f0ebd3192d0e450da.png)

NumPy 是用于科学计算的 Python 库。这是一个强大的数学库，用于数组计算。这是一个开源项目，你可以免费使用。

# 安装 NumPy

```
pip install numpy
```

在命令提示符下编写该语句会在系统中安装 numpy。如需更多指导，请参考以下官方指南。

[](https://numpy.org/install/) [## NumPy

### 安装 NumPy 的唯一前提是 Python 本身。如果你还没有 Python 并且想要最简单的方法…

numpy.org](https://numpy.org/install/) 

## **导入数字**

```
In [1]: import numpy as np
```

我们可以通过执行这条语句来导入。为了简单起见，我们使用 np 作为缩写，因为它更短并且使用方便。

## **NumPy VS Python 列表**

在 Python 中，我们将“列表”作为数组，但是它们处理起来很慢。NumPy 的目标是提供一个数组对象，即 *ndarray* ，它比传统的 Python 列表快 50 倍。

numpy 更快的主要原因是它存储在内存中的一个连续位置，因此可以很容易地访问和操作。

## **阵列的类型和形状**

在下面的代码数组声明中，可以看到 shape 和 type 函数。“形状”功能有助于了解行数和列数。

```
In [3]:
b=np.array([[1],[2],[3],[4]])
In [4]:
print(b)
print(type(b))
print(b.shape)**OUTPUT:**
[[1]  [2]  [3]  [4]] 
<class 'numpy.ndarray'> 
(4, 1)
```

## **Numpy 数组创建**

Numpy 在创建只包含 0、1 或任何常数的数组时非常有用。我们也可以使用随机模块创建包含随机数的数组。

```
In [4]:a= np.zeros((3,3))
print(a)**OUTPUT**[[0\. 0\. 0.]
 [0\. 0\. 0.]
 [0\. 0\. 0.]]In [5]:b=np.ones((2,3))
print(b)**OUTPUT**[[1\. 1\. 1.]
 [1\. 1\. 1.]]**## Array of some constants** In [6]:
c=np.full((3,2),5)
print(c)**OUTPUT**
[[5 5]
 [5 5]
 [5 5]]**## Identity Matrix** In [7]:d=np.eye(4)
print(d)**OUTPUT**
[[1\. 0\. 0\. 0.]
 [0\. 1\. 0\. 0.]
 [0\. 0\. 1\. 0.]
 [0\. 0\. 0\. 1.]]**## Random Matrix**In [8]:e=np.random.random((2,3))
print(e)*### THIS CREATES ARRAY CONTAINING NUMBERS BETWEEN 0 AND 1***OUTPUT**[[0.53270316 0.92684934 0.78472966]
 [0.15786155 0.13987468 0.65476151]]In [9]:ran=np.random.random((2,3))*100
print(ran)**OUTPUT**[[82.53682235 28.72656223 82.94049208]
 [ 6.47603388 51.03435714 23.30801068]]
```

## **Numpy 使用字符数组**

下面的代码将使您学习 NumPy 的字符数组用法的所有基础知识。

```
**## Character Numpy**In [9]:np.char.add("hi I a","m niice")***## Concatination***Out[9]:array('hi I am niice', dtype='<U13')In [10]:a = "hi it is riya"In [11]:np.char.capitalize(a) **## This capitalizes beginning of sentence.**Out[11]:array('Hi it is riya', dtype='<U13')In [16]:b = "lets say its me"
np.char.join('-',b)**## This method inserts the given character in the given array.**
Out[16]:array('l-e-t-s- -s-a-y- -i-t-s- -m-e', dtype='<U29')In [17]:np.char.title(a)
**## This capitalizes beginning of every word.**Out[17]:array('Hi It Is Riya', dtype='<U13')In [18]:m = np.char.upper(a)
**## This capitalizes every letter.**In [19]:mOut[19]:array('HI IT IS RIYA', dtype='<U13')In [20]:np.char.lower(a)
**## This turns every letter to lowercase.**Out[20]:array('hi it is riya', dtype='<U13')In [21]:np.char.split(a)
**## It splits every word.**Out[21]:array(list(['hi', 'it', 'is', 'riya']), dtype=object)In [40]:np.char.strip(a,'h')
**## It is used for removing beggining or last letter of word.**Out[40]:array('i it is riya', dtype='<U13')In [41]:np.char.strip(a,'a')Out[41]:array('hi it is riy', dtype='<U13')In [43]:np.char.splitlines("hello**\n**how it is going**\t** well")
**## It splits where there is next line.**Out[43]:array(list(['hello', 'how it is going\t well']), dtype=object)In [44]:np.char.replace(a,'i','k')
**## Used to replace some char with another one.**Out[44]:array('hk kt ks rkya', dtype='<U13')
```

# 数学运算

让我们看看 numpy 可以顺利完成的加法、减法、乘法、除法、平方根和数组的点积。

```
In [50]:x=np.array([[1,2],[3,4]])
y=np.array([[5,6],[7,8]])
print(x+y)
print(np.add(x,y))
print(x-y)
print(np.subtract(x,y))**OUTPUT**[[ 6  8]
 [10 12]]
[[ 6  8]
 [10 12]]
[[-4 -4]
 [-4 -4]]
[[-4 -4]
 [-4 -4]]In [51]:print(x*y)
print(np.multiply(x,y))**OUTPUT**[[ 5 12]
 [21 32]]
[[ 5 12]
 [21 32]]In [52]:print(x/y)
print(np.divide(x,y))
print(np.sqrt(x))**OUTPUT**[[0.2        0.33333333]
 [0.42857143 0.5       ]]
[[0.2        0.33333333]
 [0.42857143 0.5       ]]
[[1\.         1.41421356]
 [1.73205081 2\.        ]]***## Dot product / Matrix Multiplication***In [5]:print(x.dot(y))**OUTPUT**[[19 22]
 [43 50]] In [6]:print(np.dot(x,y))**OUTPUT**[[19 22]
 [43 50]]
```

我们也可以沿着特定的轴计算总和。在下面的示例中，您可以看到 sum 函数在计算一些行、列和所有元素时是如何有用的。

```
print(x) 
print(np.sum(x))
print(np.sum(x,axis=0))
print(np.sum(x,axis=1))**OUTPUT** [[1 2]
 [3 4]]
10
[4 6]
[3 7]
```

我们可以水平和垂直堆叠数组。默认情况下，下面代码中 axis 的值是 0(我们也可以指定它)。Axis = 0 按行堆叠数组，而 Axis =1 按列堆叠数组。

```
a=np.array([1,2,3,4])
b=b**2
np.stack((a,b))**OUTPUT** array([[ 1,  2,  3,  4],
       [ 1,  4,  9, 16]])np.stack((a,b),axis=1)**OUTPUT**array([[ 1,  1],
       [ 2,  4],
       [ 3,  9],
       [ 4, 16]])
```

我们可以根据需要的形状改变阵列的形状。

```
print(a.reshape((4,2)))[[ 1  2]
 [ 3  4]
 [ 1  4]
 [ 9 16]]
```

这些是 NumPy 的基础，但还有更多。你可以参考官方文档来获得更多的见解。

[](https://numpy.org/) [## NumPy

### 为什么是 NumPy？强大的 n 维数组。数值计算工具。可互操作。表演。开源。

numpy.org](https://numpy.org/) 

**感谢阅读。**