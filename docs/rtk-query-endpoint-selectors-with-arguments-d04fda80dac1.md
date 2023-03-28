# 带参数的 RTK 查询端点选择器

> 原文：<https://medium.com/codex/rtk-query-endpoint-selectors-with-arguments-d04fda80dac1?source=collection_archive---------2----------------------->

## 有时候显而易见的事实并非如此。Redux 团队帮我找到了答案。

![](img/b14c53ab8d01c16619f4bf0b681b627c.png)

图片由[阿雷克索查](https://pixabay.com/users/qimono-1962238/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=1767563)从皮查拜拍摄

我以前说过，我认为 [RTK 查询](/codex/can-we-all-just-admit-react-hooks-were-a-bad-idea-795505b0485d)对 React 钩子产生的问题有很大帮助，主要是为你做了这么多事情，你不需要在`useMemo`、`useEffect`和`useState`中来回传递小块状态。所以我一直很投入…