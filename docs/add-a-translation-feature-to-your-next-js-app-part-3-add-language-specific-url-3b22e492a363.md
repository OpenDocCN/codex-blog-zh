# 为您的下一个添加翻译功能。JS 应用程序第 3 部分:在 next-i18next 包中添加特定于语言的 URL

> 原文：<https://medium.com/codex/add-a-translation-feature-to-your-next-js-app-part-3-add-language-specific-url-3b22e492a363?source=collection_archive---------2----------------------->

## [法典](http://medium.com/codex)

![](img/64739fd521f65a3f5e91d954c8978578.png)

# 看完这篇文章你能做什么

本文将讨论如何在 next.js 应用程序中添加特定于语言的 URL。例如，`myblog.com/en`将引导您进入英文版网站，而`myblog.com/zh`将引导您进入中文版网站，等等。

# 基本设置参考

首先，为了设置这个特性，我已经在**我以前的文章中解释过了:**

1.  [为你的 Next.js 应用程序添加翻译功能第 1 部分:设置](/javascript-in-plain-english/add-a-translation-feature-to-your-next-js-app-part-1-set-up-the-next-i18next-package-4a4788cb98ba) `[next-i18next package](/javascript-in-plain-english/add-a-translation-feature-to-your-next-js-app-part-1-set-up-the-next-i18next-package-4a4788cb98ba)`
2.  [为您的下一个添加翻译功能。JS App 第 2 部分:手动切换语言](/swlh/add-a-translation-feature-to-your-next-js-app-part-2-manually-switch-languages-e308b1ec576a)

因此，在您完成这些教程后，我假设您的应用程序能够从相应的语言脚本中获取不同的脚本，并有一个按钮能够在应用程序中为您更改语言。

此外，我构建了一个回购协议来演示这一点，所以可以随意访问并使用它。

# TL；大卫:怎么做？

为了实现这个特定于语言的 URL 特性，我们需要使用`localSubpath`选项，这是一个键/值对，我们在初始化时传递给了`NextI18Next`实例。你可以在[文档](https://github.com/isaachinman/next-i18next#5-locale-subpaths)中找到更多信息。

基本上，你只需要做三个简单的步骤:

## 1.创建一个`next.config.js`来设置`localSubpath`

只需在根目录下创建一个名为`next.config.js`的文件(与放置`i18n.js`的目录相同)，并放置以下代码:

```
#next.config.jsconst { nextI18NextRewrites } = require('next-i18next/rewrites')const localeSubpaths = {
    zh: "zh", // or whichever other languages you are looking for
    en: "en"
}module.exports = {
  rewrites: async () => nextI18NextRewrites(localeSubpaths),
  publicRuntimeConfig: {
    localeSubpaths,
  },
}
```

## 2.修改`i18n.js`以访问`localSubpath`

```
// i18n.jsconst NextI18Next = require('next-i18next').default//New added!!
const { localeSubpaths } = require('next/config').default().publicRuntimeConfigconst path = require('path')module.exports = new NextI18Next({
  defaultLanguage:'en',
  otherLanguages: ['zh'],// New added!!
  localeSubpaths, localePath: path.resolve('./public/static/locales')
})
```

## 3.使用来自`NextI18Next`实例的`Link`，而不是来自`next`

如果你的应用有一个以上的内部链接(那是很有可能的！)，那么你可能也想再做一个小的设置。当您的代码中有一个链接时，不要使用 next 中的链接，而是使用我们在另一个文件中创建的 i18 实例中的`Link`。

所以这样用`Link`:

```
import { withTranslation, i18n, Link } from "../i18n"...<Link href="/second-page"> <p>{t('secondFile:second')}</p></Link>
```

为什么？因为当你使用`NextI18Next`实例中的那个，当你被重定向到`/second-page`时，网址就会变成`[/en/second-page](http://localhost:3010/second-page)`或者用户现在正在使用的任何语言，比如`[/zh/second-page](http://localhost:3010/second-page)`，这不是很酷吗？

# 结束

这个系列就说到这里吧！感谢阅读！

# 资源:

1.  [下一个-i18 下一个](https://github.com/isaachinman/next-i18next)包
2.  [我的回购示例](https://github.com/yingqi-chen/next-translation-demo)
3.  [为您的 Next.js 应用程序添加翻译功能第 1 部分:设置 next-i18next 包](/@chanwingkeihaha/add-a-translation-feature-to-your-next-js-app-part-1-set-up-the-next-i18next-package-4a4788cb98ba)
4.  [为您的下一个添加翻译功能。JS App 第 2 部分:手动切换语言](/swlh/add-a-translation-feature-to-your-next-js-app-part-2-manually-switch-languages-e308b1ec576a)