# 如何进行高级的 React 代码评审

> 原文：<https://medium.com/codex/how-to-do-a-high-level-javascript-code-review-76eff9c56d3?source=collection_archive---------19----------------------->

![](img/36843d8aff29f6e2393ba50be7b442e1.png)

你好。今天，您将了解一个有经验的 React 开发人员如何进行代码审查。我会和你分享我的清单，如果我还有什么需要补充的，请在评论中告诉我。

```
**Note**: Some basic things like checking for better variable/functions names etc. are not added since this is supposed to a high level code review guide.
```

## 1.新包

你应该寻找的第一件事是添加新的包。确保开发者添加的包是绝对必要的，并且没有简单的代码可供选择。每个开发人员的意图应该是使用简单的脚本而不是包，因为随着时间的推移，包的大小将会增长，并且会给应用程序带来很多问题。

## 2.复杂的编码

第二步是寻找棘手的代码。现在让我通过一个例子向您展示我所说的棘手代码是什么意思。

```
const hasData = !!result.length;
```

`!!`是向`true`或`false`赋值的一种方式。假设`result.length`等于`0`，第一个`0`将转换为`false`，然后第一个`!`将转换为`true`，然后第二个`!`将再次转换为`false`。所以简而言之，`!!result.length`将返回`false`。我们可以简单地使用`result.length`,而不是经历如此复杂的过程，如果在代码中的某个地方没有严格地从`hasData`中预期一个布尔值，那么它会完全一样地工作。所以一定要寻找那些不必要的复杂代码。

## 3.应用程序破解代码

考虑以下片段:

```
const profile = fetchProfile();
const username = profile.username;
```

开发人员大多很乐观，他们会假设`profile`总是有一个有效的对象，因为这个应用在他们的本地环境中从来没有崩溃过。现在假设由于某种原因`fetchProfile`失败并返回一个`null/undefined`值。`profile.username`会使应用程序崩溃，因为 JS 会抛出一个异常。现在这个问题有两个解决方案:

1.  使用异常处理
2.  使用`lodash`

我不喜欢到处使用第一种解决方案，因为这会使代码变得复杂，使用`lodash`有很多好处，而且它非常健壮和简单。

```
import _ from 'lodash';const profile = fetchProfile();
const username = _.get(profile, 'username');
const category = _.get(profile, 'category', 'social');
```

使用`lodash`的第一个好处是它不会让你的代码在变量没有想要的属性时中断。在上面的代码片段中，在`username`的情况下，如果`profile`有`undefined/null`或其他任何值或者`profile`有一个对象但不包含`username`，那么它将简单地返回`null`。在`category`的情况下，如果`profile`不包含`category`属性，它将始终返回您作为第三个参数传递的默认值。默认值是使用`lodash`的第二个好处。第三个优点是代码更简单，阅读起来不会太复杂。

## 4.处理人员隔离

考虑以下片段:

1.  `onChange`单独定义，然后传递到`Input`

```
const onChange = () => {
  ... 
  ...
  ...
  ...
  ...
}<Input
 fieldHasError={hasError}
 value={selectedDay}
 onChange={onChange}
/>
```

2.匿名函数直接传递给`Input`中的`onChange`

```
<Input
 fieldHasError={hasError}
 value={selectedDay}
 onChange={() => {
  ...
  ...
  ...
  ...
  ...
/>
```

如果代码传递函数很短(3-5 行),第二种方法是可以接受的，但是如果`onChange`有 50 多行呢？第二种方法会导致令人费解的代码，并且会损害可读性。这就是为什么我总是喜欢适当的代码分离，并且将单独定义的处理程序传递给组件，即使这些处理程序只包含几行代码。

## 5.进口

1.  杂乱无序的进口商品

```
**import** PropTypes **from** 'prop-types';
**import** DayPicker **from** 'react-day-picker';
**import** SVGIcon **from** '@ABC/components/SVGIcon';
**import** styled **from** 'styled-components';
**import** get **from** 'lodash/get';
**import** Input **from** '@ABC/components/Input';
**import** 'react-day-picker/lib/style.css';
**import** React, { useEffect, useRef, useState } **from** 'react';
**import** { dateTimeFromJSDate, isValidDate } **from** '../../utils/date';
```

2.隔离(且不杂乱)的进口

```
**import** React, { useEffect, useRef, useState } **from** 'react';
**import** PropTypes **from** 'prop-types';
**import** DayPicker **from** 'react-day-picker';
**import** styled **from** 'styled-components';
**import** get **from** 'lodash/get';
**import** 'react-day-picker/lib/style.css';**import** Input **from** '@ABC/components/Input';
**import** ClickOutside **from** '@ABC/utilities/click-outside';
**import** SVGIcon **from** '@ABC/components/SVGIcon';**import** YearMonth **from** './YearMonth';
**import** { dateTimeFromJSDate, isValidDate } **from** '../../utils/date';
```

现在你可以看到第二个片段更具可读性和组织性。我推荐的通常顺序是第三方导入，本地应用导入，然后本地导入。如果您有多个本地应用程序，请确保在导入每个应用程序后添加一个空格。在第二个代码片段中，我们有第三方导入，然后一些导入来自(local) `ABC`应用程序，然后我们有一些本地导入。