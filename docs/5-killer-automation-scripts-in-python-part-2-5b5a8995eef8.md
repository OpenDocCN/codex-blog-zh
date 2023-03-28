# Python 中的 5 个黑仔自动化脚本—第 2 部分

> 原文：<https://medium.com/codex/5-killer-automation-scripts-in-python-part-2-5b5a8995eef8?source=collection_archive---------4----------------------->

## 这里有一些非常棒的自动化脚本，您可以在您的 Python 项目中使用。

![](img/d6786594220f093cb0ec8b146f18ada0.png)

演职员表— [链接](https://www.freepik.com/free-photo/impressed-blond-female-ceo-manager-checking-out-something-amazing-gasping-fascinated-saying-wow-holding-hands-cheeks-staring-front-white-wall_19294243.htm#query=wow&position=5&from_view=search)

**这是本文的第二部分—**[**https://medium . com/codex/5-killer-automation-scripts-in-python-42c 273 fc 37 C4**](/codex/5-killer-automation-scripts-in-python-42c273fc37c4)

在做项目的时候，我们需要一些现成的代码，可以帮助我们解决日常生活中的问题。本文为您的 Python 项目提供了 5 个自动化脚本**来解决这些问题。**

所以把它收藏起来，让我们开始吧。

# 1.)照片压缩器

这将把你的照片压缩成较小的尺寸，而质量不变。

```
import PIL
from PIL import Image
from tkinter.filedialog import *fl=askopenfilenames()img = Image.open(fl[0])img.save("result.jpg", "JPEG", optimize = True, quality = 10)
```

# 2.)图像水印

这个简单的脚本将水印任何图像。您可以设置文本、位置和字体。

```
from PIL import Image
from PIL import ImageFont
from PIL import ImageDrawdef watermark_img(img_path,res_path, text, pos):
    img = Image.open(img_path)
    wm = ImageDraw.Draw(img)
    col= (9, 3, 10)
    wm.text(pos, text, fill=col)
    img.show()
    img.save(res_path)img = 'initial.jpg'
watermark_img(img, 'result.jpg','IshaanGupta', pos=(1, 0))
```

# **3。)抄袭检查器**

这个脚本检查两个文件之间的抄袭。

```
from difflib import SequenceMatcherdef plagiarism_checker(f1,f2):
    with open(f1,errors="ignore") as file1,open(f2,errors="ignore") as file2:
        f1_data=file1.read()
        f2_data=file2.read()
        res=SequenceMatcher(None, f1_data, f2_data).ratio()print(f"These files are {res*100} % similar")f1=input("Enter file_1 path: ")
f2=input("Enter file_2 path: ")
plagiarism_checker(f1, f2)
```

# 4.)文件加密和解密

一个可以加密/解密任何文件的小脚本。

```
from cryptography.fernet import Fernetdef encrypt(f_name, key):
    fernet = Fernet(key)

    with open(f_name, 'rb') as file:
        original = file.read()

    encrypted = fernet.encrypt(original)

    with open(f_name, 'wb') as enc_file:
        enc_file.write(encrypted)def decrypt(f_name, key):
    fernet = Fernet(key)

    with open(f_name, 'rb') as enc_file:
        encrypted = enc_file.read()decrypted = fernet.decrypt(encrypted)
    with open(f_name, 'wb') as dec_file:
        dec_file.write(decrypted) key = Fernet.generate_key()f_name = input("Enter Your filename: ")
encrypt(f_name, key)
decrypt(f_name, key)
```

# **5。)使 URL 变短**

有时候大的网址读起来和分享起来会很烦。这个脚本使用一个外部 API 来缩短 URL。

```
from __future__ import with_statement
import contextlib
try:
   from urllib.parse import urlencode
except ImportError:
   from urllib import urlencodetry:
   from urllib.request import urlopen
except ImportError:
   from urllib2 import urlopen
import sysdef make_tiny(url):
   request_url = ('http://tinyurl.com/api-create.php?' +
   urlencode({'url':url}))
   with contextlib.closing(urlopen(request_url)) as response:
   return response.read().decode('utf-8')def main():
   for tinyurl in map(make_tiny, sys.argv[1:]):
      print(tinyurl)if __name__ == '__main__':
   main()
```

# 最后的想法

这里有一些很棒的自动化 Python 脚本第 2 部分，可以帮助你构建你的项目。我希望这篇文章对你有所帮助，并让你学到一些新东西。把这篇文章分享给你的 Pythoneer 朋友吧。

**快乐编码！**