# 关于本地 Scala REPL 上的制表符补全的一个小提示

> 原文：<https://medium.com/codex/a-quick-little-note-about-tab-completion-on-the-local-scala-repl-7850f332edfa?source=collection_archive---------8----------------------->

## 药典

![](img/f1ef61e03170ace7fa94bd55d70d287a.png)

照片由[克里斯蒂安·休姆](https://unsplash.com/@christinhumephoto?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄

在 IntelliJ IDEA 这样的集成开发环境(IDE)上编程 Scala 时，完成建议非常有用。像本地 Scala REPL 这样的 REPL(读取-评估-打印-循环)是快速尝试可行或不可行的方法。

糟糕的是，在本地 Scala REPL 上没有完成建议。其实有…

它需要按一个键，特别是 Tab 键。这有点像在 Eclipse 和 NetBeans 上用 Ctrl-或 Command-Space 完成。

嗯，这取决于你的设置。显然，在某些设置中，本地 Scala REPL 可以正常工作，但缺少制表符补全(我写“本地 Scala REPL”是为了区别于像 [Scastie](https://scastie.scala-lang.org/) 这样的在线 REPL)。

制表符结束在你的系统上工作吗？我们很快就会知道了。让你的本地 Scala REPL 运行起来如果你还没有启动并运行它，用任何东西做一些计算。

```
C:\Users\AL>scala
Welcome to Scala 2.13.1 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_281).
Type in expressions for evaluation. Or try :help.scala> Long.MaxValue : BigInt
res0: BigInt = 9223372036854775807scala> res0 * 2
res1: scala.math.BigInt = 18446744073709551614
```

好的，目前为止，一切顺利。但是我忘记了我可以在`BigInt`实例上使用什么函数和过程。因此，我键入“`res1`”，后跟一个句点，然后按 Tab 键。

```
scala> res1.
%    <     bigInteger   doubleValue       isValidByte     longValue      setBit        toChar     unary_~
&    <<    bitCount     equals            isValidChar     lowestSetBit   shortValue    toDouble   underlying
&~   <=    bitLength    flipBit           isValidDouble   max            sign          toFloat    until
*    >     byteValue    floatValue        isValidFloat    min            signum        toInt      |
+    >=    charValue    gcd               isValidInt      mod            testBit       toLong
-    >>    clearBit     hashCode          isValidLong     modInverse     to            toShort
/    ^     compare      intValue          isValidShort    modPow         toByte        toString
/%   abs   compareTo    isProbablePrime   isWhole         pow            toByteArray   unary_-scala> res1.
```

注意，Scala REPL 填充了您在调用制表符结束之前已经输入的内容，因此您不必再次输入。但是你仍然需要输入你从建议中选择的内容。

我要去试试`bitLength()`和`gcd()`:

```
scala> res1.gcd(res0)
res3: scala.math.BigInt = 9223372036854775807scala> res1.bitLength
res4: Int = 64
```

我觉得跳过`res2`有点意思。你可能会觉得有点意思，也可能不会。

嗯，那个`underlying()`功能是干什么的？

```
scala> res1.underlying
res5: java.math.BigInteger = 18446744073709551614
```

哦，对了，它检索底层 Java `BigInteger`。

如果我知道我想要的东西的前几个字母，我可以缩小建议的范围，例如“是”函数:

```
scala> res1.is
isInstanceOf      isValidByte   isValidDouble   isValidInt    isValidShort
isProbablePrime   isValidChar   isValidFloat    isValidLong   isWholescala> res1.is
```

和之前一样，Scala REPL 填充了我们之前输入的内容，所以我们不需要再次输入。

这并没有告诉我们这些函数采用什么参数，但是因为我们是在 REPL 中，最糟糕的事情可能是我们得到一个错误消息。

```
scala> res1.isProbablePrime
            ^
       error: missing argument list for method isProbablePrime in class BigInt
       Unapplied methods are only converted to functions when a function type is expected.
       You can make this conversion explicit by writing `isProbablePrime _` or `isProbablePrime(_)` instead of `isProbablePrime`.scala>
```

我们还可以通过按 Tab 键两次来扩大建议的范围，这样完成建议也包括从超类继承而没有覆盖的项，比如从`java.lang.Object`继承`wait()`。

```
scala> res1.
%    <     bigInteger   doubleValue       isValidByte     longValue      setBit        toChar     unary_~
&    <<    bitCount     equals            isValidChar     lowestSetBit   shortValue    toDouble   underlying
&~   <=    bitLength    flipBit           isValidDouble   max            sign          toFloat    until
*    >     byteValue    floatValue        isValidFloat    min            signum        toInt      |
+    >=    charValue    gcd               isValidInt      mod            testBit       toLong
-    >>    clearBit     hashCode          isValidLong     modInverse     to            toShort
/    ^     compare      intValue          isValidShort    modPow         toByte        toString
/%   abs   compareTo    isProbablePrime   isWhole         pow            toByteArray   unary_-scala> res1.
!=   /    ^              compare       gcd               isValidFloat   mod          sign           toFloat      **wait**   
**##**   /%   abs            compareTo     **getClass**          isValidInt     modInverse   signum         toInt        |      
%    <    **asInstanceOf**   doubleValue   hashCode          isValidLong    modPow       **synchronized**   toLong       ?      
&    <<   bigInteger     ensuring      intValue          isValidShort   ne           testBit        toShort             
&~   <=   bitCount       eq            **isInstanceOf**      isWhole        notify       to             toString            
*    ==   bitLength      equals        isProbablePrime   longValue      notifyAll    toByte         unary_-             
+    >    byteValue      flipBit       isValidByte       lowestSetBit   pow          toByteArray    unary_~             
-    >=   charValue      floatValue    isValidChar       max            setBit       toChar         underlying          
->   >>   clearBit       formatted     isValidDouble     min            shortValue   toDouble       untilscala> res1.
```

我发现这对于标准 Java 和 Scala 类以及我编写的类都很方便。

有时，您可能会根据您的 REPL 用法决定，您不喜欢您为某个特定函数指定的名称。您可以继续使用 IntelliJ 并更改函数的名称。如果您这样做，请记住，您必须退出并重新启动 REPL，以使更改生效。