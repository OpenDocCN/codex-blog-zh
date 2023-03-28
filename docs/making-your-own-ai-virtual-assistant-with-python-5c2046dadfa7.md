# 用 Python 制作自己的人工智能虚拟助手

> 原文：<https://medium.com/codex/making-your-own-ai-virtual-assistant-with-python-5c2046dadfa7?source=collection_archive---------0----------------------->

![](img/3e78913ef778044367144922f33a679e.png)

Akhil Yerabati 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

我们都听说过人工智能助手，如钢铁侠中的贾维斯，以及现实生活中的亚马逊 Alexa 和谷歌 Home。但是如果你能自己做呢？嗯，有了 python 和一些语音识别的魔法，你就可以了！

制作您自己的个人助理允许您添加针对您需求的定制功能，让您过上更高效的生活！

# 什么是虚拟助理？

虚拟助手已经编写了能够识别人类语言和命令的应用程序，这些语言和命令会导致程序执行任务。虚拟助理通常为用户执行简单的工作，例如向日历添加任务；提供通常在网络浏览器中搜索的信息；或者控制和检查[智能家居](https://internetofthingsagenda.techtarget.com/definition/smart-home-or-building)设备的状态，包括灯、摄像头和恒温器。

虚拟助手是物联网的一部分。物联网是一个“物”的网络，能够在没有人工干预的情况下通过无线网络收集和传输数据。你可以在这里了解更多信息:

[](/swlh/saving-money-at-the-cost-of-privacy-19c4fd5e2115) [## 以牺牲隐私为代价省钱

### 想象一下，仅仅十年前，我们还得起床调节恒温器。然而现在我们可以完成这项任务…

medium.com](/swlh/saving-money-at-the-cost-of-privacy-19c4fd5e2115) 

这些助手的核心由语音识别程序和音频引擎组成。一旦你有了这个核心，可能性是无限的，你可以添加尽可能多的功能。

![](img/f4097cb14cf0bd045b41e98b26627fca.png)

路易斯·科尔特斯在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍摄的照片

# 构建助手

对于这个项目，我们将使用 Python，我将使用 3.6。我也将使用 Pycharm IDE(你可以在这里下载),尽管你可以使用任何东西。

首先，让我们确保我们在虚拟环境中工作，以确保我们安装的包不会影响全局运行的解释器。

一旦建立了虚拟环境，我们就可以开始安装软件包了。对于核心代码，我将使用以下命令:

```
pip install pyttsx3 
pip install speechRecognition
pip install PyAudio
```

这些软件包将允许机器说话和理解单词，并将文本转换为语音。

一旦你安装了软件包，我们可以初始化音频引擎。

```
import pyttsx3engine = pyttsx3.init()
```

是的，就这么简单。另外，如果你想改变声音，你可以使用下面的代码。

```
import pyttsx3engine = pyttsx3.init()
voices = engine.getProperty('voices')engine.setProperty('voice', voices[ENTER VOICE NUMBER].id)
```

您可以更改“输入声音号码”来更改声音。

这段代码只初始化了引擎，并允许您选择声音类型。但是为了让机器输出声音，我们需要编写以下函数:

```
def assistantResponse(*text*):
    print(*text*)

    engine.say(*text*)
    engine.runAndWait()
```

有了这个代码，我们现在可以让我们的机器说任何话。要进行测试，请运行以下代码:

```
assistantResponse("Hello World")
```

以下是语音功能的组合代码:

```
import pyttsx3engine = pyttsx3.init()def assistantResponse(*text*):
    print(*text*)

    engine.say(*text*)
    engine.runAndWait()
```

现在语音功能完成了，我们现在需要对机器进行编程以识别单词。

```
import speech_recognition as srdef recordAudio():
    # Record The Audio
    r = sr.Recognizer()  # Creating a recognizer object

    # Open the microphone and start recording
    with sr.Microphone() as source:
        print('Say something!')
        audio = r.listen(source)

    # Use google's speech recognition
    data = ''
    try:
        data = r.recognize_google(audio)
        print('You said: ' + data)
    except sr.UnknownValueError:  # Checking for unknown errors
        print('Google Speech Recognition could not understand the audio')
    except sr.RequestError as c:
        print('Request results from Google Speech Recognition service error')

    return data
```

这种代码首先识别什么时候有人说话，然后打开麦克风。然后保存识别的文本。如果音频没有被识别，那么代码打印为:“谷歌语音识别无法理解音频”。如果它遇到一个错误，它就会打印:“来自 Google 语音识别服务的请求结果错误”。

我们现在有了我们助手的核心。我们现在可以开始添加功能来扩展该助手的功能。

# 问候功能

先说简单的。该问候功能识别用户是否说过问候。如果为真，那么它将输出一个响应的问候词

```
def greeting(*text*):
    Greeting_Inputs = ['hi', 'hey', 'hola', 'bonjour', 'hello']
    Greeting_Response = ['hi', 'hey', 'hola', 'greetings', 'bonjour', 'hello']

    for word in *text*.split():
        if word.lower() in Greeting_Inputs:
            return random.choice(Greeting_Response)
    return ''
```

# 唤醒词功能

很多助手都有“嗨 Alexa”“嗨 Google”之类的唤醒词。

```
def wakeWord(*text*):
    WAKE_WORD = ['ENTER NAME HERE']

    text = *text*.lower()

    for phrase in WAKE_WORD:
        if phrase in *text*:
            return True
    return False
```

现在我们可以进行一个 while 循环，在这个循环中，系统不断地寻找唤醒词。一旦它识别出唤醒词，它将寻找任何可以触发进一步行动的关键字。

```
while True:
    text = recordAudio()
    responses = ''

    if wakeWord(text.lower()) == True:
```

一旦我们有了这个设置，我们可以在 if 语句下添加以下代码行，以允许助手识别何时说了问候语。

```
`try:
    responses = responses + greeting(text)
except Exception as e:
    responses = responses + ''
```

随着我们添加越来越多的功能，我们将继续添加代码行来激活这些功能。

# 日期函数

添加日期功能非常有用，在大多数助手中都有实现。您需要从安装日期时间包开始

```
pip install DateTime
```

我们还需要日历包，但它预装了 Python。安装后，进行以下功能:

```
import detetime
import calenderdef getDate():
    now = datetime.datetime.now()
    my_date = datetime.datetime.today()
    weekday = calendar.day_name[my_date.weekday()]
    monthNum = now.month
    dayNum = now.day

    month_names = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October',
                   'November', 'December']

    ordinalNumbers = ['1st', '2nd', '3rd', '4th', '5th', '6th', '7th', '8th', '9th', '10th', '11th', '12th', '13th','14th', '15th', '16th', '17th', '18th', '19th', '20th', '21st', '22nd', '23rd', '24th', '25th', '26th', '27th', '28th', '29th', '30th', '31st']

    return 'Today is ' + weekday + ', ' + month_names[monthNum - 1] + ' the ' + ordinalNumbers[dayNum - 1]
```

我们还需要在查找关键字的 if 语句下进行设置

```
if 'date' in text:
    get_date = getDate()
    responses = responses + ' ' + get_date
```

我们还可以添加一个 if 语句，告诉用户某个特性是否还没有添加。

```
if responses == '':
    responses = responses + "I'm Sorry I Can't Do That Yet"
```

# 编译后的代码

我在下面包含了编译后的代码。注意:我在这里提到的特性只是基本的。您可以根据需要输入任意数量的 API。我最终添加了更多。Youtube 上有很棒的教程解释如何使用不同的 API。

代码如下:

```
#Importing the Modules
import calendar
import datetime
import pyttsx3
import speech_recognition as sr# Initializing the audio engine
engine = pyttsx3.init()# Record audio and return as a string
def recordAudio():
    # Record The Audio
    r = sr.Recognizer()  # Creating a recognizer object

    # Open the microphone and start recording
    with sr.Microphone() as source:
        print('Say something!')
        audio = r.listen(source)

    # Use google's speech recognition
    data = ''
    try:
        data = r.recognize_google(audio)
        print('You said: ' + data)
    except sr.UnknownValueError:  # Checking for unknown errors
        print('Google Speech Recognition could not understand the audio')
    except sr.RequestError as c:
        print('Request results from Google Speech Recognition service error')# Function for response
def assistantResponse(*text*):
    print(*text*)

    engine.say(*text*)
    engine.runAndWait()

    return data# Random Greeting Function
def greeting(*text*):
    Greeting_Inputs = ['hi', 'hey', 'hola', 'bonjour', 'hello']
    Greeting_Response = ['hi', 'hey', 'hola', 'greetings', 'bonjour', 'hello']

    for word in *text*.split():
        if word.lower() in Greeting_Inputs:
            return random.choice(Greeting_Response)
    return ''# Wake word function
def wakeWord(*text*):
    WAKE_WORD = ['ENTER NAME HERE']

    text = *text*.lower()

    for phrase in WAKE_WORD:
        if phrase in *text*:
            return True
    return False# Function to get date
def getDate():
    now = datetime.datetime.now()
    my_date = datetime.datetime.today()
    weekday = calendar.day_name[my_date.weekday()]
    monthNum = now.month
    dayNum = now.day

    month_names = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October',
                   'November', 'December']

    ordinalNumbers = ['1st', '2nd', '3rd', '4th', '5th', '6th', '7th', '8th', '9th', '10th', '11th', '12th', '13th','14th', '15th', '16th', '17th', '18th', '19th', '20th', '21st', '22nd', '23rd', '24th', '25th', '26th', '27th', '28th', '29th', '30th', '31st']

    return 'Today is ' + weekday + ', ' + month_names[monthNum - 1] + ' the ' + ordinalNumbers[dayNum - 1]# The Brain
while True:
    text = recordAudio()
    responses = ''

    if wakeWord(text.lower()) == True:
        try:
            responses = responses + greeting(text)
        except Exception as e:
            responses = responses + ''

    if 'date' in text:
        get_date = getDate()
        responses = responses + ' ' + get_date if responses == '':
        responses = responses + "I'm Sorry I Can't Do That Yet"

    # Response
    assistantResponse(responses)
```

# 结论

虽然这个助手是非常基础的，只是一个小的演示，但它代表了大事情的开始。随着技术的不断进步，我们现在所处的非常基础的阶段将演变成一个更加复杂的助手和智能物体网络，它们共同努力，让世界变得更加高效、安全和可持续。

如前所述，AI 助手是物联网(IoT)的一部分。随着这一领域的不断进步，人类将能够在同样长的时间内完成更多的工作。物联网正被用于从住宅到商业的许多领域。虽然这项技术还有很长的路要走，但一旦准备好，它将彻底改变世界。