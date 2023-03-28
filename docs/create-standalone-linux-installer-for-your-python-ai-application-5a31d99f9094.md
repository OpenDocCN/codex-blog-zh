# 为 Python GUI 应用程序创建独立的 Linux 安装程序

> 原文：<https://medium.com/codex/create-standalone-linux-installer-for-your-python-ai-application-5a31d99f9094?source=collection_archive---------3----------------------->

![](img/b6c4d051957bda8f13aaba3226d881f6.png)

有时你可能需要用 python 开发你的应用程序的图形用户界面，因为它提供了一种快速简单的方法来创建 GUI 应用程序。此外，创建 python GUI 提供了训练模型的机器学习(ML)推理的简单集成。因此，这节省了将 ML 模型投入生产的时间。

用 python 开发 GUI 应用程序有很多选择，Tkinter 是开发桌面应用程序最流行的库之一。然而，将 python 应用程序分发给最终用户是很困难的，因为出于保护知识产权或 Python 开发和最终用户环境之间可能的冲突，我们不希望将我们的源代码与应用程序一起分发。

设置 python 环境并不容易，尤其是对于非开发人员来说。设置 Python 环境的典型工作流如下所示。

*   安装特定版本的 Python
*   设置 pip
*   设置虚拟环境
*   设置应用程序源代码
*   安装依赖项

可能的选择是使用编译的字节码，但是生成**。pyc** 文件不能在不同版本的 python 之间移植，你仍然可以反编译 **pyc** 文件来进行逆向工程。使用像 [PyInstaller](http://www.pyinstaller.org/) 和 [Pyarmor](https://wiki.python.org/moin/Pyarmor) 这样的选项组合将在一定程度上帮助我们保护我们的代码。

# PyInstaller

提供了将 python 应用程序作为普通包安装程序分发的灵活性。它将 Python 应用程序及其所有依赖项打包到一个包中。用户可以运行打包的应用程序，而无需安装 Python 解释器或任何模块。PyInstaller 检查您的 Python 代码并找出依赖项，然后根据操作系统将它们打包成合适的格式。请查看[https://pyinstaller.readthedocs.io/en/stable/usage.html](https://pyinstaller.readthedocs.io/en/stable/usage.html)了解 PyInstaller 命令行中可用选项的更多细节。

# 皮甲

使用命令行工具帮助混淆 python 脚本。详细解释将在[https://wiki.python.org/moin/Pyarmor](https://wiki.python.org/moin/Pyarmor)中提供

# 制造自我

允许在 Unix 系统中创建自解压分发包。 [Makeself](https://makeself.io/) 是一种创建自解压档案的简单方法。

# 如何为 Python 应用程序创建分发包

## 应用程序结构

```
myapp
  |
  |-- __init__.py
  |-- myapp.py
  |-- anothermod.py
  |-- python files and other resources
  |__ **dist
  |     |myapp
  |__ makeself_stage
  |     |installer.sh
  |__ build.sh**
  | ..
```

应用程序文件夹包含应用程序所需的所有必要的 python 文件和资源。作为最佳实践，包创建逻辑所需的所有脚本都被分离到 **build.sh** 文件中。以下是 build.sh 的分步内容

*   在 bash 脚本中声明 Pyarmor 路径。这可以作为一个参数传递给脚本。如果您的系统中安装了 PyInstaller，Pyarmor 会自动检测它。

`PYARMOR` ="$HOME/。本地/bin/pyarmor "

*   在这个预处理步骤中，您可以构建和准备您的依赖模块。这包括将二进制文件和库复制到适当的文件夹中
*   现在用 pyarmor 生成分发包。Pyarmor 提供了“pack”选项，将混淆的脚本打包到包中。该选项使用 pyinstaller 创建软件包。此外，您可以使用选项“-e”将额外的选项传递给 pyinstaller

`${PYARMOR} pack --clean -e '--paths='path_to_search_imports:.' --hidden-import='PIL._tkinter_finder' --add-binary='path_to_lib.so:.' --exclude-module=modules_not_needed --add-data='./Config:Config' --add-data='./*.png:.'' **myapp.py**`

Pyarmor 默认在/dist 中创建捆绑包，您可以通过使用“-O 或— output”选项传递输出路径来更改它

*   最后一步是使用 makeself 从 pyarmor from /dist 文件夹创建的包文件夹中创建自解压归档文件

1.  设置 makeself

sudo apt-get 安装 makeself

2.在/dist 文件夹中创建有效负载或归档文件，以便创建自解压运行文件

`cd dist`

`tar -cvf myapp_1_0_0.tar -C myapp/ .`

3.创建安装脚本

这个安装脚本会将 myapp_1_0_0.tar 解压缩到目标的主目录中。根据不同的用例，可以修改这个脚本来满足需求。它可以是 python 脚本，但是为了简单起见，它被写成 bash 脚本。

```
**makeself_stage/installer.sh**This script should contains following main function install_file 

install_menu # to create short cut in system application menu
create_desktop_shortcut # desktop shortcut
create_uninstaller # finally uninstaller file in /usr/local/ directoryI am not writing all the logic for the creating menu shortcuts. Simple installation function should be likefunction install_file() 
{ 
   echo "Extracting the application package"
   if [ -d $HOME/myapp] 
   then
     echo "Application already installed." 
     exit 1
   else
     mkdir $HOME/myapp
   fi

   **tar -xvf ./****myapp_1_0_0.tar****.tar -C** **$HOME/myapp**
}
```

4.通过将归档文件复制到暂存目录来运行 makeself

激光唱片..

`cp dist/myapp_1_0_0.tar makeself_stage/`

`makeself ./makeself_stage/ myapp_1_0_0.run "sample package" ./`安装人员`.sh`

makeself 的语法如下:

`makeself [args] archive_dir file_name label startup_script [script_args]`

*   `archive_dir`是包含要归档的文件的目录的名称
*   `file_name`是要创建的档案的名称
*   `label`是描述包的任意文本字符串。它将在提取文件时显示。
*   `startup_script`是从提取文件目录中*执行的命令。*

每个论点的细节将在[https://makeself.io/](https://makeself.io/)提供

# 结论

这种解决方案可能不适用于每个人，但尽量做到通用。它允许 Python 开发人员创建和分发一个像 Linux 中的 C++程序一样的图形应用程序。这在 windows 中也可以实现，但是需要根据 Windows 环境调整脚本。你可能需要使用像 [Inno Setup](http://www.jrsoftware.org/isinfo.php) 这样的软件，而不是 makeself 来为 windows 构建安装程序。通过将 pycuda 包与应用程序捆绑在一起，AI 应用程序可以像这样轻松地分发。