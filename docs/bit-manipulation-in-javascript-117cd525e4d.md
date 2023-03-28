# JavaScript 中的位操作

> 原文：<https://medium.com/codex/bit-manipulation-in-javascript-117cd525e4d?source=collection_archive---------2----------------------->

![](img/0406f2d551aac87ca42fc07c3c6f113a.png)

位操作是一种改变或处理由另一种数据类型(通常是整数)表示的实际数据位的技术。给定一个像 7 这样的整数，它所代表的位是用二进制写的——也就是说，从左到右是一系列 1 和 0，代表加起来是整数的 2 的幂。

```
7 = 4 + 2 + 1, which means
7 = 2**2 + 2**1 + 2**0, so in binary, 
7 = 111
```

再举一个例子，甚至更大的数字也可以用二进制的比特来表示。二进制中的零表示不包括在总和中的 2 的幂，从右向左读取，从零幂开始。

```
645 = 2**9 + 2**7 + 2**2 + 2**0
645 = 1010000101
```

二进制位编码可以存储的位数是有限的。毫无疑问，许多操作系统中 32 位到 64 位的变化是熟悉的——这指的是最大存储容量。很容易假设 JavaScript 可以用二进制编码的最大数是 2 的 64 次方(加上 63 次方，再加上 62 次方……)。但是，由于 JavaScript 在技术上在后台将所有数字表示为双精度浮点数，并且必须用一位来表示符号，因此可以安全编码的最大和最小数字是

```
2**53 - 1, or the MAX_SAFE_INTEGER 9,007,199,254,740,991
-(2**53 - 1) or the MIN_SAFE_INTEGER -9,007,199,254,740,991
```

有了这些数学信息，我们可以用比特操作来做什么呢？编码面试问题之外的一些常见用途包括压缩和加密，因为使用算法或密钥的逐位技术可用于将数据“推入”更小的帧中，或转换未加密数据的位，以便只有给定适当的密码才能识别它们。

# 按位运算符

为了执行位操作，我们手头准备了一系列运算符。除了少数例外，这些对我来说都是全新的，直到最近，所以这个概述对每个人都有帮助。

```
~ NOT -> flips every bit to its opposite (e.g., 1 -> 0)
& AND -> compares two equal-length bit patterns; returns one in positions where both patterns have ones, otherwise returns zero
| OR -> also compares two equal-length bit patterns; returns one if either pattern has a one in that position, otherwise zero
^ XOR -> also compares two equal-length patterns; only returns one in positions where the patterns are different
<< LEFT SHIFT -> shifts the pattern a certain number of bits (given after the operator) and adds zeroes to the end; equivalent to multiplying the starting number by 2**n, where n is the number to shift
>> RIGHT SHIFT -> shifts the pattern a certain number of bits to the right and appends one at the end; equivalent to dividing by 2**n, where n is the number to shift
```

这些不是所有的位运算符；要了解更多信息，请查看此处的文档[。](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Expressions_and_Operators#bitwise_operators)

# 常见的位操作问题

最常见的位操作问题可能是检查一个数是否是 2 的幂(在 [Leetcode](https://leetcode.com/problems/power-of-two/) 上有一个现成的)。这个解决方案看起来很简单，但实际上一开始很难理解。

```
const isPowerOfTwo = n => {
  return (n && !(n & (n - 1)))
}
```

这是什么意思？我们知道我们正在返回一个布尔值。返回的前半部分检查 n 是否为真；也就是非零。这负责检查零是否是 2 的幂。第二部分利用我们的按位 AND 运算符来比较两个等长的位模式:n 和 n-1。让我们用一些例子更清楚地看看第二部分。

```
8 is a known power of 2
8 passes the first test; it is truthy since it is not zero
What about 8 & 7? Let's look at them both as binary code
8 = 1000 (2**3 + 0 + 0 + 0)
7 = 0111 (0 + 2**2 + 2**1 + 2**0)
So the operation 8 & 7 results in 0000 (falsey)
Therefore, doing !(8 & 7) gives us a truthy value
Our function returns true! 8 is a power of 2!
```

测试是这样进行的:所有 2 的幂在它们的二进制表示中只有一个 1——它们是 2 的幂的地方。但是，从 2 的幂中减去 1 会得到一个包含多个 1 的数字，从而导致两种模式的按位 AND 运算符的结果总是返回 false。因为我们检查这是否是假的，当我们的输入是 2 的幂时，我们得到一个真的布尔返回。

另一个常见的位操作问题是计算一个数的二进制表示中 1 的个数。这也被称为 Hamming weight，我觉得它非常有趣，因为它让人想起了县集市的画面，在那里，壮汉举起了一只巨大的蓝带猪，而不是锤子和铃铛。我跑题了。同样，可靠的 Leetcode 有一个准备好的[示例](https://leetcode.com/problems/number-of-1-bits/)。

![](img/04b7e965203e7fd6e519726d74820c58.png)

```
const hammingWeight = n => { 
  let count = 0;
  while(n){
    n = n & (n - 1)
    count++
  }
  return count
}
```

同样，一个看似简单的解决方案实际上很难理解。让我们再次用一些例子来分解它。

```
Find the Hamming weight of 103
First, we see our count is 0
Then, while n -> meaning whenever n is not zero
Next, we are setting n to now equal n & (n - 1) and then incrementing our count variable
For 103, the binary representation is 2**6 + 2**5 + 2**2 + 2**1 + 2**0, or 1100111
For 102, our initial n - 1, the binary would be 1100110
So on first loop, n & (n - 1) would be 1100110, and our counter iterates for the 1 that was in the 2**0 placeOn the next loop:
n = 1100110 (102)
n - 1 = 1100101 (101)
n = n & (n - 1) = 1100100 (100) -> counter is now up to 2Next loop:
n = 1100100 (100)
n - 1 = 1100011 (99)
n = n & (n - 1) = 1100000 (96) -> counter is now up to 3Next loop:
n = 1100000 (96)
n - 1 = 1011111 (95)
n = n & (n - 1) = 1000000 (64) -> counter is now up to 4Last loop: 
n = 1000000 (64)
n - 1 = 0111111 (63)
n = n & (n - 1) = 0 -> counter is now up to 5While loop stops because while(n) is no longer valid
Counter is returned (5) -> check for accuracy
103 was 1100111 -> 5 ones!
```

这种方法的关键在于，将一个数设置为该数的前一个值的按位 AND 运算符的结果，并且其递减量递增地“翻转”该数中每个位置右侧的所有位，直到该数为零，从而“移除”所有的 1。只要这个过程被一个变量跟踪，1 位的数目就可以被计数并返回。

希望这篇关于 JavaScript 中的位操作的简要介绍能够为编写某些算法提供新的信息和视角。我知道我仍然在努力掌握一些更高级的按位概念，所以一旦我更好地解决了这些问题，我可能不得不返回第二期。到目前为止，对我来说，关键是记住仔细绘制每一步，这样我就可以观察逐位运算创建的模式。在确定 2 的幂或计算 1 的位数的情况下，我试图重新创造我自己理解位运算的方式，以便其他人可以遵循这一思维过程。更多练习问题，请查看 [Leetcode](https://leetcode.com/tag/bit-manipulation/) 上的位操作标签。编码快乐！