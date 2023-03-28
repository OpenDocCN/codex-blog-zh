# Next.js 13 +谷歌字体

> 原文：<https://medium.com/codex/next-js-13-google-fonts-5e0e50e031c6?source=collection_archive---------3----------------------->

自托管谷歌字体是 Next.js 13 的最新发展之一。有了这个新功能，一切都发生在服务器端，因此浏览器不需要保持活跃的连接，以便从谷歌的服务器下载网络字体。我们将在本文中讨论安装和使用。

![](img/5ea4a7d2021a6b9753e1e7e57bfe5239.png)

## 装置

要在现有的 Next.js 13 项目中安装谷歌字体优化，你应该使用`yarn`或`npm`

```
💿 **INSTALL**yarn add [@next/font](http://twitter.com/next/font)ornpm install [@next/font](http://twitter.com/next/font)
```

## 使用

使用这个字体优化包时，没有必要将 web 字体导入到 CSS 中。所有谷歌字体都可以通过`@next/font/google`轻松访问(两个单词的字体名称用`_`隔开)

```
🚚 **IMPORT**import { Inter, Cormorant_Garamond } from "[@next/font](http://twitter.com/next/font)/google"
```

导入后，您必须为不可变字体指定`weight`

```
💡 *Variable font = There is only one font file containing all the weight data, therefore different variations don't need to be specified explicitly*
```

为此，我鼓励您在您的架构中使用不同的文件

```
🖇 **CREATE fonts.ts WITH EXPORTS**import { Inter, Cormorant_Garamond } from "[@next/font](http://twitter.com/next/font)/google"**export const inter = Inter()
export const cormorantGaramond = Cormorant_Garamond({
  subsets: ['cyrillic'],
  weight: '300'
})**
```

使用新创建的字体对象的`className`属性，您可以向元素添加字体样式

```
🎨 **APPLY FONTS****import { inter, cormorantGaramond } from "../styles/fonts"**const App = () => {
  return (
    <div>
 **<h1 className={inter.className}>This is the title</h1>
      <div className={cormorantGaramond.className}>текст</div>**
    </div>
  )
}export default App
```

要使用外部 CSS 来应用额外的样式，您可以定义字体的`variable`字段

```
🚝 **UPDATE fonts.ts**import { Inter, Cormorant_Garamond } from "[@next/font](http://twitter.com/next/font)/google"**export const inter = Inter({ variable: '--title-font' })**
export const cormorantGaramond = Cormorant_Garamond({
  subsets: ['cyrillic'],
  weight: '300'
})
```

现在，您可以在组件的 CSS 模块中使用它，方法是将该变量传递给要设置样式的元素的父组件。

所以在创建或更新 CSS 类之后

```
📝 **CSS MODULE UPDATE**.title {
  **font-family: var(--primary-font);**
  text-decoration: underline;
}
```

你可以用它来设计你的元素

```
🎨 **APPLY STYLING WITH THE CSS MODULE**import { inter, cormorantGaramond } from "../styles/fonts"
import styles from '../styles/App.module.css'const App = () => {
  return (
    **<div className={inter.variable}>**
      **<h1 className={styles.title}>This is the title</h1>**
      <div className={cormorantGaramond.className}>текст</div>
    </div>
  )
}export default App
```

👋我希望这在你寻找新的学习材料时对你有用。感谢您花时间阅读这篇文章！如果你想在未来读到更多这样的故事，请订阅我的电子邮件列表。