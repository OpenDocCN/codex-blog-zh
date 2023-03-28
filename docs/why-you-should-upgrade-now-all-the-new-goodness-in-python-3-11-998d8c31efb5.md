# 为什么您应该现在升级:Python 3.11 中所有的新优点

> 原文：<https://medium.com/codex/why-you-should-upgrade-now-all-the-new-goodness-in-python-3-11-998d8c31efb5?source=collection_archive---------1----------------------->

![](img/a3397017a56dbfd1e384961c5b4906f0.png)

安德斯·吉尔登在 Unsplash[拍摄的照片](https://unsplash.com/s/photos/skyline?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

新的 Python 3.11 版本已经发布，带来了一些不错的新特性和功能。该概述列出了说服您升级 Python 的最重要的更改。

[新版本](https://docs.python.org/3.11/whatsnew/3.11.html#)带来了 Python 中异常和异常处理的改进，一个用于处理 TOML 文件的新模块，解释器的改进，新的类型和特性，同时也摒弃了该语言的一些旧模块和 API。此外，Python 基准测试声称 Python 3.11 比 Python 3.10 快 10-60%:你可以在这里看到结果。

# 如何安装和升级

## Linux 操作系统

在 Linux(本例中是 Ubuntu)上，您可以通过本地包管理器安装和更新 Python。你可以使用[死蛇 PPA](https://launchpad.net/~deadsnakes/+archive/ubuntu/ppa) 来安装最新版本的 Python。

```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:deadsnakes/ppa
```

添加存储库后，您可以通过 apt 安装 Python 3.11:

```
sudo apt-get update
sudo apt-get install python3.11
```

## 马科斯

在 macOS 上，我推荐使用软件包管理器[自制软件](https://brew.sh/)。您可以通过以下方式安装 homebrew:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

安装完 homebrew，就可以安装 Python 3.11 了:

```
brew install python@3.11
```

或者，如果您已经通过 homebrew 安装了早期版本的 Python(并更新了包管理器本身)，请通过以下命令将 Python 包升级到版本 3.11:

```
brew upgrade python -v 3.11
```

## Windows 操作系统

在 Windows 上，有两种方法可以更新 Python:通过 Python 安装程序或 [Chocolatey](https://chocolatey.org/) 包管理器。你可以在官方[下载页面](https://www.python.org/downloads/)上找到 Python 安装程序，它会引导你完成整个过程。如果您想使用软件包管理器 Chocolatey，您可以通过以下方式安装它:

```
Set-ExecutionPolicy Bypass -Scope Process -Force; \
iex **((**New-Object System.Net.WebClient**)**.DownloadString**(**'https://chocolatey.org/install.ps1'**))**
```

然后可以使用 Chocolatey 通过命令行安装 Python 3.11:

```
choco install python -y --version 3.11
```

或者升级 Python(如果已经安装):

```
choco upgrade python -y --version 3.11
```

# 例外组，除了*和注释

现在可以同时引发和处理多个不相关的异常。新引入的类型`ExceptionGroup`可以捆绑不相关的异常:

```
exceptions **=** ExceptionGroup(
    "all",
    [
        TypeError(1),
        ExceptionGroup("ex", [TypeError(2), ValueError(3)]),
        ExceptionGroup("os", [OSError(4)])
    ]
)
```

这些包给出了非常清晰的层次结构:

```
import traceback
traceback.print_exception(exceptions)| ExceptionGroup: all **(**3 sub-exceptions**)**
  +-+---------------- 1 ----------------
    | TypeError: 1
    +---------------- 2 ----------------
    | ExceptionGroup: ex **(**2 sub-exceptions**)**
    +-+---------------- 1 ----------------
      | TypeError: 2
      +---------------- 2 ----------------
      | ValueError: 3
      +------------------------------------
    +---------------- 3 ----------------
    | ExceptionGroup: os **(**1 sub-exception**)**
    +-+---------------- 1 ----------------
      | OSError: 4
      +------------------------------------
```

您可以在这些异常组上使用匹配条件，您可以对它们进行子类化，为处理所谓的叶异常的[构建自己的处理程序，等等。](https://peps.python.org/pep-0654/#handling-exception-groups)

Python 3.11 引入了一个新的(或增强的)关键字来更容易地处理异常组:`except*`。*字符应表示可以处理多个异常:

```
**try**:
    ...
**except*** CustomError:
    ...
**except*** OSError **as** e:
    ...
**except*** (TypeError, ValueError) **as** e:
    ...
```

[PEP-0654](https://peps.python.org/pep-0654/) 的描述为新的`except*`关键字提供了额外的[文档](https://peps.python.org/pep-0654/#except)，例如[递归匹配](https://peps.python.org/pep-0654/#recursive-matching)、[在 except*块中引发异常](https://peps.python.org/pep-0654/#raising-exceptions-in-an-except-block)或[链接](https://peps.python.org/pep-0654/#chaining)。

异常的基类`[BaseException](https://docs.python.org/3.11/library/exceptions.html#BaseException)`也成为了一个新方法。添加此方法是因为在捕获并重新引发异常时可以添加附加信息。

> `add_note(note)`
> 
> 将字符串`note`添加到异常注释中，该注释出现在异常字符串之后的标准回溯中。如果`note`不是字符串，则引发`[TypeError](https://docs.python.org/3.11/library/exceptions.html#TypeError)`。

# 欢迎新模块:tomllib

Tom 的显而易见的极简语言( [TOML](https://mostsignificant.github.io/python/2022/10/26/Tom's%20Obvious%20Minimal%20Language) )旨在成为一种极简易读的配置文件格式。语法类似于 INI 文件——但是它提供了一个实际的标准，而 INI 有多种风格。TOML 支持多种数据类型:字符串、整数、浮点、布尔、日期时间、数组和表格。因为它由键值对组成，所以它的结构可以轻松地解析为一个散列映射。以下是一个最小的例子:

```
*# this is a TOML config file example*title = "Config"[general]
name = "Database"
created = 2022-11-26T08:30:00+02:00[database] [database.connection]
  server = "192.168.1.1"
  ports = [ 8000, 8001, 8002 ] [database.auth]
  username = "root"
  password = "root"
```

现在 Python 3.11 在标准库中增加了一个模块来解析 TOML 文件，名为 [tomllib](https://docs.python.org/3.11/library/tomllib.html#module-tomllib) 。不幸的是，目前该模块只支持解析 TOML 文件，而不支持写入它们。如果你需要写功能，有两个备选方案:Tomli-W 包和 TOML 工具包。

但是，如果您只需要读取一个 TOML 文件，您可以使用标准库中的新模块。请注意，任何 TOML 文件都必须以二进制模式打开，这样`tomllib`才能在所有系统上正确处理 UTF-8 编码[。以下示例显示了如何解析上面的示例 TOML 文件:](https://peps.python.org/pep-0680/#types-accepted-as-the-first-argument-of-tomllib-load)

```
import tomllib**with** open("config.toml", "rb") **as** f:
    config **=** tomllib.load(f) server **=** config['database']['connection']['server']
    **for** port **in** config['database']['connection']['ports']:
        **print**(f'{server}:{port}')
```

您可以在 RealPython 的文章中找到使用`tomllib`模块的其他提示和技巧，例如指定一个 float 方法来控制浮点数的解析和表示方式。

# 解释器改进

Python 3.11 赶上了更现代编译器的能力，现在还会指出导致错误的[特定表达式](https://docs.python.org/3.11/whatsnew/3.11.html#whatsnew311-pep657)，而不仅仅是指向下面的行:

```
Traceback **(**most recent call last**)**:
  File "distance.py", line 11, **in** <module>
    print**(**manhattan_distance**(**p1, p2**))**
          ^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "distance.py", line 6, **in** manhattan_distance
    **return** abs**(**point_1.x - point_2.x**)** + abs**(**point_1.y - point_2.y**)**
                           ^^^^^^^^^
AttributeError: 'NoneType' object has no attribute 'x'
```

此外，还有一个新的命令行选项`-P`，它和一个名为`[PYTHONSAFEPATH](https://docs.python.org/3.11/using/cmdline.html#envvar-PYTHONSAFEPATH)`的环境变量一起，禁止自动为`[sys.path](https://docs.python.org/3.11/library/sys.html#sys.path)`预设潜在的不安全路径。这个特性可以集成到您的持续集成环境中，以确保安全性和可移植性。

> `-P`
> 
> 不要在`[sys.path](https://docs.python.org/3.11/library/sys.html#sys.path)`前添加潜在的不安全路径:
> 
> `python -m module`命令行:不要预置当前工作目录。
> 
> `python script.py`命令行:不要前置脚本的目录。如果是符号链接，则解析符号链接。
> 
> `python -c code`和`python` (REPL)命令行:不要在前面加上空字符串，这意味着当前的工作目录。
> 
> 参见`[PYTHONSAFEPATH](https://docs.python.org/3.11/using/cmdline.html#envvar-PYTHONSAFEPATH)`环境变量，以及`[-E](https://docs.python.org/3.11/using/cmdline.html#cmdoption-E)`和`[-I](https://docs.python.org/3.11/using/cmdline.html#cmdoption-I)`(隔离)选项。

# 更多类型和类型特征

新版本带来了新的打字功能。下面几节将简要介绍它们。

# 可变泛型

Python 3.5 已经为用单一类型参数化的泛型带来了`TypeVar`。Python 3.11 带来了`TypeVarTuple`，它允许用任意数量的类型进行参数化。以下示例来自 [PEP-646](https://peps.python.org/pep-0646/) ，展示了这一新功能的用法:

```
from typing import TypeVar, TypeVarTupleDType **=** TypeVar('DType')
Shape **=** TypeVarTuple('Shape')**class** **Array**(Generic[DType, *****Shape]): **def** **__abs__**(self) **->** Array[DType, *****Shape]: ...
    **def** **__add__**(self, other: Array[DType, *****Shape]) **->** Array[DType, *****Shape]: ...from typing import NewTypeHeight **=** NewType('Height', int)
Width **=** NewType('Width', int)x: Array[float, Height, Width] **=** Array()
```

# 将单个类型的直接项目标记为必需或非必需

现在可以标记`TypedDict`中的单个项目是否必须存在。默认情况下，为了向后兼容，所有字段仍然是必需的。然而，还有一个`total`参数可以设置为`False`:在这种情况下，默认情况下`TypedDict`的所有字段都不是必需的。

```
**class** **Movie**(TypedDict):
   title: str
   year: NotRequired[int]m1: Movie **=** {"title": "Black Panther", "year": 2018}  *# OK* m2: Movie **=** {"title": "Star Wars"}  *# OK (year is not required)* m3: Movie **=** {"year": 2022}  *# ERROR (missing required field title)*
```

# 自身类型

[PEP-673](https://peps.python.org/pep-0673/) 为返回类实例的方法引入了`Self`注释。下列范例显示替代建构函式的使用案例:

```
**class** **MyInt**:
    **@**classmethod
    **def** **fromhex**(cls, s: str) **->** Self:
        **return** cls(int(s, 16))
```

# 任意文字字符串类型

Python 3.11 引入了一个新的注释来增加字符串的安全性:`[LiteralString](https://docs.python.org/3.11/library/typing.html#typing.LiteralString)`。该注释允许函数接受任意的文字字符串类型，以及从其他文字字符串创建的字符串。您可以对敏感函数实施要求，例如那些执行 SQL 语句以防止 SQL 注入攻击的函数。

相应的 [PEP-675](https://peps.python.org/pep-0675/) 显示了该注释如何用于 SQL 查询:

```
**def** **run_query**(sql: LiteralString) **->** ...
    ...**def** **caller**(
    arbitrary_string: str,
    query_string: LiteralString,
    table_name: LiteralString,
) **->** None:
    run_query("SELECT * FROM students")       *# ok
*    run_query(query_string)                   *# ok
*    run_query("SELECT * FROM " **+** table_name)  *# ok
*    run_query(arbitrary_string)               *# type checker error
*    run_query(                                *# type checker error
*        f"SELECT * FROM students WHERE name = {arbitrary_string}"
    )
```

# 旧的必须去:折旧

以下传统标准库模块已被弃用，并将在 Python 3.13 中移除:

*   `aifc`
*   `chunk`
*   `msilib`
*   `pipes`
*   `telnetlib`
*   `audioop`
*   `crypt`
*   `nis`
*   `sndhdr`
*   `uu`
*   `cgi`
*   `imghdr`
*   `nntplib`
*   `spwd`
*   `xdrlib`
*   `cgitb`
*   `mailcap`
*   `ossaudiodev`
*   `sunau`

此外，`asynchat`、`asyncore`和`smtpd`模块(已经被弃用)已经更新，注意它们将在 Python 3.12 中被移除。此外，`lib2to3`包和`2to3`工具现在已经过时，可能无法解析 Python 3.10 或更新版本。未记录的模块`sre_compile`、`sre_constants`和`sre_parse`现在也不推荐使用。

[Py_UNICODE](https://docs.python.org/3.11/c-api/unicode.html#c.Py_UNICODE) 编码器 API 已经被[移除](https://docs.python.org/3.11/whatsnew/3.11.html#whatsnew311-pep624)，因为它们已经被弃用，并且因为有更好、更有效的替代方案而不再被使用。如果您仍在使用这些 API，相应的 [PEP-624](https://peps.python.org/pep-0624/) 提供了一个迁移指南。

一些宏已经被转换成静态内联函数，以避免[宏陷阱](https://gcc.gnu.org/onlinedocs/cpp/Macro-Pitfalls.html)。您可以在相关的 [PEP-670](https://peps.python.org/pep-0670/) 中找到关于这些宏的更多信息。

# 结论

新的 [Python 版本 3.11](https://docs.python.org/3.11/whatsnew/3.11.html#) 带来了许多新的好东西:更好的性能、更多的异常处理可能性、解析 TOML 文件的新模块、解释器的改进、附加的类型、注释和类型特性，以及对一些旧语言包袱的摒弃和移除。

自己决定，这是否值得升级，但最重要的是:继续编码，继续创造！