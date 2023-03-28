# 💪使用 Azure PowerShell 部署 Bicep 模板的五个最佳实践

> 原文：<https://medium.com/codex/five-best-practices-for-deploying-bicep-templates-using-azure-powershell-904f524df1f2?source=collection_archive---------3----------------------->

通过 PowerShell 采用 Bicep 部署的最佳实践

![](img/4042f201ca09e050b413b41644fb1465.png)

作为云工程师，我们知道确保我们的同事在 Azure 中部署环境时遵循和采用最佳实践和流程的挑战。如果没有正确的实践基准，云工程师在调配云基础架构时可能会面临一系列挑战。

这就是 Bicep 的基础设施即代码的最佳实践的来源。在本文中，我将与您分享使用 Azure PowerShell 部署 Bicep 模板的五个最佳实践。

出色的基础设施即代码解决方案使 DevOps 团队能够更智能地规划、更好地协作、更快地交付服务，并将摩擦降至最低。

Bicep 是作为一种开源的、特定于领域的语言(DSL)而诞生的，旨在简化你在 Azure 中的声明式部署体验。二头肌位于手臂模板的顶部。它提供了一个透明的抽象层，使得在 Azure 中读写基础设施代码变得更加容易。

也就是说，为了使用 Azure PowerShell 直接处理 Bicep 文件，您需要安装 [Bicep CLI](https://github.com/Azure/bicep/blob/main/docs/installing.md?WT.mc_id=AZ-MVP-5000671) 。

*这里是使用 Azure PowerShell 部署 Bicep 模板的五个最佳实践:*

# 1.定义部署范围。

如果你想将一个应用服务部署到 Azure，你可能想将部署定位到一个资源组。

想象一下您想要创建三个资源组，然后为每个资源组部署一个应用服务的场景。在这种情况下，您可以将部署定位到订阅级别。

与 ARM 模板类似，您可以将部署定位到资源组、订阅、管理组或租户。

要定位资源组，请使用下面的 cmdlet:

```
**New-AzResourceGroupDeployment** -ResourceGroupName <resource-group-name> -TemplateFile <path-to-bicep>
```

若要定位订阅，请使用下面的 cmdlet:

```
**New-AzSubscriptionDeployment** -Location <location> -TemplateFile <path-to-bicep>
```

若要定位管理组，请使用下面的 cmdlet:

```
**New-AzManagementGroupDeployment** -ManagementGroupId <management-group-id> -Location <location> -TemplateFile <path-to-bicep>
```

要定位租户，请使用下面的 cmdlet:

```
**New-AzTenantDeployment** -Location <location> -TemplateFile <path-to-bicep>
```

注意:如果您部署到资源组以外的任何目标，我们必须定义“*位置*”。

# 2.定义部署名称

始终定义部署名称。为每个部署提供一个唯一的名称，以便以后快速找到它们。你的团队和同事会很感激的。

下面的代码显示了为您的部署创建唯一名称的快捷选项。

```
**$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="YourDeploymentName"+"$today"**New-AzResourceGroupDeployment -Name $deploymentName -ResourceGroupName YourResourceGroup -TemplateFile .\appServicePlan.bicep
```

通过为部署使用唯一的名称，您可以并行执行多个部署，而无需覆盖部署的现有执行。

# 3.使用假设操作来验证您的二头肌模板

在执行部署之前，可以使用假设分析操作预览更改及其对环境的影响。

使用假设分析，Azure Resource Manager 将在您执行部署之前为您提供环境中潜在变化的摘要。

这里有一个使用 Bicep 在 Azure 中创建基于 Linux 的虚拟机时如何使用假设操作的例子。

# 4.构建你的二头肌模板参数，以支持团队协作。

参数有助于使二头肌模板可重用。参数可用于自定义部署的行为。尽可能多地为参数提供描述

要传递参数值，可以使用内联参数或参数文件。总是使用“ *@secure()* ”装饰器在参数中传递秘密或密码。

您还可以利用其他装饰器来控制参数值的定义。查看这篇[文章](/codex/azure-bicep-working-with-parameters-best-practices-92454074e380)，了解如何在二头肌训练中采用最佳参数实践。

要传递内联参数，请引用如下所示的“TemplateParameterFile ”:

```
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\YourBicepTemplates\vnet.bicep `
  **-TemplateParameterFile** c:\YourBicepTemplates\vnet.parameters.json
```

要传递外部文件，您可以引用 Uri:

```
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\YourBicepTemplates\web-app-sql-db.bicep `
  -TemplateParameterUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-sql-database/azuredeploy.parameters.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-sql-database/azuredeploy.parameters.json)
```

# 5.使用 Bicep 记录常见部署错误和故障排除流程

解决调配云基础架构中的技术和组织挑战。虽然 Bicep 模板应该灵活创作，但是理解和记录以前部署中的常见错误以及团队成员如何克服这些挑战也很重要。

有关于 ARM 模板部署中最常见错误的 Microsoft 文档。

如果能有类似的 Bicep 文档就太好了，希望微软 Azure 团队能尽快解决这个问题。

# 结论

采用最佳实践不仅仅适用于大型组织，您越早在组织中采用基础架构即代码部署的最佳实践，就能越早克服调配云基础架构的潜在挑战。

欢迎在评论中分享对你最有效的方法和反馈！

[*在此加入****azin sider****邮箱列表。*](http://eepurl.com/gKmLdf)

*-戴夫·r*