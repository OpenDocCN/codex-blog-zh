# 测试库符合硒

> 原文：<https://medium.com/codex/the-testing-library-meets-selenium-5f74cc712114?source=collection_archive---------2----------------------->

## [测试库](http://testing-library.com/)支持用户测试。它可用于多种 JavaScript 框架(例如 React、Vue、Cypress)。我意识到把它带到科特林的硒中是可能的，也是有价值的。

> 你的测试越像你的软件被使用的方式，它们就越能给你信心。 *(* [*)肯特*](https://twitter.com/kentcdodds/status/977018512689455106) *)*

![](img/40e9af11c601423fa5d6d59a002a8721.png)

# 什么

我开发了一组定制的 [Selenium](https://www.selenium.dev/) 定位器(例如`ByRole`、`ByText`)，包装了相应的[测试库查询](https://testing-library.com/docs/queries/about)(特别是 [DOM 测试库](https://github.com/testing-library/dom-testing-library)):

```
// with the Selenium Testing Library (Kotlin)
driver.findElement(ByAltText("First name"))
driver.findElement(
   ByRole("heading", name = "/as a user/i".asJsRegex())
)
```

```
// with the Testing Library (JavaScript)
screen.queryByAltText('First name')
screen.queryByRole('heading', { name: /as a user/i })
```

我相信[测试](/codex/automated-testing-goals-d3ace8fd223a)给出了最好的例子，所以[去看看](https://github.com/lsoares/selenium-testing-library/tree/main/lib/src/test/kotlin/seleniumtestinglib)。先睹为快，下面是测试库的 [**核心 API**](https://testing-library.com/docs/) 的更多例子:

```
val firstName = driver.findElements(ByAltText("first name"))
val person = driver.findElements(ByDisplayValue("/john/i".asRegex()))
val active = driver.findElements(ByLabelText("active"))
val input = driver.findElements(ByPlaceholderText("first name", exact = false))
val title = driver.findElements(ByRole("heading", name = "/as a user/i".asRegex()))
val panel = driver.findElements(ByTestId("test-id"))
val block = driver.findElements(ByText("present", exact = false, selector = "span"))
val title1 = driver.findElements(ByTitle("title 1"))
```

我还实现了测试库 [**用户交互 API**](https://testing-library.com/docs/user-event/intro/) :

```
driver.user.click(driver.findElement(ByRole("navigation")))
driver.user.type(driver.findElement(ByRole("textbox")), "foobar")
driver.user.selectOptions(driver.findElement(ByRole("listbox")), "C")
driver.user.keyboard("[/ShiftLeft][/ShiftRight]{Tab}")
driver.user.tab()
driver.user.clear(driver.findElement(By.Id("description")))
driver.user.pointer("[MouseLeft]", "[MouseRight]")
```

[**jest-dom**](https://github.com/testing-library/jest-dom) 也可用:

```
// API similar to the original version:
expect(button.toHaveAccessibleDescription("Register"))
expect(checkboxMarketing).toBeChecked()
assertEquals(setOf("btn", "btn-danger", "extra"), deleteButton.classes)
expect(element).not.toBePartiallyChecked()

// utilities that can be used on their own:
val formValues = registrationForm.formValues
val userAgrees = checkboxMarketing.isChecked
val name = element.accessibleName
val displayedValue = element.displayValue
```

我已经把这一切打包到 [**硒测试库**](https://search.maven.org/artifact/com.luissoares/selenium-testing-library) (针对 Kotlin，但在 Java 中也有效)**[开源它](https://github.com/lsoares/selenium-testing-library)(顺便说一下，有人对 Python 做了[同样的事情)。](https://github.com/anze3db/selenium-testing-library)**

# **为什么**

**创建这个库的原因不止一个:**

*   **为开源世界做贡献，希望已经创造了对别人有用的东西；**
*   **深入研究 Selenium、测试库和 webpack**
*   **学习[如何发布一个库到 Maven Central](https://maciejwalkowiak.com/blog/guide-java-publish-to-maven-central/) (也许还有如何用 [GitHub actions](https://github.com/features/actions) 自动化它)；**
*   **以我认为 UI 测试应该做的方式使用 Selenium 作为用户进行测试。**

**为什么我相信作为用户的*测试*优于依赖技术方面(id、类、标记名、测试 id、 [XPath](https://en.wikipedia.org/wiki/XPath) /CSS 选择器)？这与自动化测试的[目标](/codex/automated-testing-goals-d3ace8fd223a)相关:**

*   **测试与用户交互的方式越相似，我就越有信心(注意，这并不意味着端到端的测试；测试库可用于测试简单的反应组件)。如果我能够改变底层的实现细节，我会觉得重构更安全，更有指导意义。 [CSS 和 XPath 选择器以及 HTML 结构本身都是易变的底层细节](/codex/decoupling-tests-from-implementation-details-238a9ab06e4f)。测试 id 是一种变通方法，但是这意味着仅仅为了测试而创建实现代码(一个[测试反模式](/codex/anti-patterns-of-automated-software-testing-b396283a4cb6))。**
*   **作为一名开发人员，如果你在寻找 UI 元素上有困难，用户又如何能找到呢？当*作为用户*进行测试时，你会被强迫并鼓励遵循良好的可用性实践，比如编写[语义 HTML](https://web.dev/learn/html/semantic-html/) (例如`article`、`section`、`nav`)以及提供可访问性标签(例如`title`、`alt`、`aria-label`)。**
*   **当测试描述真实的使用场景时，我从测试中获得了更多的文档力量。如果我几个月后阅读这些测试，我可以很容易地理解他们的目标，并且不会迷失在技术细节中。**

**为什么不首先使用测试库(在 JavaScript 或 TypeScript 中)？我在过去的一些项目中做过(这就是我了解测试库的原因)，但并不是每个人都在用这些语言/运行时工作。一些现实世界的项目已经在运行并使用 Selenium for JVM，它们也应该受益于测试库。此外，我很欣赏编译语言能给测试套件带来什么。**

**如果没有这个库，您将不得不求助于 id、类和测试 id。如果你不想依赖这些技术细节，你需要大量的工具。在某些情况下，这甚至是不可能的。例如，不区分大小写的 contains 搜索不能用 XPath 实现(至少在当前的浏览器中)。**

```
<input placeholder='Username' />
```

```
// 🟠 With pure Selenium
val result = driver.findElements(By.cssSelector("*"))
    .first() {
        it.getAttribute("placeholder")?
          .contains("USER", ignoreCase = true) == true
    }
```

```
// 🟢 With Selenium Testing Library
val result = driver.findElement(
   ByPlaceholderText("USER", exact = false)
)
```

**注意，如果元素不是立即可用的，您仍然需要等待它们(就像本地定位器一样)；你可以求助于[硒的等待能力](https://lsoares.medium.com/different-types-of-waits-in-selenium-9522fc40f515)。然而，如果你愿意，你可以使用我添加的一个 API 来依赖[测试库的等待](https://testing-library.com/docs/dom-testing-library/api-async):**

```
val firstName = driver.queryBy(
   PlaceholderText, "first name", mapOf("exact" to false)
)
```

# **怎么**

**我如何在 Selenium 中集成测试库？通过评估它的 JavaScript 以便以后使用(相当于在 JavaScript 控制台中粘贴并运行它)。然后，我可以调用测试库函数并将结果映射回 Java/Kotlin 对象。这是这项技术有趣的部分:**

```
// 1️⃣ in JS, store a handle to TL's screen:
import {screen} from '@testing-library/dom'
window.screen = screen
```

```
# 2️⃣ generate the TL script to be injected:
webpack --mode production
cp dist/testing-library.js ../lib/src/main/resources
```

```
// 3️⃣ evaluate the TL script so it gets stored in screen:
driver.executeScript(
   {}.getResource("/testing-library.js").readText()
)
```

```
// 4️⃣ call a TL function from screen and rely on Selenium to map the results to WebElements
val results = driver.executeScript(
   "return screen.queryAllByRole(arguments[0])", 
   "listbox"
)
```

**如您所见，所有繁重的工作都由测试库 webpack 和 Selenium 来完成。但是有一个缺失的部分:如何让这些查询对 Selenium 透明？我想使用它们，比如本地的(例如`driver.findElement(By.Id("profile"))`)。Selenium 很容易用新的定位器扩展:继承`By`并实现`findElements`。下面是一个自定义定位器的示例(不是来自我的库):**

```
data class ByAttribute(private val name: String, private val value: String) : By() {
    override fun findElements(context: SearchContext): List<WebElement> =
        context.findElements(
            cssSelector("[$name='${value.replace("'", "\\''")}']")
        )
}
```

**我可以像使用任何原生 Selenium 定位器一样使用自定义定位器:`driver.findElement(ByAttribute("href", "http://example.com"))`。**

## **获取、查询还是查找？一个还是全部？**

**测试库核心 API 有三类查询:`get*`、`query*`、`find*`。选哪个？`get*`找不到元素时失败，我想把它留给硒。`find*`等待元素可用，同样，[硒有等待机制](https://lsoares.medium.com/different-types-of-waits-in-selenium-9522fc40f515)。这使得`query*`成为更灵活的选项，也是我使用的选项。**

**每个函数族都有单数版本(例如`query`)和复数版本(例如`queryAll`)。我使用复数是因为要创建 Selenium 定位器，我们只需要实现`findElements`(复数)。Selenium 基于它提供`findElement`(单数)。**

## **移植还是适应？**

**注入测试库脚本并将其连接到 Kotlin/Java 被称为[适配器模式](https://en.wikipedia.org/wiki/Adapter_pattern)。然而，我最初的方法是依靠 Selenium 定位器(例如，`xpath`，`cssSelector`)来移植测试库行为，目的是实现特性对等。事实证明这是一种有限的方法，因为:**

*   **由于 [WebdriverIO](https://webdriver.io/) 和浏览器限制(例如 XPath 的不区分大小写的文本搜索)，Selenium locators 无法与测试库搜索功能竞争；**
*   **我必须移植所有的功能，这是一个很大的工作量；**
*   **我可能会引入 bugs**
*   **我不容易得到图书馆的更新。**

**我最终使用了注入方法，基于测试库的 MIT 许可，这是有效的。一个例外是我移植了 jest-dom 而不是桥接它。为了弥合它，我不得不注入笑话和笑话界，这被证明是麻烦的。**

## **不会很慢吗？**

**注入测试库会对性能产生影响。但是，请考虑以下优化:**

*   **源 JS 脚本被缩小了(每个大约 250KB)。**
*   **JS 文件最多从文件系统中读取一次，之后从内存中读取。**
*   **每个脚本最多独立注入一次(直到出现页面导航或刷新)，并且仅在需要时注入。**

**注射时间大致在 20 至 40 毫秒之间变化。考虑到测试库的好处，我相信这是值得的。**

**这是这个库的源代码:**

**[](https://github.com/lsoares/selenium-testing-library) [## GitHub-lsoares/Selenium-testing-library:类似于

### 为什么？当我使用 Selenium 时，我不想依赖于 id、类等等。我是测试库的粉丝，因为…

github.com](https://github.com/lsoares/selenium-testing-library) 

**这是在 Maven Central 发布的库:**

 [## Maven 中央存储库搜索

### Maven 中央存储库维护者的官方搜索

search.maven.org](https://search.maven.org/artifact/com.luissoares/selenium-testing-library)**