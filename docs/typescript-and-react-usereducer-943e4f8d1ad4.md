# TypeScript 和 React useReducer

> 原文：<https://medium.com/codex/typescript-and-react-usereducer-943e4f8d1ad4?source=collection_archive---------0----------------------->

在或多或少复杂的 reducer 的代码评审过程中，我看到了下面的 action 类型声明:

但是在这种情况下，我们没有使用 TypeScript 提供的所有功能。只有一件事可能是有效的，那就是我们用可用选项的特定子集来传递动作类型，仅此而已。