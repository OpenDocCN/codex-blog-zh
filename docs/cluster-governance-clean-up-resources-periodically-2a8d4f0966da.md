# 集群治理—定期清理资源

> 原文：<https://medium.com/codex/cluster-governance-clean-up-resources-periodically-2a8d4f0966da?source=collection_archive---------9----------------------->

## 使用 client-go、GCP API 和 Github API 构建清理资源的工具

作为平台开发人员，我们通常将 IaaS 概念普遍化，并鼓励开发人员使用 YAML 创建和管理资源，然后存储在 Github 中，这样我们就可以获得免费的版本管理、历史记录和更好的可见性。通过在 CI/CD 中集成一些 Gitops 工具，可以实现对资源的统一验证、变异、预见，并最终部署到集群中。现在，这种模式是…