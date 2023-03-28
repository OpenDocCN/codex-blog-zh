# 开始编码的超级简单的 PHP 教程—第 2 部分

> 原文：<https://medium.com/codex/a-super-simple-php-tutorial-for-beginning-to-code-part-2-13dbdcc121f2?source=collection_archive---------7----------------------->

![](img/80d737e4d7d5d3189c840452eacedc46.png)

在 [Unsplash](https://unsplash.com/s/photos/data-entry?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由 [Austin Distel](https://unsplash.com/@austindistel?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

# 本部分的目的:

*   能够在页面之间进行交流。例如，在一个页面上输入数据，提交数据，然后在另一个页面上显示。
*   [**点击此处返回第 1 部分**](https://johncoonrod.medium.com/a-super-simple-php-tutorial-for-beginning-to-code-part-1-db703d22d227)

# 先决条件

*   对 [HTML5 表单和输入](https://www.w3schools.com/html/html_forms.asp)的基本理解，注意——在过去，为了验证输入或提供弹出日历，您需要使用 javascript。现在 HTML5 已经内置了这一点。
*   对 HTML 表格的基本理解。

# 基本概念

浏览器有三种方式与服务器通信:

*   GET(提交表单时，将键/值对放在 URL 中，
*   POST(当您在 POST 模式下提交表单时，会传输到服务器，而不在 URL 中)
*   COOKIE(存储在您的浏览器中，并在每次调用时传输到服务器)
*   这些在服务器上以预定义的关联数组 [$_COOKIE](https://www.php.net/manual/en/reserved.variables.cookies.php) 、 [$_GET](https://www.php.net/manual/en/reserved.variables.get.php) 和 [$_POST](https://www.php.net/manual/en/reserved.variables.post.php) 读取。(所有这些实际上都是在一个更大的 array $_REQUEST 中一起传输的，但是您很少需要知道这一点)。

# $_GET 的示例

*   在您的目录中，创建[**index.php**](http://localhost:8080/index.php)作为一个入口页面，表明您想要查看一个包含许多行和列的表格。注意，这一页其实没有 hp。这在主页中并不罕见！

```
<html>
  <head><title>Entry Page</title>
</head>
<body>
  <h1>Enter number of rows and columns you want</h1>
  <form action=results.php>
    Rows: <input type=number name=rows required>
    Columns: <input type=number name=columns required>
    <input type=submit>
  </form>
</body>
</html>
```

*   创建第二个页面[【results.php】](http://localhost:8080/results.php)**即从表单跳转到。您将看到它在$_GET 数组中以键/值对的形式接收来自 index.php 的参数**

```
<html>
<head><title>Results</title></head>
<body>
  <table>
<?php
for($i=0;$i<$_GET["rows"];$i++) putRow($_GET["columns"]);
Function putrow($columns) {
  echo("<tr>\n");
  for($j=0;$j<$columns;$j++) echo("<td>$j</td>\n");
echo("</tr>\n");
}
?>
  </table>
  <p><a href=/>Return to home page</a></p>
 </body>
</html>
```

# **示例:$_COOKIE**

*   **通常，你会想让你的程序记住你之前告诉它的内容。您可以通过设置 cookies 来实现这一点。这有点棘手——cookie 存储在您的浏览器中，但在服务器向您的屏幕写入任何内容之前，它会通过来自服务器的消息进行设置，因此您必须首先运行 [setcookie](https://www.php.net/manual/en/function.setcookie) ($key，$value，$expires)函数。**
*   **默认情况下，cookies 会在您关闭浏览器时过期，但是您可能希望它们持续一百万秒，也就是大约两个星期。**
*   **让我们修改我们的 index.php 函数，要求您首先用您的名字登录。这是一种简单的身份验证。真正安全的身份验证可能取决于您在第 3 部分中使用的数据库类型。**

```
<?php // only do this if I know your name
$name='';
if(array_key_exists("name",$_GET)) {
  $name=$_GET["name"]; 
  setcookie("name",$name,time()+1000000);
}
if(!$name and array_key_exists("name",$_COOKIE)) $name=$_COOKIE["name"];
?>
<html><head><title>Entry Page</title></html>
<body>
<?php if($name) {
  echo("Hello $name!");
?>
<h1>Enter number of rows and columns you want</h1>
<form action=results.php>
Rows: <input type=number name=rows required>
Columns: <input type=number name=columns required>
<input type=submit>
</form>
<?php } else { ?>
Enter your name and hit enter <form><input name=name></form>
<?php } ?>
<a href=/?name=>Logout</a>
</body>
</html>
```

**这是怎么回事？在 PHP 逻辑表达式中！意思是“不”所以如果你从一个表单传入一个名字，它会被存储在一个 cookie 中。否则，从 cookie 中读取它。如果 name 不是空字符串，请说 hello 并提示输入行和列。否则，要求作为要在表单中输入的名称。“logout”选项传递一个空字符串。**

# **问题集**

*   **修改应用程序，将行和列的设置存储在 cookies 中，并在表单中提示时将它们显示为默认值。换句话说，让这些值“有粘性”**
*   **添加第三个页面，创建一个类似 results.php 的表，但是显示的数字以相反的顺序运行。**

# **在阅读关于数据库的第 3 部分之前，您可能想了解以下内容:**

*   **在结果页面中，如何将表导出和下载为 csv 或 json 文件。**
*   **在新页面中，如何从 csv 或 json 文件中导入表格。**

## **[继续第三部分—](https://johncoonrod.medium.com/a-super-simple-php-tutorial-for-beginning-to-code-part-3-working-with-databases-via-pdo-e32e0b929dfe) 数据库！**