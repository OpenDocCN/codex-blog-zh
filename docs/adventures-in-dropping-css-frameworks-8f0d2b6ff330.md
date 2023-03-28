# 放弃 CSS 框架的冒险

> 原文：<https://medium.com/codex/adventures-in-dropping-css-frameworks-8f0d2b6ff330?source=collection_archive---------1----------------------->

![](img/8f688404a8c1c40093b81ce6b107c720.png)

由 [Unsplash](https://unsplash.com/s/photos/behind-bars?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上的[Damir spanick](https://unsplash.com/@spanic?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

我已经成为杰森·奈特(Jason Knight)反对框架的[言论](/codex/stop-fighting-the-reason-css-exists-with-these-dumbass-frameworks-91732f5993c7)的粉丝。框架变成了监狱，假装可以节省你的时间，但最终会花费更多的时间和编码来克服它们的限制，同时使重构变得更加困难。

我以前写过一些关于如何在没有框架的情况下编写 PWA 的例子。然而，我的组织中最大的企业应用程序，被 22 个国家的人们用来分析和显示成千上万的数据记录，是用 Pure.css 编写的，这可能是最简约的框架。

这个应用程序诞生于 20 年前的 ASP，然后是 Tomcat/JSP，然后是在 Apache 上使用 CakePHP 的 PHP，然后是在需要 jQuery 时使用 [Bootstrap](https://getbootstrap.com) 的 Google Cloud，然后是 Pure。

Web 和这些框架不断发展，经常需要重构现有代码，以便使用最新版本。CakePHP v3 与 v2 不兼容。Bootstrap 4 与 Bootstrap 3 不兼容，它们似乎都需要！css 中的重要语句做任何样式。

实际上，他们似乎比没有框架时需要更多的 CSS！我已经开始着手一个没有 CSS 的项目，然后把它添加到 taste 中，试图通过简单地设计 HTML5 标签来实现不建立 CSS 类的事情。

然后奈特先生通过[栏杆向纯](/codex/pure-css-more-like-pure-junk-1ec2d26a1122)发出挑战。

好的。接受挑战。首先，我简单地删除了加载两个纯 CSS 文件的行。我很惊讶地看到他们添加了这么少的东西——基本上只是一些更漂亮的按钮、输入和表格样式(但在 HTML 中需要几十个 CSS 类引用)。最重要的是，导航条和栏。

Remi Goyard 发表了一个不需要 div 或 CSS 类的下拉导航栏的很好的例子。

现在使用 CSS flexbox 列变得非常容易。为了避免删除 Pure grid 使用的无数类，我简单地为初始化类 pure-g 添加了自己的 CSS，以便在相同的旧名称下，或者在更符合逻辑的“行”下建立一个 flex 容器然而，我不知道在没有类的情况下如何做到这一点，因为我需要

标签以不同的方式工作。

```
.pure-g, .row {
  display:flex; 
  flex-flow: row wrap; 
  justify-content: space-around;
}
```

对于一行表单，我希望它们靠左对齐，这可以通过:

```
form {flex-grow:1;}
```

我真正想摆脱的另一件事是 jquery，我需要它来处理[数据表](https://datatables.net/)(这也需要相当多的额外 CSS)。DataTables 是一个可爱的 jquery 插件，但是只在需要它的页面中包含它意味着那些页面需要在< head >中有一组不同的行。这很烦人，因为我希望只有一种方法来设置页面。

幸运的是，有一个用普通 javascript 编写的简单数据表,它可以在输出表格后，用正文中的两个简单脚本行启动，不需要额外的 CSS:

```
<script src="https://cdn.jsdelivr.net/npm/simple-datatables@latest"></script><script>const table = new simpleDatatables.DataTable("table")</script>
```

这确实需要将类“table”添加到每个表中，但是在写表的方法中只需要做一次。

下一个要做的是字体 Awesome，因为 Unicode 可以处理我使用它的几乎所有图标，通过:

```
function icon($name="print") {
  $icons=[
  "delete"=>"&#128465;",
  "download"=>"&#128229;",
  "edit"=>"&#9999;",
  "line-chart"=>"&#128200;",
  "pie-chart"=>"&#9685";
  "plus"=>"&#10133;",
  "plus-circle"=>"&#8853;",
  "print"=>"&#128424;",
  "undo"=>"&#8634;",
  "upload"=>"&#11014;"];
  return $icons[$name];
}
```