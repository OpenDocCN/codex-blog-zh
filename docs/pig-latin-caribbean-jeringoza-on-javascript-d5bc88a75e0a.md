# JavaScript 上的拉丁猪和加勒比海猪

> 原文：<https://medium.com/codex/pig-latin-caribbean-jeringoza-on-javascript-d5bc88a75e0a?source=collection_archive---------5----------------------->

![](img/30db3266cee48a57833ff9505d9a7116.png)

来自博客野蛮女友

当练习算法和数据结构时，我们会遇到学校噩梦中最乏味的数学或者一些有趣的东西，比如[猪拉丁语](https://grammarist.com/interesting-words/pig-latin/#:~:text=Invented%20language%20is%20a%20phenomenon,Pig%20Latin%20Love%20in%201919.)。Pig Latin 这是一种游戏，其中每个单词的开头辅音或辅音被转移到单词的结尾，同时还有音节 *-ay* ，如 *igpay atinlay* 。比如 Javascript 变成了 *avascriptJay。根据一些资料，早在 19 世纪的某个时候，孩子们就开始玩文字游戏。虽然，它是在 1919 年随着歌手亚瑟·菲尔兹的歌曲《猪拉丁之爱》被带到主流良心的。*

我在波多黎各长大，西班牙语是我的第一语言。我小时候从来没有玩过英国猪拉丁，但是我们有自己的版本叫做 *Jeringoza* 。在西班牙，J *eringoza* 的演奏方法是在一个单词的每个元音后加一个“P”，然后在“P”后，你再重复一次元音。例如，“你好世界”会变成 *Hepellopo Woporld* 。在古巴、多米尼加共和国和波多黎各，我们以不同的方式演奏它，在每个音节上加上“气”。所以，在加勒比海地区,“你好世界”的 *Jeringoza* 是*chihechilo chi World*。

现在，让我们回到编码行业。在某些页面上，你可能会发现猪拉丁挑战赛。在这种情况下，他们要求您将一串单词或一个数组转换为 Pig Latin。这就是我的解决方案。

我首先声明这个正则表达式变量:

```
let regex = /^[^aeiou]+/;
```

接下来是一个与正则表达式匹配的辅音变量。

```
let consonants = str.match(regex);
```

用一个条件语句返回字符串，用空格替换起始辅音。将开头辅音与“ay”串联在世界的末尾如果辅音不是`null`或`null`而以元音开头，则将单词与“way”串联。

下面是一个完整的函数:

```
translatePigLatin(“algorithm”);// output: algorithmwaytranslatePigLatin(“Javascript”);//output: avascriptJay
```

既然我们已经知道了古老的英国猪拉丁代码，让我们来研究一下西班牙语的对应代码，Jeringoza。伴随我长大的加勒比海版本。

与 Pig Latin 类似， *Jeringoza* 算法将有一个`regex`变量。不同的是，它将比过去的代码更长，因为这一次，我们要分开所有的音节。

```
const syllableRegex = /[^aeiouy]*[aeiouy]+(?:[^aeiouy]*$|[^aeiouy](?=[^aeiouy]))?/gi;
```

在声明了`regex`之后，返回“chi”和带有匹配音节`regex`的字符串，并在每个音节之前加入“chi”。

```
return “chi” + str.match(syllableRegex).join(“chi”);
```

下面是整个函数的样子。

```
jeringoza(‘Javascript’)// output: chiJachivaschicript
```

所以你去那里，猪拉丁和`Jeringoza`玩。希望这有助于理解弦乐是如何工作的，同时有一些童年的回忆和乐趣。*“Chihachippy chicchiding！”*

![](img/e9bf1885111e53cd6361648bb9228368.png)

# 总结:

1.  什么是猪拉丁和*杰林戈扎*？
2.  猪拉丁函数。
3.  加勒比海 *Jeringoza* 功能。

# 参考资料(指向资源的超链接):

1.  [猪拉丁语](https://grammarist.com/interesting-words/pig-latin/#:~:text=Invented%20language%20is%20a%20phenomenon,Pig%20Latin%20Love%20in%201919.)
2.  [*杰林戈萨*](https://en.wikipedia.org/wiki/Jeringonza)
3.  [MDN 网络文档—字符串](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)