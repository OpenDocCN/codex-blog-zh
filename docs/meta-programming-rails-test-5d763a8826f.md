# 元编程 Rails 测试

> 原文：<https://medium.com/codex/meta-programming-rails-test-5d763a8826f?source=collection_archive---------2----------------------->

![](img/7796a44d746012f11ff854db53c2332f.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/fractal?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

点击这里查看这篇文章的视频【https://www.youtube.com/watch?v=bFSl8OQym0k 

在应用程序开发的生命周期中，人们倾向于拖延或放弃编写测试。我敢肯定，许多新工程师，就像我以前的自己一样，认为编写测试并不重要，甚至对实际交付产品来说也很麻烦。最重要的是，测试很容易被遗忘，因为它可能非常无聊和重复。幸运的是，在这个系列中，我们将挑战自我，通过[元编程](https://en.wikipedia.org/wiki/Metaprogramming)让编写测试变得有趣。

在专业环境中，您需要进行测试，以便正确地重构您的代码，并确保您的应用程序在您需要更新运行它的语言或框架时不会中断。

想象一下，如果你必须把你的应用升级到 Rails 7 和 Ruby 3.0。如果你没有编写任何测试，那么确保你的应用程序正常工作的唯一方法就是手工测试，繁琐地浏览每一页，搜索每一个链接和输入表单。这将是可怕的，幸运的是，我们可以编写测试，为我们自动化整个过程。

编辑代码的过程被称为重构。通常我们想改变代码的形式(看起来像什么)而不改变它的功能(它做什么)。

做同样的事情有很多不同的方法，你可能写了一些不错的代码，然后第二天带着一些很棒的代码回来，决定改变一下。在这种情况下，测试是完美的，因为它可以确保我们的程序无论看起来如何都能达到预期的输出。

幸运的是，有了 Ruby 语言，我们可以对我们的测试 ie 进行元编程。编写编写测试的代码。这迅速减少了我们需要花费在编写测试上的时间，并使测试我们的应用程序变得更加有趣。最重要的是，当我们选择使用元编程时，我们的测试占用的代码要少得多。好了，让我们开始有趣的部分吧！

## 测试模型验证的蹩脚方法

假设我们有一个名为 Restaurant 的模型，它有几个属性:姓名、地址、电话号码、类别和打烊时间。我们希望所有这些属性都是模型有效所必需的。通常我们会这样做:

```
#... /models/restaurant.rb
class Restaurant < ApplicationRecordvalidates :name, presence: true
validates :address, presence: true
validates :phone_number, presence: true
validates :category, presence: true
validates :closing_time, presence: trueend # 5 lines of code 
```

我们的模型测试文件如下所示:

```
#... test/models/restaurant_test.rbclass RestaurantTest < ActiveSupport::TestCase
   def setup
      @restaurant = restaurants :first
   end    test 'Name should be present' do
      @restaurant.name = nil 
      assert_not @restaurant.valid?
   end test 'Address should be present' do
      @restaurant.address = nil 
      assert_not @restaurant.valid?
   end test 'Phone Number should be present' do
      @restaurant.phone_number = nil 
      assert_not @restaurant.valid?
   end test 'Category should be present' do
      @restaurant.category = nil 
      assert_not @restaurant.valid?
   end test 'Closing Time should be present' do
      @restaurant.closing_time = nil 
      assert_not @restaurant.valid?
   end
end # 4 lines of code per attribute test, 5 attributes 5*4 = 20 lines 
```

为了编写和测试一个简单的存在验证，我们必须编写 5 行代码，1 行用于验证，另外 4 行用于测试。存在验证是最容易编写和测试的事情之一，但是它们仍然需要大量的代码，并且编写起来非常枯燥。

如果你有 5 个模型，每个模型有 5 个属性，你必须写 125 行代码[😤](https://hotemoji.com/frustrated-emoji.html) [🤕](https://emojipedia.org/face-with-head-bandage/) [😠](https://emojipedia.org/angry-face/)。哪个头脑正常的人会想写或者浏览那些无聊的代码和重复的代码呢？如果你在经营一家初创公司，你真的想把你的工程师的时间和注意力用在这上面吗？

那么，我们如何才能让我们的生活更轻松，更明智地使用我们的时间，编写更少的代码，赚更多的钱，并让测试变得更有趣呢？

## TDD +元编程:一对充满活力的搭档

我们将采用[测试驱动开发](https://en.wikipedia.org/wiki/Test-driven_development)方法，通过编写失败的测试并重构我们的应用程序代码，直到我们的测试通过为止。如果你注意到之前我们所有的测试都遵循一个相似的模式或公式(总结如下)。每个测试名称都以属性名称为前缀，然后在我们的实例变量上将属性设置为 nil，最后我们断言我们的变量因其 null 属性而无效。

```
test 'attribute is present' do
   @restaurant.attribute = nil 
   assert_not @restaurant.valid?
end
```

让我们首先创建一个必须存在的所有属性的列表。然后，我们将使用 each 方法遍历每个属性，传入一个实际测试发生的块。命名测试非常简单，我们所要做的就是使用字符串插值。

那么，我们如何将实例变量的属性设置为零呢？最初我想使用[发送方法](https://apidock.com/ruby/Object/send)，但这毫无意义。我本来打算放弃，但后来我意识到, [Ruby 内核方法 eval](https://apidock.com/ruby/Kernel/eval) 会评估一个字符串，就好像它是一段 Ruby 代码一样，因此我们可以把数据当作代码来处理。

我的最终测试是这样的:

```
#... test/models/restaurant_test.rb%i[name phone_number address category closing_time].each do |attr|
   test "#{attr} must be present" do 
      eval "@restaurant.#{attr} = nil"
      assert_not @restaurant.valid?
   end
end# 6 lines of code 70% reduction in code volume ;)
```

此时，我们的测试应该是红色的。因为模型验证是如此的重复，所以它们是元编程的明确候选。我们将再次遍历符号列表，在代码块内部，我们将使用 eval 和 [validates_presence_of](https://apidock.com/rails/ActiveModel/Validations/ClassMethods/validates_presence_of) 助手来运行验证。代码如下:

```
#... /models/restaurant.rb%i[name phone_number address category closing_time].each do |attr|
     eval "validates_presence_of #{attr}"
end # 3 lines of code 60% reduction in code volume
```

## 结论

元编程是一种实践，你写代码写代码，本质上我们是自动化编程。任何时候我们写重复或伪相似的代码都是一个信号，表明我们可以使用元编程来保持事情[干燥](https://en.wikipedia.org/wiki/Don't_repeat_yourself)。

元编程允许我们用更少的代码行以更快的速度编写更多的代码。这使得我们的程序更容易阅读、编写和调试。在一个专业的开发人员中，能够恰当地利用元编程将能够在更短的时间内完成更多的工作。**通过元编程，我的模型验证和测试减少了 60-70%的代码量。**

要了解更多关于元编程的知识，请务必查看我的[初学者 Ruby 元编程教程](https://www.youtube.com/watch?v=1YgOlk7c1QE)、[高级元编程教程](https://www.youtube.com/watch?v=otToay6NHCc)和元编程 a [Ruby on Rails 电子商务应用程序](https://www.youtube.com/watch?v=bFSl8OQym0k)。还有别忘了听[科里的角落](https://anchor.fm/coreys-corner)，谢谢！

科里的角落播客:[https://anchor.fm/coreys-corner](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbFNVb1htenJDQTRsU2hRckVjbXhOUGRPM3I2QXxBQ3Jtc0ttMWtRWklBSjJlZDFmSlo4enJPRW5sYkZQeFRPZ2VmajFPajdnR2JGMUFlLXpCWG1zbS1nV3BQZnVSZWx4NVpOeEhkS1ZyRG5EbVNLSU12R1pSVlJRRDg5TUxVNTBvVWw2bGN4WGo0bkFETWMxbV82NA&q=https%3A%2F%2Fanchor.fm%2Fcoreys-corner)

加德纳应用开发:[https://gardnerappdev.com](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqa053RF9Cb1YwZHhwU05uMWFMRUVsaV8wNkZzQXxBQ3Jtc0ttWFR5TFhHT2hwN3U3cHZSeUpFY1JlVFM1Qmt4TE5ydGlzWHZQWUNCenhMbVpVVHpKUGpZVHZ2UGV3QkJnTDVWVlJRV2xyU2VYN1NNRGJTSmZObUpDMmFIcWp3QlpWb1JYMkJIbE11RzVtWjJvSnBCaw&q=https%3A%2F%2Fgardnerappdev.com)

被束缚🍳[https://thoughtsandfitness.com](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbkRqcmlXSmRWdTFJQUk4YlZaV3VuT1g4dXpDUXxBQ3Jtc0tuNWx4SEY4QlQxMzFKX2xrSzdpb2FlZ2x1UHRXTmExLUpRUnVDV2JkMXNkUklJUUx5N1YtaHFxblh1ODBWLXpYSzRqTE9uTHhOUGN0UWdqU3dmYkxVNGN4SGVDMG5wZlBjLW1wRmdMVFJ0d0x3OUZSUQ&q=https%3A%2F%2Fthoughtsandfitness.com)