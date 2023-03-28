# Python 和物理学:矩阵数学小抄

> 原文：<https://medium.com/codex/python-and-physics-matrix-math-cheat-sheet-1209f4e71e93?source=collection_archive---------10----------------------->

![](img/82d1dc738d1e1bd8a404193a35ea7e69.png)

约书亚·雷德科普在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

随着 STEM 课程的深入，一个常见的数学主题不断出现，那就是矩阵。在本文中，我们将通过一些例子来说明如何使用 Python 来解决一些常见的矩阵数学问题。

# **直入代码**

首先，我们应该导入一些包来帮助我们处理一些更复杂的主题，比如特征值和行列式。虽然它们可以通过冗长的计算来解决，但这些软件包将帮助我们更快地处理事情。

```
import numpy as np
from numpy.linalg import det
from numpy.linalg import eig
from numpy.linalg import inv
```

**标量乘法**

首先，让我们从一些简单的乘法开始，比如从正数变成负数。

```
a = np.reshape(([2, 3, 4, 5]), (2,2)) #Making a 2x2 matrix
print(a)##Output
[[2 3]
 [4 5]]
```

要相乘，我们只用星号。

```
symbol_change = -1 * a
print(symbol_change)##Output
[[-2 -3]
 [-4 -5]]
```

乘以一个常数，我们做同样的事情。

```
constant_multiplication = 2 * a
print(constant_multiplication)##Output
[[ 4  6]
 [ 8 10]]
```

**矩阵乘法/点积**

现在我们从矩阵乘法/点积开始。这次我们将尝试不同维数的矩阵。

```
b = np.reshape([1,2,3,4,5,6],(2,3))#Matrix with 2 rows and 3 columns
print(b)
print('----')
c = np.reshape([7,8,9,10,11,12],(3,2))#3 rows and 2 columns matrix
print(c)##Output
[[1 2 3]
 [4 5 6]]
----
[[ 7  8]
 [ 9 10]
 [11 12]]
```

虽然 Numpy 确实有一个 *matmul* 函数，但是 Python 使用@符号来表示矩阵乘法。

```
matrix_dot_product = b@c #@ symbol does our matrix multiplication
print(matrix_dot_product)##Output
[[ 58  64]
 [139 154]]
```

**矩阵求逆**

使用我们之前导入的包 *inv* ，我们可以很容易地对矩阵求逆。为了验证它是否有效，我们将把我们的逆矩阵与原始矩阵相乘，看它是否形成单位矩阵。需要注意的是，由于 Python 处理的精度，单位矩阵的值有时可能是非零的，所以如果您正在进行某些需要精度的计算，请注意一点。

```
print(a)
inverse = inv(a)
print('---')
print(inverse)
print('---')
identity = a@inverse
print(identity)##Output
[[2 3]        #Original
 [4 5]]
---
[[-2.5  1.5]  #Inverse
 [ 2\.  -1\. ]]
---
[[1\. 0.]       #Verified that it does output our identity matrix
 [0\. 1.]] 
```

**决定因素**

使用我们导入的另一个包，det 将帮助求解决定因素。对于这个例子，我们将通过一个 2x2 矩阵和一个 3x3 矩阵。

```
b = np.reshape([4,6,3,8],(2,2))
print(b)
determinant = det(b)
det_format = float("%.2f" % det(b))
print('---')
print(determinant,'/',det_format),
c= np.reshape([6,1,1,4,-2,5,2,8,7],(3,3))
print('---')
print(c)
print('---')
det_c = det(c)
print(det_c)##Output
[[4 6]
 [3 8]]                       #Original 2x2 matrix
---
14.000000000000004 / 14.0     #Determinants, second value formatted
---
[[ 6  1  1]
 [ 4 -2  5]
 [ 2  8  7]]                  #Original 3x3 matrix
---
-306.0                        #Determinant
```

如前所述，Python 的精度有时会给我们带来意想不到的值，比如这个行列式。代码行 *det_format* ，我只是使用 Python 的字符串格式来保持小数点后两位，同时使用 *float* 来确保它不是字符串值。

**求解 Ax = B**

我们一直在使用的函数的一个更实际的用途是求解一个方程组。通过将它们转换成 **Ax=B** 的形式，我们可以通过将两边乘以 *A* 的倒数来求解 *x* 。由于 *A* 的逆矩阵就是单位矩阵，为了求解 *x* ，我们只需将 *A* 的逆矩阵乘以 *B* 。

```
a = np.reshape([1,1,1,0,2,5,2,5,-1],(3,3))
print(a)
print('---')
b = np.reshape([6,-4,27],(3,1))
print(b)
print('---')
solving_x = inv(a) @ b
print(solving_x)##Output
[[ 1  1  1]
 [ 0  2  5]
 [ 2  5 -1]]    #Our A
---
[[ 6]
 [-4]
 [27]]          #Our B
---
[[ 5.]
 [ 3.]
 [-2.]]         #Our X
```

为了验证，我们将 *A* 乘以 *x* ，看看我们是否得到了 *B* 。

```
print(a@solving_x)##Output
[[ 6.]
 [-4.]
 [27.]]   #Works!
```

**转置和共轭**

对于转置，我们利用 Numpy 的内置命令 *T* 。

```
print(a)
print('---')
transpose = a.T #All we need to get the transpose of a Numpy array
print(transpose)##Output
[[2 3]
 [4 5]]
---
[[2 4]
 [3 5]]    #Switched out values over the diagonal
```

对于共轭，我们使用 Numpy 的共轭命令，我们可以完全键入*共轭*或者只使用 *conj* 。为了表示我们的虚数，我们用 *j* 代替最常见的 *i* 。

```
#j for our imaginary number, 1j is equal to i.
a = np.reshape([1+1j,-2+1j,3+-1j,4+-1j],(2,2)) 
print(a)
print('---')
conjugate = np.conj(a)
print(conjugate)##Output
[[ 1.+1.j -2.+1.j]
 [ 3.-1.j  4.-1.j]]
---
[[ 1.-1.j -2.-1.j]
 [ 3.+1.j  4.+1.j]]   #Changed the sign of our imaginary numbers 
```

**特征值和特征向量**

此外，我们将使用我们导入的最后一个包， *eig* 。特征值给出的很简单，但是对于特征向量，我们将不得不做一些改动。

```
a = np.reshape([-6,3,4,5],(2,2)) #2x2 matrix
print(a)
print('---')
values, vectors = eig(a) #Values and vectors given by eig function
print(values)
print('---')
print(vectors)##Output
[[-6  3]                      #Original Matrix
 [ 4  5]]
---
[-7\.  6.]                     #2 eigenvalues
---
[[-0.9486833  -0.24253563]    #2 Vectors, each vector is a column
 [ 0.31622777 -0.9701425 ]]
```

如果我们使用 *vectors[0]* 来获得我们的第一个向量，我们将获得第一行，这将不是第一个向量。为了得到第一个向量，这就是我做的。

```
#Assigning the first values of the rows as the vector
vector_one = np.reshape([vectors[0][0],vectors[1][0]],(2,1))
print(vector_one)##Output
array([[-0.9486833 ],
       [ 0.31622777]])
```

检查它们是否是特征值和特征向量，我们首先将原始矩阵乘以向量，看看它是否与特征值乘以向量得到的向量匹配。

```
print(a@vector_one) #Matrix multiplied by vector
print(values[0]*vector_one) #First eigenvalue by the vector##Output
[[ 6.64078309]
 [-2.21359436]]
[[ 6.64078309]
 [-2.21359436]]             #They match!
```

现在你知道了！一个很好的 Python 示例集，可以帮助那些需要创建处理矩阵的程序的人。还有很多其他的概念需要研究，比如 trace 和正交性等等，但是对于大多数需求来说，这应该是朝着正确方向迈出的一步！希望你喜欢这个！