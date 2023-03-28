# 使用 Python 模拟具有相关价格路径的多资产篮子

> 原文：<https://medium.com/codex/simulate-multi-asset-baskets-with-correlated-price-paths-using-python-472cbec4e379?source=collection_archive---------2----------------------->

## 利用 NumPy 的乔莱斯基分解功能，轻松生成多项资产的相互依赖的价格变动。

![](img/63fc7ec7e235afb644c6fec1c7ca87b1.png)

相关矩阵并不是生活中唯一分解的东西(图片由 [PublicDomainPictures](https://pixabay.com/nl/users/publicdomainpictures-14/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=316437) 通过 [Pixabay](https://pixabay.com/nl/?utm_source=link-attribution&amp;utm_medium=referral&amp;utm_campaign=image&amp;utm_content=316437)

作为一名金融工程师，金融市场的布朗模型是你学习的第一个构造之一，有了它，一个方便的…