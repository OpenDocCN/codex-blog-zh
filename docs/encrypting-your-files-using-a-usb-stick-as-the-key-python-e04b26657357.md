# 使用 u 盘密钥加密文件(Python)

> 原文：<https://medium.com/codex/encrypting-your-files-using-a-usb-stick-as-the-key-python-e04b26657357?source=collection_archive---------2----------------------->

网络安全对我们的数字生活越来越重要，人们真的低估了他们的信息被窃取的容易程度。有鉴于此，为什么不用 python 建立一个你自己的很酷的小系统来加密你的文件呢！在这篇文章中，我将介绍如何在你的 windows pc 上创建一个安全的位置，这将加密你的重要文件，除非你插入一个 u 盘。

![](img/d6943e375b57a2aaf91b507becbff485.png)

现在我不会假装这是你能做的保护自己的最安全的事情，但每一点都有帮助，而且非常简单。你需要的只是一个 u 盘，最好上面什么都没有，但这不是必须的。

首先，你要插上你的 u 盘，确保它能正常工作，并清除所有无用或不想要的文件。当它出现在您的计算机上时，在侧边菜单中，右键单击它并单击属性。在这里你可以给它一个名字，在我的例子中，我就称它为“KEY”。在我们进入代码之前，你要做的另一件事是选择你要在你的计算机上保存这些安全文件的位置。我刚刚在桌面上做了一个名为 SecureData 的文件夹，又快又简单。

所以进入代码！这个项目需要三个库，其中两个需要 pip 安装。

```
pip install wmi
pip install cryptographyimport os
import wmi
from cryptography.fernet import Fernet
```

然后，您希望定义一个变量来保存您提供给 u 盘的字符串名称，并定义另一个变量来保存您的 secure files 文件夹的系统路径。

```
my_key = 'KEY'
files = r'YOUR PATH HERE'
```

现在我们实例化一个我们导入的“wmi”对象。这是允许我们扫描计算机端口的库。

```
c = wmi.WMI()
```

这就是我们现在需要的所有设置。下一部分被分解成四个不同的功能。首先是检查我们的“钥匙”是否插上了。

```
def check_for_key():
   for disk in c.Win32_LogicalDisk():
      if disk.VolumeName==my_key:
         return disk
```

非常简单容易。这只是迭代任何磁盘驱动器，包括您的内部磁盘，但只有当驱动器名称与您给它的名称匹配时才返回值。

加密过程的第一部分是我们需要生成一个唯一的加密密钥。这就是我们知道并理解的数据是如何被转换成完全的胡言乱语的。所以在这个函数中，我们想从我们的 u 盘中读取数据，看看是否已经有一个密钥，如果没有，就创建一个新的。要做到这一点非常简单，我们将使用一个“try”块来尝试和加载密钥，如果没有，我们将在“except”块中创建一个新的。

```
def load_key(usbDisk):
   port = usbDisk.DeviceID
   try:
      print('Trying to find key...')
      with open(f'{port}\\encryptionKey.key','rb') as encryptKey:
         key = encryptKey.read()
         print('Key Found')
   except:
      print('Key not found... Creating a new key')
      key = Fernet.generate_key()
      with open(f'{port}\\encryptionKey.key','wb') as encryptKey:
         encryptKey.write(key)
   return key
```

所以现在我们有了 USB 端口扫描仪和加密密钥来保护我们的数据。接下来我们需要做的两个函数是加密和解密函数。这些肯定可以浓缩成一个函数，但是为了简单起见，将它们分开更容易。

从加密功能开始:

```
def encryptFiles(key,directory):
   files = os.listdir(directory)
   cipher = Fernet(key)
   global state
   state = 'encrypted'
   for file in files:
      with open(f'{directory}\{file}','rb') as old:
         original = old.read()
      encrypted = cipher.encrypt(original)
      with open(f'{directory}\{file}','wb') as old:
         old.write(encrypted)
```

为了清楚起见，我们传入的参数是我们的密钥，然后目录是我们在开始时定义的安全文件文件夹的路径。变量 state 的全局声明后面会更清楚。然后，我们打开目录中的每个文件，并读取它。我们加密内容，然后再次以“写”模式打开文件，用加密的数据替换内容。

所以现在解密函数，几乎是一样的:

```
def decryptFiles(key,directory):
   files = os.listdir(directory)
   cipher = Fernet(key)
   global state
   state = 'decrypted'
   for file in files:
      with open(f'{directory}\{file}','rb') as old:
         encrypted = old.read()
      decrypted = cipher.decrypt(encrypted)
      with open(f'{directory}\{file}','wb') as old:
         old.write(decrypted)
```

这是我们唯一需要写的函数！我们只是在代码的底部增加了一个小部分，它将使用这些函数。

```
state = 'decrypted'
if __name__=='__main__':
   while True:
      disk = check_for_key()
      try:
         key = load_key(disk)
      except:
         print('No Key Available')
      if disk!=None:
         current_state = 'decrypted'
         if current_state!=state:
            decryptFiles(key,files)
         else:
            current_state = 'encrypted'
            if current_state!=state:
               encryptFiles(key,files)
```

因此，在这最后一部分，我们只是确保文件只有在“密钥”被插入或移除时才会被加密或解密，而不是不断地被加密或解密，这将给计算机带来真正的压力，并可能使文件无法打开。

值得注意的是，最后一部分的编码方式意味着当您开始运行脚本时，文件需要以正常格式(解密)存在，并且您的密钥已经插入。之后，它会完全按照你想要的那样工作。另一个注意事项是，并非所有文件类型都适合加密，它最适合纯文本格式和 csv 文件类型。特别是 PDF 文件可能会被破坏，所以要小心你使用它的文件，这是你的责任！

我希望您喜欢尝试加密文件，并且这是使用 python 保护重要数据安全的有益介绍！