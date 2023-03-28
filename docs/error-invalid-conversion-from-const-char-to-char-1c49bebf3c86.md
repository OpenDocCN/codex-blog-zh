# 错误:从“const char*”到“char*”的转换无效

> 原文：<https://medium.com/codex/error-invalid-conversion-from-const-char-to-char-1c49bebf3c86?source=collection_archive---------2----------------------->

## 这个常见错误的完整解决方案

![](img/3dc87cd72955260081c17fa9c6f0b7e5.png)

[Elisa Ventur](https://unsplash.com/@elisa_ventur?utm_source=medium&utm_medium=referral) 在[Unplash](https://unsplash.com?utm_source=medium&utm_medium=referral)上拍摄的照片

## 试图从 std::字符串转换为 char*

## 示例代码:

```
char* message = NULL;
message = std::string("blah blah").c_str();
```