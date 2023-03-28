# Python 中的 5 个黑仔自动化脚本

> 原文：<https://medium.com/codex/5-killer-automation-scripts-in-python-42c273fc37c4?source=collection_archive---------0----------------------->

这里有一些非常棒的自动化脚本，您可以在您的 Python 项目中使用。

![](img/df6e61814df7c7b7c8f849113ce8af91.png)

在做项目的时候，我们需要一些现成的代码，可以帮助我们解决日常生活中的问题。这篇文章为您的 Python 项目提供了 5 个自动化脚本，可以解决您的 T2 问题。

*本文第二部分—*[*https://medium . com/codex/5-killer-automation-scripts-in-python-Part-2-5b 5a 8995 eef 8*](/codex/5-killer-automation-scripts-in-python-part-2-5b5a8995eef8)

所以把它收藏起来，让我们开始吧。

# 1.)**将图像转换为 PDF**

如果你有很多图片，并且想把它们转换成一个 Pdf 文件，那么这个自动化脚本对你来说会很方便。

方法 1-

```
import os
import img2pdfwith open("out.pdf", "wb") as file:
   file.write(img2pdf.convert([i for i in os.listdir('Path of              image_Directory') if i.endswith(".jpg")]))
```

方法 2 -

```
from fpdf import FPDF
Pdf = FPDF()list_of_images = ["one.jpg", "second.jpg","third.jpg"]for i in list_of_images:
   Pdf.add_page()
   Pdf.image(i,x,y,w,h)
   Pdf.output("out.pdf", "F")
```

# **2。)**将 PDF 转换为 CSV

有时我们需要将 PDF 数据转换成 CSV 格式，因此对于这种工作，这个 Python 脚本将会很方便。

```
import tabulafilename = input("Enter File Path: ")df = tabula.read_pdf(filename, encoding='utf-8', spreadsheet=True, pages='1')
df.to_csv('out.csv')
```

# 3.)YT 视频下载器

下载 Youtube 视频的简单自动化脚本。不需要任何网站或应用程序，只需使用下面的代码下载任何视频。

```
import pytubelink = input('Enter The Youtube Video URL')
dn = pytube.Youtube(link)
dn.streams.first().download()
print('Your Video Has Been Downloaded', link)
```

# 4.)InstaDpViewer

这个脚本会下载任何 Instagram 用户的 **DP** 。它使用模块 **instaloader** 以用户名作为输入，下载 **DP** 作为输出。

```
import instaloaderil = instaloader.Instaloader()
username = input("Enter Insta username ")
il.download_profile(username , profile_pic_only=True)
print("Your DP is Downloaded")
```

# 5.)文本到语音

它使用谷歌文本到语音 API 将你的书面文本转换为人工智能机器人的声音。

```
from pygame import mixer
from gtts import gTTSdef main():
   tts = gTTS('Like This Article')
   tts.save('output.mp3')
   mixer.init()
   mixer.music.load('output.mp3')
   mixer.music.play()
if __name__ == "__main__":
   main()
```

# 最后的想法

这里有一些很棒的自动化 Python 脚本，可以帮助你构建你的项目。我希望这篇文章对你有所帮助，并让你学到一些新东西。把这篇文章分享给你的 Pythoneer 朋友吧。

**快乐编码！**