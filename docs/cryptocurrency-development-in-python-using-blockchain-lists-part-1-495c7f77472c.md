# 使用区块链列表在 Python 中开发加密货币(第 1 部分)

> 原文：<https://medium.com/codex/cryptocurrency-development-in-python-using-blockchain-lists-part-1-495c7f77472c?source=collection_archive---------22----------------------->

![](img/f614d46135117e6f2d392ac81e7b186d.png)

图片来自 Pixabay 的 WorldSpectrum

随着区块链和加密货币的发展在过去几年中持续领先，越来越多的人希望开发自己的加密货币。这背后的原因各不相同；一个独特的创业公司的财务灵活性，奖励用户使用自己的新推出的软件，以及实践开发区块链的能力。

不管什么原因，本文将描述开发自己的加密货币的初始步骤。

Python 是一种动态类型语言；因此，这意味着变量只在运行时确定，而不是静态类型化。此外，Python 是一种强类型语言，因为所有变量在执行操作时都有一个类型——即使用户没有输入所述类型。

这是 Python 简单性的一个例子。Python 允许用一个简单的函数执行许多任务。因此，Python 是首次区块链开发的推荐语言。

然而，对于更开发的项目，我推荐使用 C++。与 Python 相比，C++允许操作指针和访问指针，这对于开发更复杂的链表是必不可少的。

然而，由于这是一个简单的介绍，Python 将足够好地工作。

在本文中，正在编写的代码将在本地执行。对于未来的文章，区块链将必须登录到一个系统或通过云，以允许其他人访问上述加密货币。

通过这样做，其他人将能够开采货币并将其添加到当前的区块链中。

代码将写在两个文件之间:

cryptoFunctions.py

mainBlock.py

mainBlock.py 文件有两个类:

*   块值
*   主要的

cryptoFunctions.py 文件没有类，但有多个定义。

其中包括以下内容:

*   getPreviousHash()
*   返回块()
*   setHash()
*   runData()
*   我的()
*   newTransactionData()
*   验证链()

我们要做的第一件事是导入必要的库和文件。

```
import time
import CryptoFunctions
```

时间导入用于块创建的时间戳。我们还必须导入加密函数，这样我们就可以使用该文件中包含的定义。

接下来，我们需要初始化全局变量。

```
diff = 3
nonce = 0
number = 1
arrayOfValues = []
currentArray = []
currentIndex = 0
previous_hash = "0" *64
```

上面列出的主要变量——diff——代表短语“难度”困难在于区块链试图寻找的散列偏移量处的 0 的数量。

当试图找到必要的散列时，数据将由停滞数据组成，例如挖掘开始的当前时间以及所述加密货币的发送者和接收者。

然而，能够继续操纵加密的是随机数的迭代。如果 nonce 发生更改，块的整体哈希将会更改。因此，随机数将需要递增，直到该块满足所需的难度。对于每个块，nonce 值将从 0 开始，这在全局变量中声明。

第三个全局变量——number——表示当前块。

其他四个全局变量只是占位符。

下一组全局变量将表示本地设置的“事务信息”。

```
senderData = ["Sender_One", "Sender_Two", "Sender_Three", "Sender_Four", "Sender_Five"]recipientData = ["Recieve_One", "Recieve_Two", "Recieve_Three", "Recieve_Four", "Recieve_Five"]amountData = ["1", "2", "3", "4", "5"]transactionIndex = 0
```

第一个全局变量表示“数据”,它将在本地表示通过加密货币发送的数据。这被表示为变量“senderData”

类似地，第二个变量— recipientData —保存本地设置的数据，模拟“收件人数据”的真实示例

下一个变量是“amountData ”,它表示发送方和接收方之间发送的加密货币的可能数量。

最后，transactionIndex 表示必须从哪里提取事务数据。

接下来，让我们初始化第二个文件 CryptoFunctions.py。

```
from hashlib import sha256
```

我们将应用的加密区块链的方法是 sha256，这是一种行业标准的加密方法。

接下来，我们将通过编写 BlockValue 类继续开发主文件。

BlockValue 类将有两个定义，“__init__”和“new_data”

```
class BlockValue:def __init__()def new_data()
```

每个函数的参数如下。

```
def __init__(self, index, transactions, timestamp, previous_hash, number, transactionIndexUpdated):def new_data(self):
```

*   index 参数将用于跟踪更大的数组“arrayOfValues”的索引
*   事务是一个占位符，但它是一个数组，由每个块的当前发送者、接收者和数字数据组成。
*   时间戳参数将用于标识形成的时间。
*   previous_hash 参数用于验证连续链。下一个链的 previous_chain 应该是与当前块 hash 相同的 hash。
*   number 参数用于标识当前被引用的块，不是根据索引值，而是根据数字。
*   transactionIndexUpdated 参数用于标识从哪个索引中提取事务数据。

在 __init__ 定义中，代码将设置如下:

```
def __init__(self, index, transactions, timestamp, previous_hash, number, transactionIndexUpdated):self.index = index
  self.transactions = transactions 
  self.timestamp = timestamp
  self.previous_hash = previous_hash
  self.nonce = 0
  self.number = number
  self.transactionIndex = transactionIndexUpdated
```

在 main 函数中，我们将传入这些基于自身的变量，以便在以后需要使用它们时可以提取它们。

BlockValue 类的第二部分— new_data() —将表示如下:

```
def new_data(self):
  timestamp = time.time()
  transactions = CryptoFunctions.newTransactionData(    
  senderData[self.transactionIndex],     
  recipientData[self.transactionIndex],
  amountData[self.transactionIndex], [])currentArray, number = CryptoFunctions.runData(self.index, 
  transactions, timestamp, previous_hash, nonce, self.number,     
  arrayOfValues,diff)

  return currentArray, number
```

*   时间戳函数将用于检索块完全公式化的时间。
*   事务将使用“newTransactionData”函数从 senderData、recipientData 和 amountData 中检索当前事务数据。

现在，我们需要检查 CryptoFunctions 文件中提供的各种函数。

第一个函数是“getPreviousHash(currentIndex，arrayOfValues)”，它接受参数“currentIndex”和“arrayOfValues”。

```
def getPreviousHash(currentIndex, arrayOfValues):
   if (currentIndex == 0):
      return "0" * 64
   else:
      array = arrayOfValues[currentIndex - 1]
      return array[1]
```

如果 currentIndex 等于 0，这意味着没有前一个块，因此返回初始块为“0”* 64。所有散列都用 64 个数字初始化。

如果存在前一个块，则可以在较大的“arrayOfValues”内的前一个块中的索引 1 处检索前一个散列这是因为哈希值存储在索引 1 处。

第二个函数将返回当前块。函数 returnBlock 将接受两个参数:arrayIndex 和 arrayOfValues。

```
def returnBlock(arrayIndex, arrayOfValues):
   arraySubsection = arrayOfValues[arrayIndex]     
   newValue =  ("Block Number: " + str(arraySubsection[0]) +      
   "\nHash: " + str(arraySubsection[1]) + "\nPrevious: "                
   + str(arraySubsection[2]) + "\nTransactional Data: " +  
   str(arraySubsection[3]) + "\nNonce: " + 
   str(arraySubsection[4]) + "\nTimestamp: " +      
   str(arraySubsection[5]) + "\n")
   return (newValue)
```

该函数用于返回每个块中的各种值。每个块以索引 0 处的块号组织，散列将在索引 1 处初始化，先前的散列将在索引 2 处陈述，交易数据(发送者、接收者和金额)将在索引 3 处陈述，随机数值将在索引 4 处初始化，并且块形成的时间戳将在索引 5 处呈现。

将要声明的第三个函数将用于返回接受五个参数的哈希值——事务、时间戳、previous_hash、nonce、
number——它将被初始化为“*args”

```
def setHash(*args):
   hashing_text = ""
   hashing = sha256()for arg in args: 
      hashing_text += str(arg)hashing.update(hashing_text.encode('utf-8'))
   return hashing.hexdigest()
```

这个函数要做的第一件事是初始化 sha256()加密方法。然后，通过迭代，它将加密通过参数推送的内容。

在结果散列值被更新之后，加密将被返回。

第四个函数将对正在开发的货币进行挖掘。哈希很可能不适合这个难度。因此，必须使用 mine 函数重复挖掘它。

接受的参数将是事务、nonce、hash、number、previous_hash、timestamp、currentIndex 和 diff。

```
def mine(transactions, nonce, hash, number, previous_hash, timestamp, currentIndex, diff):while (hash[:diff] != "0" * diff):
      nonce += 1
      hash = setHash(transactions,timestamp,previous_hash, nonce,    
      number)return (nonce, hash)
```

如前所述，可以增加 nonce 值来进一步改变散列以满足必要的难度。这就是在这个功能中发生的事情。

一旦遇到困难，就会返回适当的 nonce 和 hash 值。

将使用的第五个函数是将当前发送者、接收者和数据量设置为一个单一值。

函数 newTransactionData 将具有参数 sender、recipient、amount 和值。

```
def newTransactionData(sender, recipient, amount, values):

   values.extend([sender, recipient, amount])return values
```

第六个函数用于验证链内的块值。这个简单的函数将仅仅用于检查当前散列是否等于下一个块的先前散列。

将采用的参数是 arrayOfValues 和 lengthData。

```
def verifyChain (arrayOfValues, lengthData):
   if (lengthData > 1):        
      for x in range(lengthData-1):            
          currentArray = arrayOfValues[x]            
          secondaryArray = arrayOfValues[x+1]            
          if (currentArray[1] == secondaryArray[2]):                    
             continue            
          else:                
             return(False)     
   return (True)
```

在 CryptoFunctions 文件中，将要执行的主要函数是 runData。该函数将接受参数 currentIndex、transactions、timestamp、previous_hash、nonce、number、arrayOfValues 和 diff。

此函数用于组织各种变量，这些变量将用于制定 arrayOfValues。此外，该函数将用于组织散列和挖掘方法。

```
def runData(currentIndex, transactions, timestamp, previous_hash, nonce, number, arrayOfValues, diff):previous_hash = getPreviousHash(currentIndex, arrayOfValues)hash = setHash(transactions, timestamp, previous_hash, nonce, 
     number)nonce, hash = mine(transactions, nonce, hash, number,   
     previous_hash, timestamp, currentIndex, diff)arrayOfValues = [number, hash, previous_hash, transactions,     
     nonce, timestamp]number += 1return arrayOfValues, number
```

*   如 BlockValue 类所示，在检索事务数据后，BlockValue 函数将使用所有有效参数执行“runData”。
*   在这个定义中，出现的第一个方法是使用 getPreviousHash 方法提取 previous_hash 数据。
*   然后，使用得到的有效参数检索哈希。
*   哈希很可能不适合这个难度。因此，必须使用 mine 函数重复挖掘它。
*   在检索到 previous_hash、nonce 和 hash 值之后，可以更新 arrayOfValues。
*   对于先前的迭代，也应该更新块号。
*   最后，返回 arrayOfValues 和更新后的数字。

现在，让我们完成内瓦林娜的档案。

```
class main:for value in range(len(senderData)):

      genisisBlock = BlockValue(currentIndex, [], time.time(), 
      "0" * 64, number, transactionIndex)

      currentArray, number = genisisBlock.new_data()arrayOfValues.append(currentArray)print(CryptoFunctions.returnBlock(currentIndex,  
      arrayOfValues))currentIndex += 1transactionIndex += 1print(CryptoFunctions.verifyChain(arrayOfValues,   
   len(senderData)))
```

*   在主类中，我们将基于 senderData 范围内的值的数量进行迭代。
*   没有任何修改的主块将被设置在 genisisBlock 的条件下。
*   arrayOfValues 将追加 currentArray 以构成列表集合。
*   在每次迭代结束时，打印当前数组。

```
if __name__ == '__main__':
   main()
```

在开始时添加主初始化。

结论:

截至目前，这个程序允许我们运行一个本地制定的加密货币程序。因此，块可以被挖掘，并且所有需要的加密将被陈述。

然而，这个本地运行的程序之外的用户将无法挖掘货币。这将在未来的更新中进一步解释。

到目前为止，您可以在 GitHub 的这个位置看到这个程序:

[https://github.com/AliFakhry/Nevalinna](https://github.com/AliFakhry/Nevalinna)