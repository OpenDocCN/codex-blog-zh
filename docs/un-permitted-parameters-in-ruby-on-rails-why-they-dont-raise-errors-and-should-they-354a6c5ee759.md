# Ruby on Rails 中不允许的参数——为什么它们没有引发错误，它们应该引发错误吗？

> 原文：<https://medium.com/codex/un-permitted-parameters-in-ruby-on-rails-why-they-dont-raise-errors-and-should-they-354a6c5ee759?source=collection_archive---------2----------------------->

![](img/f88665390475bfd538965b2bf2a80d97.png)

不要输入标志，贷方: *Unsplash*

在使用 Ruby on Rails 后端进行我最近的项目时，我注意到在使用批量赋值和强参数时有一个有趣的默认行为。如果你需要复习 RoR 的强参数，请点击这里查看文档！

对于这个项目，我只是在后端工作，所以我使用[邮递员](https://www.postman.com/)来检查我的路线，确保一切正常。在这里，我注意到任何不允许的参数都被自动过滤掉了，并返回一个 2xx 的 HTML 状态代码(“OK”或“Created”取决于场景)。我想知道为什么在试图传入一个不允许的参数时没有 4xx HTML 代码，所以我做了一些调查！让我们从头开始，建立这个项目的一部分。

## **在 Ruby on Rails 中构建一个简单的用户模型**

假设我们正在为我们的应用程序构建一个用户。我们首先使用内置的 rails 生成器来创建我们需要的一切；我们数据库的模型、控制器、路由和模式。`rails g resource User username password_digest --no-test-framework`。

> *我喜欢添加`--no-test-framework`，它删除了我们用户模型的规范文件，因为对于这个项目，我不会做任何 TDD(测试驱动开发)。

现在我们的用户模型已经创建好了，它有了`username`和`password_digest`的参数。

> 当使用安全加密我们密码的 [bcrypt](https://github.com/bcrypt-ruby/bcrypt-ruby) Ruby gem 时，我们使用`password_digest`。但是，在创建新用户时，我们仍然应该在 Postman 中的 JSON 主体中使用`{"password": "<insert-password>"}`来创建它。

为了允许使用`:password`和`:username`参数，我们将`user_params`方法作为私有方法添加到我们的`UsersController`中。确保将这些参数传递给我们的`create`方法。此时，我们的整个代码应该如下所示:

```
class UsersController < ApplicationControllerdef create
    user = User.create(user_params)
    render json: user, status: :created
endprivatedef user_params
    params.permit(:password, :username)
end
```

## 在邮递员中测试

现在，在 Postman 中，假设我试图创建一个新用户，但是为`email`传入一个参数。默认行为是过滤掉不允许的参数，保留上面定义的允许的参数。结果是创建了一个新用户，HTTP 代码为 201(按照上面代码中的定义创建的)，email 参数被过滤掉。

如果我们想知道在构建应用程序的开发过程中什么是不允许的参数，该怎么办？如果我们不知不觉地传入了一个错误的参数，并想在部署之前调试我们的程序，该怎么办？

幸运的是，我们有办法提出 rails 内置的错误！

# 如何为不允许的参数引发错误

查看 rails/strong_parameters 的文档，特别是对不允许的键[的处理。您将会看到，只要您拥有 rails 版本 4 或更高版本，就已经有了为不允许的参数引发错误的能力！说明在链接中，但是我还需要一些额外的步骤来呈现错误。我会这么做！](https://github.com/rails/strong_parameters#handling-of-unpermitted-keys)

1.  转到`config/environments/development.rb`并添加`config.action_controller.action_on_unpermitted_parameters = :raise`这将导致当您传递一个在更新或创建操作中不允许的参数时，弹出`*ActionController*::UnpermittedParameters`错误，只要您有`create!`或`update!`并在其后带有`!`。
2.  然后你可以在你想要的控制器文件的顶部使用一个`rescue_from`来挽救这个错误，就像这样:
    `rescue_from *ActionController*::UnpermittedParameters, with: :<name-of-method>`
3.  请确保将该方法添加到您的私有方法中！您可以自定义错误消息。我选择让它呈现不允许的参数的 key:value 对，如下所示:
    `render json: { "Unpermitted Parameters": [params.to](http://params.to/)_unsafe_h.except(:controller, :action, <include-your-permitted-params-here!!>)}, status: :unprocessable_entity`
4.  在`params`上使用`.to_unsafe_h`将会把所有的参数变成一个标准的 Ruby 散列。除非参数都被允许，否则不能在参数上使用`.to_h`，这就是`.to_unsafe_h`存在的原因。然后我使用`.except`从错误消息中排除允许的参数，因为我们只想显示导致错误的参数！

我们最终版本的`UsersController`将会是这样的:

```
class UsersController < ApplicationController
wrap_parameters format: []
rescue_from *ActionController*::UnpermittedParameters, with: :render_unpermitted_params_responsedef create
    user = User.create!(user_params)
    render json: user, status: :created
enddef update
    user = *User*.find(params[:id])
    user.update!(user_params)
    render json: user
endprivate def user_params
    params.permit(:password, :username)
enddef render_unpermitted_params_response
    render json: { "Unpermitted Parameters": params.to_unsafe_h.except(:controller, :action, :id, :username, :password).keys }, status: :unprocessable_entity
end
```

现在，当传入一个不允许的参数时，我们会收到一条错误消息，显示哪些键是不允许的，同时还会显示一个 HTTP 状态代码 422。太好了！有用！但是……什么时候需要有这种行为呢？

> 我们改变行为是为了发展，而不是生产。为什么不两者都要？

在生产中，将任何不允许的参数作为默认行为过滤掉更有意义。我只建议在开发过程中加入这个行为来调试。理想情况下，在一个完整的全栈应用程序中，您应该创建自己的前端来控制表单，并确定客户端传入什么信息，同时利用 RoR 验证来检查它们的响应。

> 应用程序的客户端不应该能够传入不允许的*键*，只要你记住这一点就可以了！

**希望这为您的调试工具箱提供了另一个工具！编码快乐！**