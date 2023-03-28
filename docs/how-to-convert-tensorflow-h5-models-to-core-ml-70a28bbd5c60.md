# 如何将 TensorFlow h5 型号转换为 Core ML

> 原文：<https://medium.com/codex/how-to-convert-tensorflow-h5-models-to-core-ml-70a28bbd5c60?source=collection_archive---------6----------------------->

![](img/e5df0d62c7323e70fb675afb4410c100.png)

艾莉娜·格鲁布尼亚克在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上的照片

CreateML 使得开发用于 Xcode 的机器学习模型变得非常容易。近乎神奇。然而，尽管苹果的玫瑰园很漂亮，但有时也需要其他工具。

我将用我发现的最快最简单的方法将 Keras h5 模型文件转换成 CoreML。

## **先决条件**

遗憾的是，我们不会使用 XCode，我们必须依赖 python、 [CoreML Tools](https://coremltools.readme.io/docs) 和你的模型需要的第三方包。要转换 h5 Keras 文件，我们需要安装 [TensorFlow](https://www.tensorflow.org/) 。

假设您已经设置了 python。您可以在终端中使用这两个命令。

```
pip install coremltools

pip install tensorflow
```

**代码**

如果 python 不是你经常使用的东西，我推荐 Sublime Text 作为轻量级 IDE。

```
import coremltools
import tensorflow as tf

model_path = '/Users/You/YourModelPath.h5'

keras_model =  tf.keras.models.load_model(model_path)

model = coremltools.convert(keras_model, convert_to="mlprogram")

model.save("Model Name Comes Here")
```

写完之后，你可以将代码保存为 python 文件。例如“convert.py”并复制其目录路径。

打开终端，将目录切换到您想要保存 CoreML 文件的位置。然后，像这样运行程序，就完成了。您可以将元数据添加到模型中，方法是用 Xcode 打开它，然后单击右上角的“编辑”。

```
cd Developer/CoreMLFiles

python /Users/YourPathToThePythonFile/convert.py
```

感谢阅读。我真的很感激，并希望你的项目编译没有错误。

![](img/5600ba5b5614937d59a353cda9c5a6eb.png)

< Me being appreciative>来自 Samurai Champloo 的 Gif