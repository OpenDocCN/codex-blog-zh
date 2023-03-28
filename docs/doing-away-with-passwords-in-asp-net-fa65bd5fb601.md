# 在 ASP 中取消密码。网

> 原文：<https://medium.com/codex/doing-away-with-passwords-in-asp-net-fa65bd5fb601?source=collection_archive---------7----------------------->

## 也许替代品应该有更大的吸引力，让用户少一些烦恼。

![](img/825f85ce7901fc717ecef32a15eeb8e3.png)

由 [olieman.eth](https://unsplash.com/@moneyphotos) 上 [Unsplash](https://unsplash.com/photos/r8VbpgMS6Uc)

当我第一次听说 Medium 时，远在我真正加入之前，我记得我被它的登录系统惊呆了。我看着我的老板通过查看他的电子邮件登录他的账户，并使用那个链接，我问“它不需要任何密码吗？”

几年后，在阅读了大量关于如何保护网站、应用程序和设备的资料后，我开始意识到这种方法在安全性方面的诸多优势。

# 基本原理

对于开发应用程序、网站或其他任何处理用户身份验证的软件的任何人来说，最困难的安全任务之一是可靠地，尤其是安全地验证该用户。

处理用户的密码是一种将所述密码暴露给许多可能的攻击媒介的操作:

*   用户必须键入密码，这使得它容易受到键盘记录器的攻击
*   客户端应用程序(可能是浏览器，也可能是移动应用程序)需要将密码发送到服务器，因此任何数量的中间人或类似邪恶双胞胎的技术都可以工作，特别是如果用户不是特别懂安全的话
*   服务器接收并需要验证密码，因此，至少在一个实例中，某种形式的密码存储在服务器内存中的某个地方，使其容易受到更高级的漏洞的攻击
*   需要存储密码，或者存储一个散列值，以便以后可以对其进行验证，这样，如果操作不正确，就容易受到攻击者的攻击

现在应该很明显，针对密码的攻击面是巨大的，而且，尽管最近使用上述任何一种手段成功攻击变得越来越困难，但我们仍然面临着一个巨大的难题:

用户。

如果用户选择“password”作为他的密码，那么您的密码处理机制有多安全并不重要。如果你的客户的倒霉员工在众目睽睽之下把他们的密码写在便利贴上，那么你有一个强制使用非常强的密码的密码策略也没有什么区别。如果您有多因素身份验证，如果您不喜欢的用户不能区分实际的登录页面和钓鱼页面，这都无关紧要。

当然，这并不是什么新鲜事。该领域的主要参与者在保证用户安全方面遇到了真正的麻烦。大多数这样的玩家投入了大量的时间和精力来尽可能地保护东西。以一个 [Active Directory](https://en.wikipedia.org/wiki/Active_Directory) 域的功能和灵活性为例，通过查看其内部概念和协议，想象一下这些年来它遇到了多少挑战。或者就拿[脸书](https://www.facebook.com/)来说，重大数据泄露事件多到有[页面](https://selfkey.org/facebooks-data-breaches-a-timeline/) [专用](https://firewalltimes.com/facebook-data-breach-timeline/)到[分析](https://www.forbes.com/sites/kateoflahertyuk/2021/04/06/facebook-data-breach-heres-what-to-do-now/) [它们](https://haveibeenpwned.com/PwnedWebsites#Facebook)。就连强调安全是其首要任务之一的苹果公司也未能幸免。

所以任何一个新应用程序的开发者都应该问自己:我有足够的知识和经验来解决这个问题吗？我的解决方案真的能与历史悠久的身份认证解决方案相提并论吗？答案是，在大多数情况下，可能不会。

不要误解我的意思，软件开发和架构技能很可能不缺乏足够勇敢的人来接受这样的挑战。但是一个开发人员或一个小团队根本没有时间或资源来实现(尤其是维护！)以与该领域主要参与者相同的方式保护解决方案。并且有这样的态度“好吧，脸书是巨大的，谁会来 lil' ol' Jackson O'Coder 的网站？”一点帮助也没有:请记住，脸书有足够的资源从巨大的数据泄露中幸存下来(就像它在过去多次发生的那样)，但仅仅一个小的泄露就可能足以让你的企业从地球上消失。你可能最终没有人认为你值得他们花时间，但是，通常，这也意味着你的软件也不会带来很多客户。

# 毕竟，为什么要废除密码呢？

因此，在阅读了基本原理后，您仍然不相信，如果存在比使用密码更好的选择，您一定应该采用它，让我们来看看取消密码的一系列优点:

1.  不使用密码不会暴露他们的安全。这对用户来说是一个很大的优势，因为大多数人在多个应用程序中重复使用他们的密码，所以攻击者很有可能获得一个密码的访问权，从而获得这个人正在使用的大多数应用程序的访问权
2.  不使用密码大大降低了消耗的资源量，因为不需要额外的加密和/或散列，也不需要为此编写代码或分配存储空间
3.  不使用密码丝毫不会妨碍任何框架正确认证用户的能力，也不会阻止用户维护会话
4.  不使用密码严重降低了数据泄露时的责任，也严重限制了攻击者收集的数据的有用性。这种观点很可能会让任何企业的法律和财务部门欢呼雀跃，尤其是那些不会从用户那里收集太多数据的应用程序

现在，进一步的优势来自于你可以选择废除密码的特定方式。作为一名 ASP.NET 开发者，和我一样，对于外部登录提供者有现成的解决方案和文档，像[脸书](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/facebook-logins)、[谷歌](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/google-logins)、[推特](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/twitter-logins)、[微软](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/microsoft-logins)、 [Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/quickstart-v2-aspnet-core-webapp) 、 [Windows 认证](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/windowsauth)或[其他通用提供者](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/other-logins)，甚至多因素认证和 TOTP 方案。我不会在这里详细介绍，但是可以说，将身份验证委托给比您的开发团队更强大的第三方在开发效率、安全性以及用户参与度方面有其优势。

然而，本文不是关于委托给第三方。已经有大量相关文档了。但是，尽管它们有优势，但它们有一个严重的缺点:它们是第三方，因此，会使您的应用程序进入数据保护、跟踪和隐私的监管领域。这对于你的应用程序来说完全没问题，所以，无论如何，如果这种方法适合你的目的，就去用它吧。但如果没有，可能需要一个单独的方法。

# 输入个人物品

可以有把握地假设，如果有人使用你的软件，他们至少可以访问一个电子邮件地址，可能还有一个电话号码。几乎可以肯定的是，他们会使用某种智能手机。

近年来最大的安全改进之一是将智能手机融入日常生活。它在许多方面使事情变得更加安全，但最重要的安全改进实际上根本不是安全改进:让每个人都能随手拿到电子邮件的能力。

因此，人们连接笔记本电脑，将其连接到无密码的公共 Wi-Fi 热点，并导航到或多或少(通常不太安全)的网页，像 hotshot69@smartypants-email.com 一样查看他们喜欢的域名电子邮件的日子已经一去不复返了。此后，大多数用户都转向了少数几家标准化的提供商，然后这些提供商就可以开始正确地对待用户设备和服务器之间的网络，就像它实际上是一个不安全、最有可能是敌对的环境一样。通过将安全性集中在几个提供商身上，这些提供商和智能手机制造商已经设法使电子邮件足够安全。

除此之外，还有电话网络的问题，每个设备都保证有一个严格监管的通信通道，如果不投入大量的金钱和时间，很难以实际的方式攻击。

最重要的是，让设备尽可能安全的竞争还在继续。沙箱、锁定机制、内部安全、加密、查找我的设备技术、远程加锁选项等。所有这些协同工作，使每个人都拥有一个理想的多因素一体化身份验证设备。

# 所以，让我们开始吧

既然我已经解释了我们是如何接近的，我将做一个非常简短的代码介绍如何在 ASP.NET 正确地做这件事。出于这个目的，我将假设我们使用。NET 6，并且您已经掌握了一些如何正确处理基本 ASP.NET 应用程序的知识。

虽然我将使用电子邮件来尝试对用户进行身份验证，但您可能希望使用的任何其他方式(如短信、TOTP 或推送通知)也应该使用相同的原则。

## 第一步

我们首先需要在 DI 容器中设置身份和授权服务:

```
// Authentication and authorization
services.AddIdentity<IdentityUser<int>, IdentityRole<int>>()
    .AddEntityFrameworkStores<MyDbContext>()
    .AddDefaultTokenProviders();

services.AddAuthorization(
    options =>
    {
        options.AddPolicy("AdministratorsOnly",
            policyBuilder =>
            {
                policyBuilder.RequireRole("Administrators");
            });
    });
```

请注意，我在具有默认令牌提供者的实体框架数据存储中使用了具有整数主键的标准 IdentityUser 和 IdentityRole 实体。请根据自己的情况随意调整。在我的例子中，MyDbContext 类被简单地定义为:

```
public class MyDbContext : IdentityDbContext<IdentityUser<int>, IdentityRole<int>, int>
```

我还添加了一个“AdministratorsOnly”策略作为示例策略(检查用户是否属于“Administrators”组)，当然，您可以根据自己的需要进行调整。

接下来是添加所需的中间件:

```
app.UseAuthentication();
app.UseAuthorization();
```

根据中间件指示，在使用路由和端点的情况下，需要在[用户路由](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.userouting)和[用户端点](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints)或 Map…方法( [MapRazorPages](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.razorpagesendpointroutebuilderextensions.maprazorpages) 、 [MapControllers](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.builder.controllerendpointroutebuilderextensions.mapcontrollers) 等)之间添加这些。).

这就是所有的管道，我们准备实施。

## 简单登录页面

我们只需要一个简单的表单，只有一个字段，有一个登录页面:

```
<h1>Log in</h1>

<form method="post" class="container-fluid">
    <div class="row">
        <div class="col-sm-12 col-md-4 col-lg-3 col-xl-2">
            <label for="email" class="form-label">Your e-mail address:</label>
        </div>
        <div class="col-sm-12 col-md-8 col-lg-9 col-xl-10">
            <input type="text" name="email" id="email" placeholder="my.name@email.com" class="form-control" />
        </div>
        <div class="row">
            <div class="col-12 align-content-end">
                <input type="submit" class="btn btn-primary" value="Log in"/>
            </div>
        </div>
    </div>
</form>
```

请注意，我使用的是带有响应式布局的引导类，这是一个模板 ASP.NET 应用程序所使用的。显然，您可以根据自己的需要随意修改。

该表单只发布一个字段，即用于登录的电子邮件地址。我使用一个 Razor 页面来托管它，并使用一个幕后模型来捕捉电子邮件的价值:

```
public class LoginModel : PageModel
{
    private readonly LoginValidationService loginService;

    public LoginModel(LoginValidationService loginService)
    {
        Requires.NotNull(out this.loginService, loginService);
    }

    public void OnGet()
    {
    }

    public async Task<IActionResult> OnPost(
        PostModel postModel,
        CancellationToken cancellationToken = default)
    {
        if (!this.ModelState.IsValid)
        {
            return this.Page();
        }

        await this.loginService.CommitLogin(postModel.Email, cancellationToken);

        return this.RedirectToPage("LoginMessageSent");
    }

    public class PostModel
    {
        [BindProperty(Name = "email")]
        [MaxLength(255)]
        [Required]
        public string Email { get; set; }
    }
}
```

让我们仔细分析一下。

首先，我们有一个 LoginValidationService 实例(我们稍后将创建这个实例，所以现在就让它保持原样)，它是通过构造函数注入和验证的。

*注:需要。NotNull 是* [*IX 的一个类似契约的验证和初始化方法部分。标准扩展*](https://www.nuget.org/packages/IX.StandardExtensions/) *获取包。*

我们没有理由为 GET 方法做任何特殊的事情，因为神奇的事情发生在页面上的表单通过 POST 方法回发的时候。

当处理 POST 方法时，我们得到一个只包含 email 字段的模型(如 PostModel 类中所定义的),如果模型有效，就调用带有电子邮件地址的 CommitLogin 方法，然后将用户重定向到某个页面，让他查看自己的电子邮件。

在这个阶段，我们只对获取用户的电子邮件感兴趣，这样我们就可以生成一些秘密代码和一个链接发送给用户。这只是第一步，用户对所述链接的访问将触发登录过程的第二步。

## 登录和验证控制器

在我的例子中，仅仅为了多样性，我选择将登录验证链接处理程序实现为一个无视图的 MVC 控制器。一个 Razor 页面也能很好地工作，所以你可以随意修改你认为合适的页面，但是我只是觉得这需要更多的工作。

我的代码如下所示:

```
[Route("Login")]
public class LoginController : Controller
{
    private readonly LoginValidationService lvs;

    public LoginController(LoginValidationService lvs)
    {
        Requires.NotNull(out this.lvs, lvs);
    }

    [HttpGet("Validate")]
    public async Task<IActionResult> ValidateLogin([FromQuery] string email, [FromQuery] string code)
    {
        var result = await this.lvs.ValidateLogin(
            email,
            code);

        if (result)
        {
            return this.Redirect("~/");
        }

        return this.RedirectToPage("/Forms/Authentication/LoginFailed");
    }
}
```

我们注入了相同的 LoginValidationService，并且我们有 ValidateLogin 操作方法，该方法将使用用户接收到的查询字符串参数调用其 ValodateLogin 方法:代码以及电子邮件地址。我们也处理 GET 方法，因为没有一个有自尊的电子邮件客户端会在没有首先提醒用户的情况下为你做一个适当和安全的帖子，我们不希望不必要的复杂化。

收到验证结果(在本例中是一个布尔值)后，控制器将用户重定向到一个页面，告诉他登录失败，或者重定向到主页。随意适应自己的需求。

仅此而已。现在，让我们转到 LoginValidationService 类。

## 登录验证服务

现在真正的奇迹发生了。但是，在此之前，我们需要定义一个具有大量字段的类:

```
public class LoginValidationService
{
    private const string LoginValidationKey = "LOGINVALIDATION:{0}";

    private readonly IConfiguration config;
    private readonly IWebHostEnvironment host;
    private readonly IHttpContextAccessor httpContextAccessor;
    private readonly IAppCache cache;
    private readonly UserManager<IdentityUser<int>> userManager;
    private readonly SignInManager<IdentityUser<int>> signInManager;
    private readonly ILogger<LoginValidationService> logger;

    public LoginValidationService(
        IConfiguration config,
        IWebHostEnvironment host,
        IHttpContextAccessor httpContextAccessor,
        IAppCache cache,
        UserManager<IdentityUser<int>> userManager,
        SignInManager<IdentityUser<int>> signInManager,
        ILogger<LoginValidationService> logger)
    {
        Requires.NotNull(out this.config, config);
        Requires.NotNull(out this.host, host);
        Requires.NotNull(out this.httpContextAccessor, httpContextAccessor);
        Requires.NotNull(out this.cache, cache);
        Requires.NotNull(out this.userManager, userManager);
        Requires.NotNull(out this.signInManager, signInManager);
        Requires.NotNull(out this.logger, logger);
    }[... rest of class ...]
}
```

让我们依次把他们都带走。

我们可能需要某种配置访问，所以我们注入 IConfiguration 接口作为访问它的最简单的方法。更多关于 ASP.NET 配置的[文档在微软文档。](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/)

我们希望获得一些关于当前主机的信息，因为您可能不希望从开发人员的机器上发送电子邮件(或发送太多电子邮件)。稍后您将看到这是如何发生的。

我们需要访问 HTTP 上下文，我们通过注入 IHttpContextAccessor 来访问它。更多关于注入和使用 ASP.NET HttpContext 的[文档在微软文档。](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/http-context)

然后我们变了一个相当有趣的戏法。具体如何执行取决于您的实际需求，因此您可能需要调整这一部分以适应您的业务需求。但是对于一个非常简单的应用程序来说，这是可行的:

我们需要生成一个代码，用户可以使用 10 分钟左右，之后就消失了。我们不一定需要存储它，只要在它还没有过期的时候，为应用程序提供一个简单的方法来访问它。我们不期望过多的这种代码需要同时有效。这听起来完全像缓存对象的行为，因此，自然地，缓存(在我们的特定缓存中是内存缓存)将完成这一任务。我用的是[懒人缓存](https://www.nuget.org/packages/LazyCache/)，注射 IAppCache。

现在需要注意的一件重要事情是常量 LoginValidationKey。它将被用来与我们的 IAppCache 实例一起工作。

然后我们注入 UserManager，请注意它有上面提到的 user 类作为类型参数。我们将用它来检查用户以前是否登录过，如果没有，就为它创建一个数据库条目。

之后，我们将使用 SignInManager 来指示 ASP.NET 实际进行登录，最后，如果需要，ILogger 将为我们提供记录日志的机会。

让我们看看这是如何工作的。

## 第一阶段登录

让我们分析登录第一阶段的代码:

```
public async ValueTask CommitLogin(string emailAddress, CancellationToken cancellationToken = default)
        {
            // Create login key
            string key = string.Format(
                LoginValidationKey,
                Requires.NotNullOrWhiteSpace(emailAddress));

            // Validate that there hasn't already been a login
            if (this.cache.TryGetValue<string>(
                    key,
                    out _))
            {
                return;
            }

            // Generate code
            string loginCode = DataGenerator.RandomAlphanumericString(50);

            // Save code to cache
            this.cache.Add(key, loginCode, new DateTimeOffset(DateTime.Now.AddMinutes(10)));

            // Send e-mail message
            if (this.host.IsDevelopment())
            {
                this.logger.LogDebug("Message sending on login was triggered, but will not run on a development environment.");

                return;
            }

            try
            {
[... initialize email sending service here ...] var httpContext = this.httpContextAccessor.HttpContext ?? throw new InvalidOperationException("No HTTP context, cannot proceed.");
                var linkGenerator = httpContext.RequestServices.GetService<LinkGenerator>() ?? throw new InvalidOperationException("No link generator, cannot proceed.");
                var website = linkGenerator.GetUriByName(httpContext, "default", new RouteValueDictionary());
                var loginValidateAddress = linkGenerator.GetUriByAction(
                    httpContext,
                    "ValidateLogin",
                    "Login");
                var loginPageAddress = linkGenerator.GetUriByPage(httpContext, "/Forms/Authentication/Login");

                var subject = $"Login request for {emailAddress}";
                var plainTextContent = string.Format(
                    @"Thank you for your interest in the {2} site.

Please login using the following link (valid for 10 minutes):

{3}?code={0}&email={1}

This link should validate your session, and should redirect you towards the home page.

If you cannot use this link, this means it might have expired, so please try to log in again.",
                    loginCode,
                    emailAddress,
                    website,
                    loginValidateAddress);
                var htmlContent = string.Format(
                    @"<h1>Login</h1>
<p>Thank you for your interest in the <a href=""{2}"">{2}</a> site.<p>
<p>Please login using the following link (valid for <strong>10 minutes</strong>):</p>
<p><a href=""{3}?code={0}&email={1}"">{3}?code={0}&email={1}</a><p>
<p>...or copy and paste it into a browser's navigation bar.</p>
<p>This link should validate your session, and should redirect you towards the home page.</p>
<p>If you cannot use this link, this means it might have expired, so please try to <a href=""{4}"">log in again</a>.</p>",
                    loginCode,
                    emailAddress,
                    website,
                    loginValidateAddress,
                    loginPageAddress);

[... send the email with the content ...]
            }
            catch (Exception e)
            {
                this.logger.LogError(e, "Could not send the login email due to an exception.");

                return;
            }
        }
```

很长，但是很简单，不是吗？

首先，从电子邮件地址创建一个适当的缓存键，检查是否已经有效，如果无效，则创建代码(*注意:DataGenerator。RandomAlphanumericString 是* [*IX 的一部分。StandardExtensions*](https://www.nuget.org/packages/IX.StandardExtensions/) *包*。我们将使用已经存在的键将代码保存在缓存中。我们检查我们是否在一个开发环境中(比如一台开发机器，为了不处理电子邮件，为了避免发送垃圾邮件给 SMTP 服务器)，如果不是，我们可以继续创建一些链接。

请注意我们是如何使用 HTTP 上下文的。我们从它那里得到一个 LinkGenerator 实例，并使用它来生成链接。为什么我们需要将它与 HttpContext 联系起来？

答案在于 ASP.NET(以及一般的 web 应用程序框架)如何生成链接。例如，如果我使用 www.myawesomeapp.com 访问应用程序，那么这就是我们需要创建相关链接的地方。然而，如果我们使用的是一个临时服务器，比如 staging.myawesomeapp.com，那么我们需要创建相对于它的链接，否则它们就不能工作。我们还需要思考:我们使用的是 HTTP 还是 HTTPS(尽管这很快就会变成一个无关紧要的问题)？诸如此类。我们可以在配置文件中硬编码一些地址，但是这意味着要维护那些链接，这只是一个无用的努力，因为我们有这个工作解决方案。

接下来是实际的电子邮件发送，代码示例中展示了内容(包括链接)。

第二阶段登录

第二阶段的代码稍微复杂一些:

```
public async ValueTask<bool> ValidateLogin(
            string emailAddress,
            string code,
            CancellationToken cancellationToken = default)
        {
            // Create login key and code
            string key = string.Format(
                LoginValidationKey,
                Requires.NotNullOrWhiteSpace(emailAddress));

            var innerCode = Requires.NotNullOrWhiteSpace(code);

            // Validate that there was a login that hasn't expired
            if (!this.cache.TryGetValue<string>(
                    key,
                    out var existingCode))
            {
                return false;
            }

            // Compare code
            if (!existingCode.OrdinalEquals(innerCode))
            {
                return false;
            }

            // Do login
            try
            {
                var existingUser = await this.userManager.FindByNameAsync(emailAddress);

                if (existingUser == null)
                {
                    // If there is no current user, let's create one
                    existingUser = new IdentityUser<int>(emailAddress);

                    var result = await this.userManager.CreateAsync(existingUser);

                    if (!result.Succeeded)
                    {
                        return false;
                    }

                    // Send an e-mail to the new user, if we're not in development mode
                    if (!this.host.IsDevelopment())
                    {
[... send user an email with user creation ...]
                    }
                }

                await this.signInManager.SignInAsync(
                    existingUser,
                    false);

                // Done
                return true;
            }
            catch (Exception e)
            {
                this.logger.LogError(e, "Could not perform login due to an exception.");

                return false;
            }
        }
```

主要的区别是，我们实际上希望我们的缓存现在包含一个条目，我们尽职尽责地验证它。

在这一点上，只有一件事需要提及:默认的身份实现确实期望用户被持久化以便正常工作，所以这就是 userManager 所做的——试图找到一个现有的条目，如果没有找到，则试图创建一个。它唯一需要设置的是用户名，也就是电子邮件地址。

最后，同样重要的是，signInManager 调用 SignInAsync 并指示 ASP.NET 登录用户。从那时起，就创建了一个用户会话，您可以使用该身份做所有基于会话的事情:

```
@if (User.Identity?.IsAuthenticated ?? false)
{
    <a class="nav-link text-dark">Welcome, @User.Identity.Name! @Html.GravatarImage(User.Identity.Name, size: 25)</a>
}
else
{
    <a class="nav-link text-dark" asp-page="/Forms/Authentication/Login">Log in</a>
}
```

(*注:Html。GravatarImage 可以从 Andrew Freemantle 的 GitHub 存储库* *中使用，它可以做你所期望的事情*

我不会详细介绍注销页面，因为它唯一需要做的就是调用 [SignInManager。signot async](https://docs.microsoft.com/en-us/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync)方法。

# 结论

正如 [Medium 清楚地展示了自己](https://blog.medium.com/signing-in-to-medium-by-email-aacc21134fcd)，取消密码和不麻烦第三方社交媒体是可能的。

我希望我这个相当短的实现示例是一个确凿的证据，证明它也可以被开发人员使用。

请记住:每个人都对安全负责，作为安全应用程序的开发人员，您的首要职责是给攻击者尽可能少的攻击面，尽可能少的成功机会。有些事情总是不在您的掌控之中，但是，在不在您掌控之中的时候，您有责任确保安全性几乎不可能受到损害。