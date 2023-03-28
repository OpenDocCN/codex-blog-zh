# Python 中 R 的路径处理函数

> 原文：<https://medium.com/codex/how-i-wrote-a-python-package-to-imitate-rs-nice-and-neat-path-handling-functions-c6c45eccea9?source=collection_archive---------8----------------------->

## 以类似 R 的方式统一 Python 的路径处理函数。

![](img/d349ac6bc8d82d055c1c04911a1b6725.png)

照片由 [Birgit 从](https://www.pexels.com/@birgit-held-392791?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) [Pexels](https://www.pexels.com/photo/aerial-photo-of-building-1046125/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) 举行

[base R 中的路径处理函数](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-r-4459ba21e411)是内置的，干净而直观，而 Python 的路径处理函数[是跨三个不同的标准包(](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a) `[os](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a)` [、](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a) `[pathlib](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a)` [和](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a) `[shutils](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a)` [)](https://gwang-jin-kim.medium.com/useful-path-handling-functions-in-python-657296fd445a) 分布的、重复的、略有不同的。

把它们统一成一个 Python 包怎么样？

我们姑且称这个套餐为`rpath`。让我们一起创建一个包，同时在本文中浏览 R 和 Python 的路径处理命令。

# 使用诗歌设置包主干

诗歌使 Python 中的包创建和部署变得容易，提供了高度的可再现性。

我们使用诗歌的安装脚本来安装诗歌:

```
$ curl -sSL https://raw.githubusercontent.com/python-poetry/poetry/master/get-poetry.py | python3
```

重要提示:不要使用`$ pip install poetry`。他们还在`poetry`的网站上警告不要通过`pip`安装。

通过以下方式检查安装是否正确:

```
$ poetry --version
```

现在让我们安装一个诗歌项目(包):

```
$ poetry new rpath
## Created package rpath in rpath
```

让我们通过以下方式进入包的文件夹:

```
$ cd rpath
```

用你选择的编辑器打开`pyproject.toml`文件，比如 gedit(在 Ubuntu 中)。`.toml`文件是`poetry`包的配置文件。请随意选择除 gedit 之外的任何其他编辑器:

```
$ gedit pyproject.toml
```

我们将文件写入/完成到:

```
[tool.poetry]
name = "rpath"
version = "0.1.0"
description = "R's path handling functions for Python"
authors = ["Gwang-Jin Kim <[gwang.jin.kim.phd@gmail.com](mailto:gwang.jin.kim.phd@gmail.com)>"][tool.poetry.dependencies]
python = "^3.9"[tool.poetry.dev-dependencies]
pytest = "^5.2"[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"
```

并保存更改。

我们检查`toml`文件的正确性:

```
$ poetry check
## All set!
```

然后，我们用我们最喜欢的编辑器创建并打开`main.py`文件，并在那里编写我们的代码。

```
$ YOUR_EDITOR rpath/main.py # in my case `gedit`
```

并在那里写道:

```
import os
import pathlib
import shutil
```

现在我们已经准备好为这个包编写函数了。

# 1.如何在 R 和 Python 中获取/设置当前目录

在 R 中，这将是:

```
getwd()
setwd("/to/dir")
```

在 Python 中，我们有:

```
# get the current directory:
os.path.abspath(os.getcwd())  
# os.path.abspath() helps to deal with Windows path peculiarities
os.path.realpath('.')         # eliminates symbolic links
                              # and replace them by the real path
pathlib.Path.cwd()
pathlib.Path(__file__).resolve() # resolve to absolute path# set the current directory:
os.chdir("/to/dir") 
# no corresponding in pathlib or shutil
```

所以我们写成`rpath/main.py`:

```
def getwd():
    return os.path.realpath(os.path.abspath(os.getcwd()))def setwd(path):
    return os.chdir(path)
```

我们可以用 Python 调用:

```
rpath.getwd()rpath.setwd(path)
```

# 2.如何在 R 和 Python 中解析和访问路径名组件

在 R 中，我们有:

```
# parent directory name
dirname(path)# file name
basename(path)# file extension ## note, the following functions are not base R functions
## you have to install the packages 
## preceding `::` 
## e.g. by `install.packages("tools") 
## or "rio" or "xfun"
## e.g. 'csv' for files ending with '.csv'
tools::file_ext(path) # or:
rio::file_ext(path)   # or:
xfun::file_ext(path)# split a path into its components # how to dissect path folder and file names
# "/a/b/c.txt" => c("a", "b", "c.txt")
split_path <- function(path) {
  if (dirname(path) %in% c(".", path)) return(basename(path))
  return(c(basename(path), split_path(dirname(path))))
}
# from [here](https://stackoverflow.com/questions/29214932/split-a-file-path-into-folder-names-vector)
```

在 Python 中:

```
# parent directory:
os.path.dirname(path)# file name:
os.path.basename(path)# file extension:
filename, file_extension = os.path.splitext(path)# file extension:
filename, file_extension = os.path.splitext(path)
```

所以我们可以理解它:

```
def dirname(path):
    return os.path.dirname(path)def basename(path):
    return os.path.basename(path)def file_ext(path):
    return os.path.splitext(path)[1]
```

# 3.如何在 R 和 Python 中连接路径组件

在 R 中，我们有:

```
file.path("/a", "b", "file.txt") ## "/a/b/file.txt"
```

在 Python 中，我们必须选择:

```
os.path.join("/dir", "dir", "file.txt")                                                
pathlib.Path("/dir") / "dir" / "file.txt"                                             
pathlib.PurePath("/dir", "dir", "file.txt")                                           
# make Path() objects to string using `str()` 
# or their `.as_posix()` method
# they all create "/dir/dir/file.txt"
```

因此，要在 Python 中使用`file.path()`，我们需要:

```
def file_path(*args):
    return os.path.join(*args)
```

但是我也喜欢`/`超载…

```
Path = pathlib.Path
```

那么，我们就可以使用:

```
rpath.file_path("/a", "b", "c.txt") ## "/a/b/c.txt"
```

而且:

```
str(rpath.Path("/a") / "b" / "c.txt") ## "/a/b/c.txt"
```

# 4.如何在 R 和 Python 中递归列出文件夹中的文件

在 R 中，这将是:

```
# folders
list.dirs(path = ".", full.names = TRUE, recursive = TRUE)# files
list.files("/to/dir", pattern="\\.csv", full.names=TRUE, recursive=TRUE) ## pattern is regex
```

在 Python 中，我们有:

```
pathlib.Path("/dir").glob("**/*.csv") 
# recursivity through wildcards `**`!
```

我们可以通过以下方式在 Python 中提供 R 函数:

```
def list_dirs(path, pattern = "*", full_names=True, recursive=True):
    pattern = "**" + os.sep + pattern if recursive else pattern
    res = [p for p in pathlib.Path(path).glob(pattern) \
                if os.path.isdir(p)]
    if full_names:
        return res
    else:
        return [basename(p) for p in res]def list_files(path, pattern="*", full_names = True, recursive=True):
    pattern = "**" + os.sep + pattern if recursive else pattern
    res = [p for p in pathlib.Path(path).glob(pattern) \
                if os.path.isfile(p)]
    if full_names:
        return res
    else:
        return [basename(p) for p in res]## `pattern` is here not regex pattern, but bash file path pattern of `glob`
```

# 5.如何在 R 和 Python 中测试文件和文件夹的存在

在 R 中，这是通过以下方式解决的:

```
# folders
dir.exists("/to/dir")                # or file.exists("/to/dir")# files
file.exists("/to/file.txt")
```

在 Python 中，我们使用其中之一:

```
# for both:
pathlib.Path("/to/dir/or/file.txt").exists()# for folders
pathlib.Path("to/dir").is_dir()
os.path.isdir("to/dir")# for files
pathlib.Path("to/file.txt").is_file()
os.path.isfile("to/dir")# there is also
os.path.islink(path)
```

所以我们可以总结为:

```
def dir_exists(path):
    return os.path.isdir(path)def file_exists(path):
    return os.path.isfile(path)
```

# 6.如何在 R 和 Python 中创建新的文件夹和文件

在 R 中，我们使用:

```
# folders
dir.create("/to/new/dir", recursive=TRUE)# files
file.create("/to/file.txt")
```

在 Python 中对应于:

```
pathlib.Path("/to/new/dir").mkdir(exist_ok=True, parents=True)
os.makedirs("/to/new/dir", exist_ok=True)# create a file (touch)
pathlib.Path("/to/new/file.txt").touch()
```

因此，我们用以下公式取代 R 的语法:

```
def dir_create(path, recursive=True):
    os.makedirs(path, exist_ok=True)
    print(f"Created {path}")def file_create(path):
    pathlib.Path(path).touch()
    print(f"Created {path}")
```

# 7.如何在 R 和 Python 中(递归地)删除文件和文件夹

在 R 中，我们通过`unlink()`函数删除:

```
# folders
unlink("/to/dir", recursive=TRUE)# files
unlink("/to/file.txt")
```

Python 中的`unlink()`也是这样:

```
# for both
os.unlink("/to/dir/or/file.txt")
pathlib.Path("/to/dir/or/file.txt").unlink()# rmdir (however, I never liked rmdir - it deletes only empty folders)
os.rmdir(dir_path, missing_ok=True)
```

所以，这很简单:

```
def unlink(path):
    os.unlink(path)
```

# 8.如何在 R 和 Python 中复制包含内容和文件的文件夹

在 R 中，我们可以:

```
# copy folder architecture recursively (not keeping original dates)
dir.create('/to/dir', recursive=TRUE) # subsequently followed by:
file.copy("/from/dir/or/file.txt", "/to/dir", recursive=TRUE, copy.date=FALSE)
```

在 Python 中，我们有:

```
# copy a folder recursively
shutil.copytree(src="/from/dir", *dst="/to/dir"*, 
                *symlinks=False*, 
                *ignore=None*, 
                *copy_function=shutil.copy2*, 
                *ignore_dangling_symlinks=False*, 
                *dirs_exist_ok=False*)# copy a file
shutil.copyfile("/from/file.txt", 
                "/to/file.txt",        # must be a complete file
                follow_symlinks=True)            
shutil.copy("/to/file.txt", "/new/dir/file.txt") 
# only Python3.8+ # 
# in <Python3.8 put `str()` around Path() objects! or `.as_posix()`!
```

要实现 R 的`file.copy()`，似乎有些棘手。

让我们实现一个简单的版本，涵盖最常见的情况。

因此，要么`from`路径是一个目录——然后我们希望递归地将文件夹中的所有文件复制到新目录。因此，我们可以使用`shutil.copytree()`。

如果`from`路径是一个文件，我们使用`shutil.copyfile()`。然而，我们可能希望允许现有的目录作为目标`to`。我们可以使用上面写的`dir_exist()`来检查。

```
def file_copy(_from, _to):
    if dir_exist(_from):
        shutil.copytree(src=_from, dst=_to,
                        *symlinks=False*, 
                        *ignore=None*, 
                        *copy_function=shutil.copy2*, 
                        *ignore_dangling_symlinks=False*, 
                        *dirs_exist_ok=False)*
    elif file_exist(_from):
        if dir_exist(_to):
            _to = file_path(_to, basename(_from))
        shutil.copyfile(_from, _to,
                        follow_symlinks=True)
    else:
        raise FileNotFoundError
```

# 9.如何在 R 和 Python 中移动和重命名文件夹和文件

要承认，除了单个文件，R 中没有真正的`mv`等价物。

```
# move a folder
file.copy(from = "/from/dir", to = "/to/dir", 
          overwrite = recursive, 
          recursive = FALSE, 
          copy.mode = TRUE)
unlink("/from/dir")
# so this is not actually a mv in the sense of unix commands,
# but a copy and delete recursively# files
# move a file
file.rename("from", "to")
# or one could also copy first the file and then unlink() the original one.
```

在这一点上，Python 有`shutil.move()`，它相当于 Unix 系统的`mv`:

```
# move a folder recursively
shutil.move(*src="*/from/dir", *ds*t="/new/dir", *copy_function=shutil.copy2*)# move files:
os.rename("/to/file.txt", "/new/dir")
pathlib.Path("/to/file.txt").rename("/new/dir/file.txt")
shutil.move(src="/to/file.txt", dst="/new/dir")
```

所以这可能是唯一的功能，我们更喜欢 Python 的`move()`而不是 R 的`file.rename()`。然而，我们将实现后者:

```
def move(_from, _to, copy_function=shutil.copy2):
    return shutil.move(src=_from, dst=_to,           
                       copy_function=copy_function)def file_rename(_from, _to):
    return shutil.move(src=_from, dst=_to)
```

最后，我们离开了:

# 10.如何在 R 和 Python 中获取文件夹和文件的大小和创建时间

r 对这个问题的解决方案是:

```
obj <- file.info(path)
# watch available variables by
str(obj)# file size
obj$size# is path a directory?
obj$isdir# modification, creation, access time
obj$mtime, obj$ctime, obj$atime
```

为此，Python 具有:

```
os.getsize(path)
os.getatime(path)
os.getctime(path)
os.getmtime(path)# and one can use the setters too
os.path.isdir(path)# look for methods in
dir(os)
dir(os.path)
dir(pathlib.Path)
```

为了模仿 R 的对象，我们可以在我们的 Python 包中为它编写一个类:

```
class FileInfo:

    def __init__(self, path):
        self.path = path
        self.size = os.getsize(path)
        self.atime = os.getatime(path)
        self.ctime = os.getctime(path)
        self.mtime = os.getmtime(path)
        self.isdir = os.path.isdir(path)# a more encompassing class would be:
class FileInfo:

    def __init__(self, path):
        self.path = path
        self.exists = os.path.exists(path)
        if self.exists:
            self.size = os.getsize(path)
            self.atime = os.getatime(path)           # last access
            self.ctime = os.getctime(path)           # creation
            self.mtime = os.getmtime(path)           # last modific.
            self.isdir = os.path.isdir(path)         # is a dir
            self.islink = os.path.islink(path)       # is a symlink
            self.isfile = os.path.isfile(path) and not self.islink
                                                 # is a regular file
```

# 使用诗歌构建和发布

首先，我们必须导出函数名和类名，这样它们就可以从包中导出，因此可以从包外访问。

为此，我们打开文件夹中的`~/rpath/rapth/__init__.py`文件，其中有`main.py`文件。并在那里写入导入文件:

```
__version__ = '0.1.0' # this line poetry wrote
from .main import getwd, setwd, dirname, basename, file_ext  
from .main import file_path, Path, list_dirs, list_files 
from .main import dir_exists, file_exists, dir_create, file_create
from .main import unlink, file_copy, move, file_rename, FileInfo
```

然后，我们必须构建并发布这个包:

```
$ poetry build
$ poetry publish 
# asks for our username and password in PyPI
# so you should have an account there or register
```

Cave:诗词对版本控制非常严格，所以每次要重新构建重新发布(为了更新包代码的改动)，都要在`pyproject.toml`文件中增加版本号！(你可以给出版本号`0.1.0-0`，如果你想避免版本号上升带来的膨胀，只升级`—`后面的版本号)。

一旦完成并删除了所有错误，我们就可以从这个星球上的任何地方进入终端:`$ pip install rpath`，打开 Python(或`ipython`)，所有的路径处理功能都唾手可得！恭喜你！

(完整代码列在[https://bitbucket.org/freiburgmls/rpath/src/master/](https://bitbucket.org/freiburgmls/rpath/src/master/)，我的 bitbucket 工作区)。