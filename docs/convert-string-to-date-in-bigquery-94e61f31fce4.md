# 在 BigQuery 中将字符串转换为日期

> 原文：<https://medium.com/codex/convert-string-to-date-in-bigquery-94e61f31fce4?source=collection_archive---------2----------------------->

## 如何用解析和格式化数据轻松转换 SQL 中的类型

![](img/b39f83854768301e3714d0333dd1214a.png)

照片由[祝福 Ri](https://unsplash.com/@blessingeffect?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/calendar?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在 SQL 中，经常可以将字符串转换成日期。无论是在数据集成过程中还是在数据分析过程中。有了 *PARSE_DATE* 和 *FORMAT_DATE* ，BigQuery 中有两个强大的函数可以做到这一点。这是给你的一张小抄。

## 格式选项