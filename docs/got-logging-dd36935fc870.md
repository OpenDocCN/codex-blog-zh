# 有伐木吗？

> 原文：<https://medium.com/codex/got-logging-dd36935fc870?source=collection_archive---------10----------------------->

![](img/f32b4092869b020b90deba26e3a2dad9.png)

作者图片

您的 Python 项目中集成了日志记录功能吗？我不会试图让你相信拥有这样的功能的重要性；我会让你试一试再决定。

***日志*** 模块是标准 Python 库的一部分，如果设置得当，它可以帮助您调试代码，对代码的运行时特征进行分析，和/或收集代码执行过程中的信息。

有很多有用的资源解释了如何使用 ***日志*** 模块。可以先参考 [Real Python](https://realpython.com/python-logging/) 的或者[数字海洋](https://www.digitalocean.com/community/tutorials/how-to-use-logging-in-python-3)的；最后可以参考 Python 库网页中的[文档](https://docs.python.org/3/library/logging.html)。

我想和你分享一个我经常使用的实现。

# 密码

让我们从导入所需的包开始。

```
# import packages
import logging
from datetime import datetime
```

接下来，让我们定义类和类中的函数。

让我们假设你已经用文件名***manageprojectlogging . py***保存了这个类。从那时起，你需要做的就是初始化这个类并调用类中的函数。

下面是这个类中可用的函数及其用途。

*   ***configureProjectLogging:***此功能让您配置您的记录器对象名、日志文件名和日志级别；你可以在这里阅读更多关于不同级别的日志[。](https://docs.python.org/3/howto/logging.html)
*   ***logMessage:*** 这个函数让你在执行代码时记录重要的信息。您可以将此函数与***configureProjectLogging***一起嵌入到代码中的关键位置。例如，当你执行你的代码时，你想跟踪什么样的异常发生；您需要做的就是放置 configureProjectLogging 和 logMessage 函数
*   ***resetProjectLogs:***此功能永久删除之前会话的所有日志条目。

现在，让我们看看如何调用这些函数。您需要注意日志记录的不同级别，日志的重要性从左到右依次增加:调试、信息、警告、错误、严重。例如，**如果您将记录器配置为信息记录级别，并命令记录器在调试级别记录消息，则记录器不会这样做**；发生这种情况是因为您将记录器配置为只记录信息或更高重要级别的消息。当您调用如下所示的函数时，

*   根据您传递给 ***loggerFile*** 参数的名称自动生成一个日志文件，
*   您可以先使用***configureProjectLogging***配置记录器，然后调用 ***logMessage*** ，
*   您需要确保 ***loggingLevel*** 参数在两个函数上设置为相同的级别，或者在***configureProjectLogging***中设置为最低级别(您希望为您的项目记录日志),在您调用 ***logMessage*** 时为消息定义不同的更高级别。

```
from manageProjectLogging import manageProjectLogging# initialize the class
logger = manageProjectLogging()# construct message
_message = '** construct your message here **'# configure logger
logger.configureProjectLogging(loggerName = 'myProjectLogger',
                               loggerFile = 'myProjectLogs.log',
                               loggingLevel = 'INFO',
                               debugConfiguration = False) # log message
logger.logMessage(message = _message,
                  loggingLevel = 'INFO',
                  printBool = False)
```

在开始下一次执行代码之前，让我们看看如何重置日志文件以删除以前收集的日志条目。

```
from manageProjectLogging import manageProjectLogging# initialize the class
logger = manageProjectLogging()# reset log file
logger.resetProjectLogs()
```

除了以上所有内容，您有时可能会发现日志文件中的一些日志条目来自第三方包。您可能会发现配置这些第三方包的记录器并防止它们在您定制的项目日志中生成条目是很有用的。让我们看看如何为一个通常被称为 ***matplotlib*** 的第三方包实现这一点。

```
# configure third party package logging - matplotlib
logging.getLogger('matplotlib').setLevel(logging.CRITICAL + 1)
logging.getLogger('matplotlib').propagate = False
```

这样，您定制的项目日志记录程序的日志文件就不会包含来自其他包/模块的日志条目。

你可以在 GitHub 上找到这个[要点中的所有代码。](https://gist.github.com/troymyname/83d74999b06bbe5ba4f705303f41f1ec)

# 结论

在这篇文章中，您了解了如何为您的项目设置日志系统，如何配置日志系统，以及如何永久删除以前的日志条目。此外，您还了解了如何“静音”来自第三方包的日志条目。

我希望你在读完这篇文章后能学到一些重要的技巧。如果你想支持我，让我产生更多这样的内容，你可以[给我买杯咖啡](https://www.buymeacoffee.com/tonmoyroy)。谢谢大家！

# 参考

[1] Python。(未注明)。*日志记录 Python 的日志记录工具*。[https://docs.python.org/3/library/logging.html](https://docs.python.org/3/library/logging.html)

[2] Ajitsaria，Abhinav。(未注明)。*登录 Python* 。真正的 Python。https://realpython.com/python-logging/

[3]塔利亚费里，丽莎。(2017 年 5 月 2 日)。*如何在 Python 3 中使用日志记录*。数字海洋。[https://www . digital ocean . com/community/tutorials/how-to-use-log in-python-3](https://www.digitalocean.com/community/tutorials/how-to-use-logging-in-python-3)