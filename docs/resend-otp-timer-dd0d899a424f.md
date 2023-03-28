# 定时器抖动

> 原文：<https://medium.com/codex/resend-otp-timer-dd0d899a424f?source=collection_archive---------2----------------------->

移动或电子邮件验证在让真正的用户进入系统方面发挥了重要作用。没有验证，我们可能无法获得关于用户的正确信息，这将影响我们项目中真正用户的质量。因此，这里我们要设计一个漂亮的 UI 或者验证 Otp 屏幕。

包括获得经过验证的真实信息，控制我们的移动应用程序向我们的服务器请求响应的次数也很重要。当系统越来越大，用户数量越来越多，移动应用程序和服务器之间的通信也越来越多。

因此，为了控制用户的移动应用程序访问服务器的数量，我们在应用程序中迈出了重要的一小步，即限制用户在一定时间内请求 OTP 响应。

在产品的开始阶段，它可能看起来不那么重要，但它在限制服务器请求方面也起着重要作用。

我们的应用程序的输出如下所示:

![](img/667eb46cc0b257b0391c2c9f73188277.png)

工作应用程序演示

在编码之前，首先，让我们讨论一下我们将用于 OTP 字段的漂亮 UI 和动画的包。

在这个项目中，我们将使用 [pin_code_fields](https://pub.dev/packages/pin_code_fields) 。你也可以根据自己的需要定制这个包。

# 好吧，让我们深入进去。

首先，让我们将这个包添加到**publibsec . YAML**文件中。

```
pin_code_fields: ^7.4.0
```

之后，让我们定义我们的主题颜色，以及我们将在应用程序中使用的错误颜色。不一定要创建一个单独的文件来保存我们的颜色或其他常量值，比如文本、字符串、样式和装饰。但是当我们的项目变得更大时，我们在一类文件中的代码长度也变得更大，这使得我们的工作变得更慢和不整洁。

因此，最好为相似的东西创建单独的文件或类，以保持代码整洁、干净和可理解。

创建一个名为 **my_theme.dart.** 的文件，如上所述，我们将在这个项目中使用颜色和主题。

我们首先创建一个类，将我们的十六进制颜色转换成材质颜色。

```
class HexColor extends Color {  
static int _getColorFromHex(String hexColor) {
    hexColor = hexColor.toUpperCase().replaceAll("#", "");
    if (hexColor.length == 6) {
      hexColor = "FF$hexColor";
    }
    return int.parse(hexColor, radix: 16);  
}   
HexColor(final String hexColor) : super(_getColorFromHex(hexColor));
}
```

然后我们宣布我们的颜色为

```
static Color themeColor = HexColor('#2F3131');
```

**my_theme.dart** 如下:

***my _ theme . dart***

然后我们创建一个按钮，作为一个可重用的小部件。

在 **lib** 文件夹中创建一个 **custom_button.dart** 文件。

在创建了我们的实用程序和小部件之后。我们现在创建我们的 **Otp 屏幕。**

在 lib 文件夹中创建 otp_screen.dart。

在这个屏幕中，我们创建了一个方法，当我们单击**重新发送按钮**时，该方法将被执行。

```
void startTimer() {
    const oneSec = Duration(seconds: 1);
    _timer = Timer.periodic(
      oneSec,
      (Timer timer) 
{        if (_start == 0) {
          setState(() {
            timer.cancel();
            isLoading = false;
          });
        } else {
          setState(() {
            _start--; 
         });
        } 
     },
    );
  }
```

当我们的屏幕加载时，我们首先调用函数。

```
@override
  void initState() {
    super.initState();
    errorController = StreamController<ErrorAnimationType>();          startTimer();
  }
```

我们如下处置我们的定时器:

```
@override
  void dispose() {
    super.dispose();
    _timer.cancel();
    errorController!.close();
  }
```

最终 **otp_screen.dart** 看起来如下:

最后我们的主镖

# 让我们连接起来

我们可以成为朋友。在[脸书](https://www.facebook.com/nabin.dhakal.714/)、 [Linkedin](https://www.linkedin.com/in/nabindhakal/) 、 [Github](https://github.com/nbnD) 、 [Youtube](https://www.youtube.com/channel/UCW6oYt_3QSl7J2HSHNqwXWw) 、 [BuyMeACoffee](https://www.buymeacoffee.com/nabindhakal) 和 [Instagram](https://www.instagram.com/nbn_d_/) 上查找。

访问:[颤振连接](https://flutterjunction.com/)

**投稿:** [BuyMeACoffee](https://www.buymeacoffee.com/nabindhakal)

# 结论

希望这篇文章对你有所帮助，让你学到新的东西。我在这篇文章中使用了一些对你们中的一些人来说可能是新的东西。

如果你学到了新的东西或者想提出一些建议，请在评论中告诉我。

如果你喜欢这篇文章，请点击👏图标，为您提供传递所有新事物的动力。

此外，关注令人兴奋的文章和项目的更新。

通过分享来学习对学习过程有很大的影响，并使社区越来越大。

分享是吸引其他爱好者的磁石。

因此，让我们朝着扩大我们的学习社区迈出一小步。

与你的朋友分享这篇文章，或者如果你喜欢这篇文章，就在推特上发表。

**在**获得满

[](https://github.com/nbnD/resend_otp_timer) [## GitHub - nbnD/resend_otp_timer

### 一个新的颤振项目。这个项目是颤振应用的起点。一些帮助您入门的资源…

github.com](https://github.com/nbnD/resend_otp_timer)