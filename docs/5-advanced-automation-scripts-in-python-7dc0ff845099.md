# 5 个高级 Python 脚本，像专业人员一样实现自动化！

> 原文：<https://medium.com/codex/5-advanced-automation-scripts-in-python-7dc0ff845099?source=collection_archive---------0----------------------->

## 这些都是生活窍门！

![](img/982da00d3c7117b488dd3a6795b06962.png)

卢西亚诺·德·萨在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 1.家庭作业自动化脚本

这个脚本没有完成你的家庭作业，但是已经足够接近了。这是你在 docx 文件中提到的谷歌搜索。

```
from docx import Document
from googlesearch import searchfilename = "PythonQuestions.docx"
questions = []
def getText(filename):
    doc = Document(filename)
    fullText = []
    for para in doc.paragraphs:
        questions.append(para.text)
        fullText.append(para.text)
    return '\n'.join(fullText)doc = Document()
doc.add_heading('Homework', 0)for i in questions:
    doc.add_heading(i, level = 1)
    for j in search(i,                  # query
                    lang="en",          
                    num=5,              # number of results
                    start=0,            # start page
                    stop=2,             # stop page
                    pause=2):           # delay between each call
        doc.add_paragraph(j)doc.save('Homework.docx')
```

以上脚本中使用的库:

```
pip install python-docx
pip install googlesearch-python
```

# 2.文件管理自动化脚本

我曾经在网上课堂上遇到过这个想法，因为我必须为多个主题下载多个文件，然后手动将它们分成不同主题的文件夹，这变成了一项单调乏味的任务。

感谢 StackOverflow 的回答，我发现了关于看门狗库跟踪目录中的变化。

```
from watchdog.observers import Observer
from watchdog.events import FileSystemEventHandler
import os
import time
import re
import shutilregexSearch = "Util.py$"  # replace your regex hereclass MyHandler(FileSystemEventHandler):def on_modified(self, event):
  for filename in os.listdir(folder_to_track):
   if(re.search(regexSearch, filename)):        
    src = folder_to_track + "\\" + filename
    new_destination = folder_destination + "\\" + filename
    shutil.move(src, new_destination)folder_to_track = ""  # Source
folder_destination = ""  # Destinationevent_handler = MyHandler()
observer = Observer()
observer.schedule(event_handler, folder_to_track, recursive = True)
observer.start()try:
 while True:
  time.sleep(2)
except KeyboardInterrupt:
 observer.stop()observer.join()
```

要下载的库

```
pip install watchdog
```

# 3.即时制作有声读物

如果你是一个期待听有声读物的人，那么你肯定想要这个脚本，它可以将 PDF 中的所有文本转换成 MP3 文件。

```
from PyPDF2 import PdfFileReader
from pyttsx3 import initdef getTextFromAllPages():
    txt = ""
    with open('PythonQuestions.pdf', 'rb') as f:
        pdfReaderObj = PdfFileReader(f)
        numPages = pdfReaderObj.getNumPages()
        for pageN in range(numPages):
            page = pdfReaderObj.getPage(pageN)
            pageContent = page.extractText()
            txt += pageContent
    return txttxt = getTextFromAllPages()
engine = init()
engine.save_to_file(txt, 'test.mp3')
engine.runAndWait()
```

要下载的库

```
pip install PyPDF2
pip install pyttsx3
```

# 4.批量文件重命名自动化脚本

这个脚本是所有程序员的必备:P

当我们从网上大量下载文件时，我们只是给文件起了个糟糕的名字，然后忘记给它们重新命名，那么不用再看了，这个脚本可以为你节省几个小时的重新命名文件的时间。

```
import osroot = os.path.join('..', 'test')targetName = "textFile"
iter = 1for directory, subDirList, fileList in os.walk(root):
    for name in fileList:
        sourceName = os.path.join(directory, name)
        modName = os.path.join(directory, f'{targetName} {iter}')
        iter  += 1
        print(f'Changed from: {sourceName} to {modifiedName}')
        os.rename(sourceName, modName)
```

# 5.本地备份自动化脚本

该脚本允许您将文件夹和文件备份到 zip 文件中，以便保存到云上。

```
from zipfile import ZipFile
from os import path, walkroot = path.join('..', 'test')def zipFiles(root):
    zipName = ''.join(path.basename(root) + '.zip')
    backupZipFile = ZipFile(zipName , 'w')
    print(f'Creating {zipName}')
    for directory, subDirList, fileList in walk(root):
        print(f'Adding files in {directory}')
        backupZipFile.write(directory)
        for name in fileList:
            backupZipFile.write(path.join(directory, name))   
    backupZipFile.close()
    print('Done!')
zipFiles(root)
```

如果您喜欢这篇文章，并且想了解更多关于 Python 的内容，请查看这些文章。

[](/codex/5-advanced-code-snippets-for-your-python-programs-3377baa3d544) [## Python 程序的 5 个高级代码片段

### 使用它们，让人们惊奇

medium.com](/codex/5-advanced-code-snippets-for-your-python-programs-3377baa3d544) [](https://python.plainenglish.io/python-functions-that-make-life-easier-9d0c41b986a8) [## 让生活更简单的 5 个 Python 函数

### 让生活更简单的 Python 函数列表。

python .平原英语. io](https://python.plainenglish.io/python-functions-that-make-life-easier-9d0c41b986a8) [](https://python.plainenglish.io/if-you-are-using-assert-you-must-read-this-7256e46dc816) [## 停止使用 Assert 进行数据验证

### 对于那些使用断言操作进行数据验证的人来说，这是一篇必读的文章。

python .平原英语. io](https://python.plainenglish.io/if-you-are-using-assert-you-must-read-this-7256e46dc816) 

你可以在我的个人资料中找到更多这样的内容。

[](/@rahulbhatt1899) [## 拉胡尔·布哈特-中等

### 阅读媒体上的拉胡尔·布哈特作品。我写 Python 脚本和文章。GitHub…

medium.com](/@rahulbhatt1899)