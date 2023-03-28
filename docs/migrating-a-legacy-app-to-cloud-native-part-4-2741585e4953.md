# 将传统应用迁移到云原生环境—第 4 部分

> 原文：<https://medium.com/codex/migrating-a-legacy-app-to-cloud-native-part-4-2741585e4953?source=collection_archive---------0----------------------->

![](img/a28fe7066c9be0af3f955771a19bb67b.png)

[斯蒂夫·约翰森](https://unsplash.com/@steve_j?utm_source=medium&utm_medium=referral)在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 上拍照

在本系列的第 4 部分中，我添加了 Amplify 存储，并探讨了它的安全模型以及如何定制它…

*   第一部分:背景
*   [第二部分:需求&架构](/@kernwig/migrating-a-legacy-app-to-cloud-native-part-2-533dfebd38fb)
*   [第 3 部分:认证](/@kernwig/migrating-a-legacy-app-to-cloud-native-part-3-4bb187fea485)

Amplify 有一个存储模块，可以由 S3 或 DynamoDB 在 AWS 中支持。回到第 2 部分，在探索应用程序需求时，我注意到 S3 将用于存储用户设置和数据集合。简而言之，DynamoDB 不能使用，因为:

1.  记录被限制在 400 KB 以内，我不想将一个集合限制在这个大小。
2.  Amplify 的存储 API 可能使用 S3 *或*DynamoDB；对于不同的数据，我们不能两者都用。因此，用户设置，虽然很小，也会进入 S3。

*注意:我后来发现，虽然 CLI 提供了 DynamoDB 存储选项，但任何语言的 Amplify 库都不支持它。*

我们将使用 DynamoDB 进行搜索(参见需求)，但这将是以后的事情。现在，我们只想保存并回读用户设置和集合。

如果在这个系列中还不清楚，我写这个是因为我试图用*放大。这是一个关于*旅程*的系列，而不是简单的操作指南。*

# 添加存储

我们在哪里停下来的？

```
$ amplify status

Current Environment: dev

| Category | Resource name   | Operation | Provider plugin   |
| -------- | --------------- | --------- | ----------------- |
| Hosting  | S3AndCloudFront | No Change | awscloudformation |
| Auth     | sqacauth        | No Change | awscloudformation |

Hosting endpoint: http://sqac-amplify-20190817123020-hostingbucket-dev.s3-website-us-west-2.amazonaws.com
Hosted UI Endpoint: https://sqac-dev.auth.us-west-2.amazoncognito.com/
```

我只有托管和授权。因此，让我们添加存储:

```
$ amplify add storage
? Please select from one of the below mentioned services Content (Images, audio, video, etc.)
? Please provide a friendly name for your resource that will be used to label this category in the project: storage
? Please provide bucket name: sqac-amplify-user-data
? Who should have access: Auth and guest users
? What kind of access do you want for Authenticated users? create/update, read, delete
? What kind of access do you want for Guest users? read
? Do you want to add a Lambda Trigger for your S3 Bucket? No
Successfully added resource storage locally

Some next steps:
"amplify push" builds all of your local backend resources and provisions them in the cloud
"amplify publish" builds all of your local backend and front-end resources (if you added hosting category) and provisions them in the cloud
```

我选择“图像、音频、视频等”内容，而不是 NoSQL 选项。这就是你如何得到 S3，而不是 DynamoDB。然后我给这些东西起了友好的名字，而不是随机生成的默认值。

在探索需求时，我声明我希望第一次访问我的应用程序的人不需要首先创建一个帐户，因此我选择提供来宾只读访问。经过身份验证的用户拥有完全读写权限。

虽然我们最终会在 S3 桶中添加一个 Lambda 触发器，但这是一个很大的任务，所以我现在回答不。Amplify 允许我们稍后使用`amplify storage update`命令来改变事情。

接下来，让我们部署这些变化…

```
$ amplify push

Current Environment: dev

| Category | Resource name   | Operation | Provider plugin   |
| -------- | --------------- | --------- | ----------------- |
| Storage  | storage         | Create    | awscloudformation |
| Hosting  | S3AndCloudFront | No Change | awscloudformation |
| Auth     | sqacauth        | No Change | awscloudformation |
? Are you sure you want to continue? Yes
⠴ Updating resources in the cloud. This may take a few minutes...

… A few dozen lines of CloudFormation output over a few minutes … 

✔ All resources are updated in the cloud
```

没什么。在 AWS 控制台(网站)中，我看到一个新的`sqac-amplify-user-data-dev`空桶。(`-dev`是我的环境名；它被附加到任何东西上以支持多种环境。🥳)

所以我们结束了，对吗？嗯……那要看情况。

# 安全策略和参数

*警告:我将在这里深入探讨 AWS 政策和云形成。你可以跟着* [*中的文件第四部分拉请求*](https://github.com/kernwig/sqac-amplify/pull/3) *，或者只是让你的目光呆滞。*😳

我仔细研究了定义存储(包括策略)的新 CloudFormation 堆栈，发现这并不是我想要的。我希望用户将他们的私有数据保持在私有访问级别，将共享数据保持在受保护的访问级别，除此之外别无其他。虽然政策也允许用户在公共区域写访问，但这不是很有用，因为任何人都可以在这里写任何东西，任何人都可以修改或删除它。我真的不想那样。啊哦？但后来我意识到我正在查看堆栈的“参数”部分，在 amplify 文件夹中有一个`parameters.json`文件。💡这里是`parameters.json`:

```
{
   "bucketName": "sqac-amplify-user-data",
   "authPolicyName": "s3_amplify_7405df3b",
   "unauthPolicyName": "s3_amplify_7405df3b",
   "authRoleName": {
       "Ref": "AuthRoleName"
   },
   "unauthRoleName": {
       "Ref": "UnauthRoleName"
   },
   "selectedGuestPermissions": [
       "s3:GetObject",
       "s3:ListBucket"
   ],
   "selectedAuthenticatedPermissions": [
       "s3:PutObject",
       "s3:GetObject",
       "s3:ListBucket",
       "s3:DeleteObject"
   ],
   "s3PermissionsAuthenticatedPublic": "s3:PutObject,s3:GetObject,s3:DeleteObject",
   "s3PublicPolicy": "Public_policy_9efc80af",
   "s3PermissionsAuthenticatedUploads": "s3:PutObject",
   "s3UploadsPolicy": "Uploads_policy_9efc80af",
   "s3PermissionsAuthenticatedProtected": "s3:PutObject,s3:GetObject,s3:DeleteObject",
   "s3ProtectedPolicy": "Protected_policy_7b753c06",
   "s3PermissionsAuthenticatedPrivate": "s3:PutObject,s3:GetObject,s3:DeleteObject",
   "s3PrivatePolicy": "Private_policy_7b753c06",
   "AuthenticatedAllowList": "ALLOW",
   "s3ReadPolicy": "read_policy_9efc80af",
   "s3PermissionsGuestPublic": "s3:GetObject",
   "s3PermissionsGuestUploads": "DISALLOW",
   "GuestAllowList": "ALLOW",
   "triggerFunction": "NONE"
}
```

酷毙了。让我们看看我在这里想要什么…

```
"s3PermissionsAuthenticatedPublic": "s3:GetObject",
```

我想那就行了。我只是取消了在公共区域写东西的许可。任何人，包括客人，仍然可以从中阅读。只有我手动放入的数据才能存在，因此我可以将它用于来宾“演示模式”内容。(我不会的，继续读下去。)这里没有什么能完全清楚地说明*受保护的*功能是如何工作的，但这些只是配置选项，而不是实际的策略。为此，我翻遍了 Amplify 创建的`s3-cloudformation-template.json`文件。JSON 格式有点冗长，所以我在 AWS 控制台中弹出 CloudFormation Designer (GUI)工具，并使用它来选择策略，以便在更简洁的结果 YAML 中查看。

我发现的第一个有趣的地方是:

```
S3GuestReadPolicy:
    DependsOn:
      - S3Bucket
    Condition: GuestReadAndList
    Type: 'AWS::IAM::Policy'
    Properties:
      PolicyName: !Ref s3ReadPolicy
      Roles:
        - !Ref unauthRoleName
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Action:
              - 's3:GetObject'
            Resource:
              - !Join 
                - ''
                - - 'arn:aws:s3:::'
                  - !Ref S3Bucket
                  - /protected/*
          - Effect: Allow
            Action:
              - 's3:ListBucket'
            Resource:
              - !Join 
                - ''
                - - 'arn:aws:s3:::'
                  - !Ref S3Bucket
            Condition:
              StringLike:
                's3:prefix':
                  - public/
                  - public/*
                  - protected/
                  - protected/*
```

如果您还没有注意到，Amplify 通过在 S3 密匙前面加上`public`、`protected`或`private`来管理访问级别，后面是认证用户的 ID。(您可以将它们视为文件系统文件夹，尽管从技术上讲它们并不是。)因此，假设一个 Cognito ID 为 123 的用户将在`private/123/`中存储私有数据，在`protected/123/`中存储受保护的数据。注意这里有一个`public/*`前缀匹配条件。这是不是意味着用户只能在自己的公共区域写？如果是，公开和受保护的区别是什么？我必须再挖一些！

这个政策确实回答了我的一个问题:一个访客用户*可以阅读受保护的内容，而不仅仅是公开的。我可能根本不需要公开。*

`S3AuthReadPolicy`(针对经过身份验证的用户，而不是来宾)与此类似，但是多了两个前缀，允许读取用户的私有数据。因此，经过身份验证的用户可以阅读除其他用户的私人部分之外的任何内容:

```
Condition:
      StringLike:
        's3:prefix':
          - public/
          - public/*
          - protected/
          - protected/*
          - 'private/${cognito-identity.amazonaws.com:sub}/'
          - 'private/${cognito-identity.amazonaws.com:sub}/*'
```

有一个`S3GuestUploadPolicy`允许客人上传到一个`uploads/`前缀，但是它被默认参数`s3PermissionsGuestUploads`设置为`DISALLOW`而无效。👍这导致了针对认证用户的`S3AuthUploadPolicy`，这允许用户将文件转储到`uploads/`前缀中，而没有办法读回它。我想这一定是一个允许上传的特性，然后由触发的 Lambda 处理。我不需要这样的上传，所以在`parameters.json`中，我将`s3PermissionsAuthenticatedUploads`设置为`DISALLOW`，就像为客人设置的参数一样。

继续，我看到一个`S3AuthPublicPolicy`，它与我已经更改为`S3:GetObject`的`s3PermissionsAuthenticatedPublic`参数相关联；因此，经过身份验证的用户只能读取，不能写入公共区域。我发现这适用于 public 前缀中的任何地方，对用户 ID 没有任何限制:

```
Resource:
      - !Join 
        - ''
        - - 'arn:aws:s3:::'
          - !Ref S3Bucket
          - /public/*
```

与`S3AuthProtectedPolicy`相比，哪个*限制对用户 ID 的写活动:*

```
*Resource:
       - !Join 
          - ''
          - - 'arn:aws:s3:::'
            - !Ref S3Bucket
            - '/protected/${cognito-identity.amazonaws.com:sub}/*'*
```

*`S3AuthPrivatePolicy`看起来几乎一样，保护私有文件夹:*

```
*Resource:
       - !Join 
          - ''
          - - 'arn:aws:s3:::'
            - !Ref S3Bucket
            - '/private/${cognito-identity.amazonaws.com:sub}/*'*
```

*那么有什么区别呢？向上滚动回到`S3GuestReadPolicy`和`S3AuthReadPolicy`。虽然`S3AuthProtectedPolicy`和`S3AuthPrivatePolicy`涵盖了用户可以*写入*的内容，但是早期的策略允许任何人*读取*前缀。*

*我一直很困惑为什么 Amplify 的文档没有明确定义访问角色:公共、受保护和私有。以前找不到任何解释，但是看到现在有一些细节[这里](https://aws-amplify.github.io/docs/js/storage#using-amazon-s3)。但是，我还是觉得有点暧昧。现在我看到了一些理由:这取决于你！编辑`parameters.json`文件让您决定行为。然而，这也没有记录在案。😔*

*基于这个快速的研究，我总结出了我*认为的规则。不过，我可能在某些方面弄错了；没有保证。假设您为经过身份验证的用户选择了 guest 访问和读写，那么这是默认行为，如果您愿意，可以更改该参数:**

*   *经过验证的用户可以上传到此。(`s3PermissionsAuthenticatedUploads` )
    客人不得。(`s3PermissionsGuestUploads`)*
*   *`public/*` 认证用户可以读写。(`s3PermissionsAuthenticatedPublic` )
    宾客可阅。(`s3PermissionsGuestPublic`)*
*   *`protected/` 任何人都可以阅读(不可配置)*
*   *`protected/{user-id}/*` 任何人都可以读(不可配置)
    匹配的认证用户可以写(`s3PermissionsAuthenticatedProtected`)*
*   *`protected/` 禁止进入*
*   *`protected/{user-id}/*` 匹配的认证用户可以读写(`s3PermissionsAuthenticatedPrivate`)*
*   *认证用户可以列出他们可以阅读的任何前缀的内容(`AuthenticatedAllowList`*
*   *客人可以列出他们可以阅读的任何前缀的内容(`GuestAllowList`*
*   *如果在 CLI 中选择了无访客访问，则未经身份验证的用户没有访问权限。*

*这些属性的可能值是任何一个`s3:ListBucket`、`s3:GetObject`、`s3:PutObject`、`s3:DeleteObject`或简单的`DISALLOW`的逗号分隔列表。*

*参数文件还包括`selectedGuestPermissions`和`selectedAuthenticatedPermissions`，但是我没有在 CloudFormation 模板中看到它们。🤔🤦‍♂️*

*对于 SqAC，我修改了参数以完全禁用 upload 和 public 特性，同时保持 protected 和 private 的默认行为。*

# *CORS？*

*作为使用亚马逊 S3 文档进行放大的*存储* / *的一部分，[这个位](https://aws-amplify.github.io/docs/js/storage#amazon-s3-bucket-cors-policy-setup)告诉你在你的 S3 桶上手动配置 CORS 策略。😲这将打破基础设施代码(IaC)和多种环境的易用性！幸运的是，文档是一个转移注意力的话题——Amplify 已经按照文档设置了存储桶策略。(我已经提交了一份 [bug 报告](https://github.com/aws-amplify/docs/issues/918)来移除这个。)**

# *未完待续…*

*在[第 4 部分拉动请求](https://github.com/kernwig/sqac-amplify/pull/3)中找到所有这些。*

*我本打算将更新客户端应用程序以使用存储作为这篇文章的一部分，但安全策略分析已经将这篇文章变成了一篇大文章，下一篇文章也将成为一篇好文章。*

*下次再来… *使用*放大存储！*