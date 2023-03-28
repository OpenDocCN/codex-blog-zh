# 使用 Python 时自动锁定 PC

> 原文：<https://medium.com/codex/auto-locking-pc-when-youre-away-with-python-e184fe2b4bd5?source=collection_archive---------9----------------------->

完全披露，这不会是最安全的方式来锁定你的电脑，但它很酷。它也是在 windows pc 上设计的，但经过一些调整，在 linux 或 osx 上也能很好地工作。

这背后的想法是，我们可以选择任何设备作为我们的“钥匙”，当这个设备不再连接到您的家庭网络时，您的 pc 将自动锁定。在这个例子中，我将使用我的手机作为钥匙，所以如果我离开房子但忘记锁我的笔记本电脑，它会在我的手机与我的 WiFi 断开时自动锁定，非常简单！

![](img/91d852fa6b057349c3210b69c486e9d2.png)

这个小 python 项目再简单不过了，可以用不到 20 行代码完成！首先，我们只需要导入两个库，这两个库都内置于 python 中，因此不需要安装外部包，这是一个额外的好处。我们需要的两个包是‘subprocess’和‘ctypes’，它们让 python 可以访问 windows 命令提示符和系统进程。

```
import subprocess
import ctypes
```

接下来，您需要决定您希望哪个设备充当您的密钥。理想情况下，它应该是一个你可以随身携带的设备，这样手机就可以完美地工作，或者如果你有智能手表的话，也可以是智能手表，但这完全取决于你。

对于您选择的任何设备，您需要找出它在您的网络上的名称，最好的方法是登录到您的路由器，找到您想要使用的设备，然后在我们的 python 脚本中将它定义为一个变量。

```
my_device = 'Toms-iPhone'
```

我们还需要设置连接启动时的默认状态，这将是 False。

```
connected = False
```

所以现在我们需要进入使这一切成为可能的主函数，我们称之为‘check _ connection’。在这个函数中，我们将使用“子进程”包来编写 shell 命令，这些命令 ping 本地网络以找到我们想要找到的设备。函数的开始看起来像这样:

```
def check_connection():
   check = subprocess.run(f'ping {my_device}',
                          stdout=subprocess.PIPE,shell=True) check = check.stdout.decode('utf-8')
   state = check.split('\r')[2].split()
```

这部分代码所做的就是为匹配我们之前定义的名称的任何设备发送一个 ping，并返回 shell 输出。shell 输出是一个长字符串，所以我们对它进行解码，并将其拆分成更容易识别和管理的内容，这可以通过多种方式实现。

现在我们需要检查输出，看它是否在网络上找到了我们的设备，所以我们只需要一个 if 语句来检查输出是否说设备“不可达”最后。

```
if state[-1]=='unreachable.':
   connected = False
else:
   connected = True
return connected
```

这就完成了我们的网络检查！我们现在有了一个可以随时调用的功能来扫描网络寻找我们的“密钥”。

最后要添加的部分，是不断检查我们设备的连接状态，并使其在连接值为假时锁定 PC。为此，我们将简单地使用一个 WHILE 循环。

```
while True:
   device_state = check_connection()
   print(f'Last Device Connection State: {device_state}')
```

为了增加锁定能力，我们使用了“ctypes”库，以及一个 IF 语句来检查“device_state”何时为假。

```
if device_state == False:
   ctypes.windll.user32.LockWorkStation()
```

这就够了！完整的代码如下所示:

```
import subprocess
import ctypesmy_device = 'Toms-iPhone'
connected = Falsedef check_connection():
   check = subprocess.run(f'ping {my_device}',
                          stdout=subprocess.PIPE,shell=True)
   check = check.stdout.decode('utf-8')
   state = check.split('\r')[2].split()
   if state[-1]=='unreachble.':
      connected = False
   else:
      connected = True
   return connectedwhile True:
   device_state = check_connection()
   print(f'Last Device Connection State: {device_state}')
   if device_state = False:
      ctypes.windll.user32.LockWorkStation()
```

超级容易！只要在后台运行这个小脚本，就能让你的电脑更加安全，如果你出门时容易忘记锁电脑的话！

我希望你喜欢这个快速的 python 教程，这绝不是你可以用这个概念做的唯一的事情。也许你不在乎锁定你的电脑，所以你可以让你的电脑做任何事情，当你离开家！