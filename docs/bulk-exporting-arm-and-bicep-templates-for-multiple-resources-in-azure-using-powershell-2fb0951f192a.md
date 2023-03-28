# 使用 Powershell 批量导出 Azure 中多个资源的 ARM 和 Bicep 模板

> 原文：<https://medium.com/codex/bulk-exporting-arm-and-bicep-templates-for-multiple-resources-in-azure-using-powershell-2fb0951f192a?source=collection_archive---------3----------------------->

自动化一切！

## 概观

Azure 门户一次只允许一个资源导出到 ARM 模板，目前，您根本无法通过门户直接导出 Bicep 模板。您可以将资源组的全部内容导出到包含组合资源的模板中，但这会将资源组合到一个模板中。