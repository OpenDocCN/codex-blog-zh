# 如何在 PHP 中使用位掩码

> 原文：<https://medium.com/codex/how-to-use-bitmasks-in-php-a594be597fd3?source=collection_archive---------6----------------------->

## 或者其他语言。是同一个概念。

位掩码和计算本身一样古老，不可否认，在内存稀缺和低级编程的时代更有用。但是没有什么可以阻止你在适当的时候使用它们。

PHP 在其许多内置函数中使用了位掩码。考虑:

```
json_encode($json, JSON_PRETTY_PRINT | JSON_UNESCAPED_SLASHES);
```

(我个人最喜欢的调用 json_encode 的方式……)