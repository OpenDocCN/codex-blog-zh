# 从 Kubernetes 配置图中动态读取变量，更新角度/反应环境

> 原文：<https://medium.com/codex/update-angular-react-environment-dynamically-reading-variables-from-kubernetes-configmaps-ae32b8d4021c?source=collection_archive---------1----------------------->

## 一个简单的分步指南，使用 Kubernetes ConfigMap 在运行时更新前端应用程序的环境，无需任何额外的库。

带有标准 docker 文件和基本 angular 和 react 应用程序的完整代码可在[此处](https://github.com/GaetanoPiazzolla/env-frontend)获得。请继续阅读，了解对实施要点的全面解释。