# 以非标准方式减少 SQL 注入

> 原文：<https://medium.com/codex/mitigating-sql-injection-in-the-non-standard-ways-f8b9675caa2b?source=collection_archive---------9----------------------->

![](img/0b330bb41dc128e6dcd384fc0b0c1c6a.png)

[SQL 注入](https://owasp.org/www-community/attacks/SQL_Injection)是一个极其严重的安全漏洞。从一开始，[注射](https://owasp.org/Top10/A03_2021-Injection/)就在 [OWASP 十大](https://owasp.org/www-project-top-ten/)中占据显著位置，这是有充分理由的。通常情况下，成功实施 SQL 注入所需的复杂程度很低，但造成的伤害却非常高。这是一个非常令人担忧的组合，我们至少希望最具破坏性的攻击会需要很多…