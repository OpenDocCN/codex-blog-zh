# 用机器学习识别手写数字

> 原文：<https://medium.com/codex/recognising-written-digits-with-machine-learning-c51576a26dfb?source=collection_archive---------18----------------------->

如果你有任何编码的经验，那么你应该熟悉“Hello World”是你被教会如何做的第一件事。在现代技术世界中，机器学习正在占据主导地位，并具有如此大的潜力，但首先，让我们介绍一下人工智能的“Hello World ”!

![](img/ff24d400ed34fcfd896f116708ebee71.png)

人工智能的一个基本问题是，教会神经网络如何以你我的方式看待世界及其内容。这叫做计算机视觉。因此，我们不是从让计算机识别每一个不同的物体或人开始，而是从教它如何识别手写数字开始。听起来不太有趣，但这只是机器学习和神经网络的门户！

我们将在这里通过 python 中最简化的代码，让神经网络识别手写数字图片中的数字。令人惊讶的是，现在实现这样的事情是多么简单，只需我在这里讲述的少量代码，我们就可以教会神经网络正确分类 99%的图像！

在开始编写代码之前，我将简要介绍一下这个项目所需的基本概念。如果您从未接触过前向和后向传播背后的数学，我强烈建议您研究一下它，以及可用的不同架构。为此，我们将使用卷积神经网络(CNN)，它通常用于计算机视觉或自然语言处理(NLP)问题。

CNN 的工作原理是获取一组输入数据，并通过一些应用数学运算的过滤器。通常有许多不同大小的过滤器。经过多次卷积后，在进行分类之前，产生的数据被压缩并送入网络的“全连接”层。

同样，我不会深入这些复杂结构的力学和数学，但少量的背景知识是有用的。

现在说说你为什么会在这里，密码！构建任何简单的神经网络模型基本上都有三个部分:

*   处理输入数据
*   构建和训练模型
*   评估模型

幸运的是，对于这个特殊的问题，有一个名为 MNIST 的免费数据集，其中包含 6 万张手写数字的图像及其标签。该模型是使用 tensorflow 构建的，tensor flow 是最容易构建入门级神经网络的库。

为此，我们将需要以下软件包:

```
import matplotlib.pyplot as pltfrom keras.datasets import mnistfrom tensorflow.keras.utils import to_categoricalfrom tensorflow.keras.models import Sequentialfrom tensorflow.keras.layers import Conv2D, MaxPooling2D, Dense, Flatten
```

因此，要解决任何成功模型的第一个方面，导入和操作数据。我们首先需要导入 MNIST 数据集，并把它分成训练和测试，有输入和输出。

```
(x_train,y_train),(x_test,y_test) = mnist.load_data()
```

“x_train”和“x_test”是我们的输入数据，而“y_train”和“y_test”是它们相应的值，即图像中写入的数字。我们需要确保输入数据数组的形状适合 CNN 处理。每个图像是 28×28 像素，并且是黑白的，即具有一个颜色维度，因此每个图像的阵列形状是(28，28，1)。我们还需要将响应标签转换为“分类”数据，这意味着它不再保存像“4”这样的单个值，而是变成一个二进制列表，其中 1 占据标签值的位置。例如，标签“4”将变成[0，0，0，0，1，0，0，0，0]，或者“2”将变成[0，0，1，0，0，0，0，0，0，0]。这看起来很武断，但当有两个以上可能的类别要分类时，这就是如何做到的。

总之，事情是这样的:

```
x_train = x_train.reshape((x_train.shape[0],28,28,1))x_test = x_test.reshape((x_test.shape[0],28,28,1))y_train,y_test = to_categorical(y_train),to_categorical(y_test)
```

在构建模型之前，我们需要做的最后一点数据操作是对输入数据进行规范化。这通常用于输入数据，以防止训练期间的梯度爆炸(如果您理解反向传播的数学，这将更有意义)，并且还使预测与测试数据更加一致。由于输入数据是黑白图像，它们的值将介于 0 和 255 之间，因此要轻松标准化数据，我们只需将每个值除以 255。

```
x_train = x_train/255.0x_test = x_test/255.0
```

是时候建立我们的模型了！这是事情变得有趣的地方，也是你可以真正尝试的地方。我们将使用两个卷积层、两个池层和两个全连接层。你可以用它来试着找到一个比我将要展示给你的更好的模型。作为反向传播优化器，我们使用统计梯度下降(SGD)。这是构建我们模型的代码，你将会看到创建这样一个神经网络需要多么少的工作。

```
model = Sequential()
model.add(Conv2D(32,(5,5),activation='relu',input_shape=(28,28,1)))
model.add(MaxPooling2D((3,3)))
model.add(Conv2D(64,(2,2),activation='relu'))
model.add(MaxPooling2D((2,2)))
model.add(Flatten())
model.add(Dense(500,activation='relu'))
model.add(Dense(100,activation='relu'))
model.add(Dense(10,activation='softmax'))
opt = tensorflow.keras.optimizers.SGD(learning_rate=0.01,
                                      momentum=0.9)
model.compile(optimizer=opt,loss='categorical_crossentropy',
              metrics=['accuracy'])
history = model.fit(x_train,y_train,epochs=20,
                    validation_data=(x_test,y_test),verbose=1)
```

训练机器学习模型本质上是计算密集型的。如果你的电脑有点不足，强烈推荐使用 google colab 和 GPU 运行时来加快速度。

现在我们只需要评估我们的模型有多有效。在上面的“fit”方法中将“verbose”设置为 1，您将会看到所有的结果，但是绘制它们会使理解您的模型更加容易。我们将在一个图上绘制训练集和测试集的准确性，在另一个图上绘制训练集和测试集的损失。

```
plt.plot(history.history['accuracy'],label='Training')
plt.plot(history.history['val_accuracy'],label='Testing')
plt.title('Accuracy')
plt.legend()
plt.show()plt.plot(history.history['loss'],label='Training')
plt.plot(history.history['val_loss'],label='Testing')
plt.title('Loss')
plt.legend()
plt.show()
```

如果你遵循所有这些代码并自己运行它，那么你应该最终得到一个神经网络，它可以在 99%的时间内正确地分类手写数字！现在用 python 处理这样的问题非常容易，任何人都可以做到。

我希望你觉得这个神经网络的介绍很有趣，并希望你能更进一步，进行真正的实验。如果你已经发现了机器学习的错误，精通微积分和编码，那么我强烈建议你从头开始编写一个前馈神经网络，不要打包！它真的会让你更好地理解这些令人难以置信的结构是如何工作的。