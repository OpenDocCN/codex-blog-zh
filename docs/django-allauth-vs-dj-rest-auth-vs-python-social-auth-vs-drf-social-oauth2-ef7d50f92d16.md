# django-allauth vs DJ-rest-auth vs python-social-auth vs drf-social-oauth 2

> 原文：<https://medium.com/codex/django-allauth-vs-dj-rest-auth-vs-python-social-auth-vs-drf-social-oauth2-ef7d50f92d16?source=collection_archive---------2----------------------->

为你的 Django 项目寻找合适的社会认证包。

![](img/bf195964f7d2541552c5577d7ea85ed6.png)

照片由[飞:D](https://unsplash.com/@flyd2069?utm_source=medium&utm_medium=referral) 在 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# 包裹地狱🔥

不幸的是，有太多的软件包试图解决 Django 项目中的(社会)认证任务。我根据功能、开发历史、流行程度和文档比较了四个流行的包。他们已经预选的基础上受欢迎程度和积极的发展状况。

# 包装概述

## [姜戈-阿劳斯](https://django-allauth.readthedocs.io/)

Django-allauth 是 Django 世界中最流行的认证包之一，为传统和社会认证提供了广泛的功能。它已经被开发和积极维护了超过 10 年，被用于近 100，000 个项目，并拥有大量的贡献者。它的文档内容丰富、简单易懂，而且很容易实现。然而，这个方案有一个很大的缺点。它没有提供与 django-rest-framework 一起使用的现成功能，仅限于与 django 的模板语言一起使用。因此，不包括所有以单页面应用程序或移动应用程序为前端的无头项目。总的来说，它是一个开发良好的 Django(社交)认证包，但是它缺少 REST API 功能，这是一个很大的缺点。

## [dj-rest-auth](https://dj-rest-auth.readthedocs.io/)

[DJ-rest-auth](https://dj-rest-auth.readthedocs.io/) 构建在 [django-allauth](https://django-allauth.readthedocs.io/) 之上，弥补了使用认证与 [django-rest-framework](https://www.django-rest-framework.org/) 的差距，有效地允许通过使用 REST API 端点对无头 django 应用程序进行(社交)认证。它已经开发了好几年，现在仍然在积极维护。几乎有 1000 名明星和 100 多名贡献者，它也是一个非常受欢迎的包。它的文档直截了当，易于理解，并且涵盖了所有重要的内容。总的来说，这似乎是一个用 Django 和 [django-rest-framework](https://www.django-rest-framework.org/) 实现(社交)认证的伟大包。

## [python-social-auth](https://python-social-auth.readthedocs.io/)

这个包为 python 项目提供了社会认证和授权，支持 OAuth1、OAuth2 和 OpenID 协议等。已经开发了好几年，现在还在积极维护。代码库最初源自 django-social-auth，旨在提供一个更通用的包，也支持 Flask 等其他 Python 框架。这个包被分成几个模块，而仅核心模块[就有 18000 多个用户和数百个贡献者。文档内容广泛、简单易懂。总的来说，它可以被认为是在 Python(不仅仅是 Django)项目中实现(社会)认证解决方案的安全赌注。](https://github.com/python-social-auth/social-core)

## [drf-social-oauth2](https://github.com/wagnerdelima/drf-social-oauth2)

Drf-social-oauth2 是围绕 [python-social-auth](https://python-social-auth.readthedocs.io/) 和 [django-oauth-toolkit](https://django-oauth-toolkit.readthedocs.io/) 的包装器，将它们的功能组合在一个包中。有效地让您能够轻松地使用 REST API 端点设置社交认证，以及设置 OAuth 提供者。当你除了让你的用户有可能通过一个公共的社会提供者进行认证之外，还想自己建立一个 OAuth 提供者时，它相对于 [python-social-auth](https://python-social-auth.readthedocs.io/) 的优势就发挥出来了。它似乎是 django-rest-framework-social-oauth 2 的继承者，并得到了积极的维护。乍一看，它似乎不像其他套装那样受欢迎。这可能只是因为它在 2019 年接管了[django-rest-framework-social-oath 2](https://github.com/RealmTeam/django-rest-framework-social-oauth2)。文档并不太广泛，但很简单，涵盖了所有基本案例。此外，因为它是另外两个包的包装器，所以您可以更深入地研究它们的文档。总的来说，如果您对通过 REST API 端点实现(社交)身份验证感兴趣，并且还想自己设置一个 OAuth 提供者，那么这是一个不错的包。

# 摘要

就流行度、开发状态和文档而言，这四个包似乎都做得很好。差异主要存在于不同的功能范围。哪个包适合您的项目将在很大程度上取决于您的特定需求。

如果你有一个 Django 项目，使用它的内置模板语言，并且想要使用一个开发良好并且经过严格测试的包， [django-allauth](https://django-allauth.readthedocs.io/) 可能是你的正确选择。如果你也需要通过 REST API 端点实现认证，那么 [dj-rest-auth](https://dj-rest-auth.readthedocs.io/) 或者 [python-social-auth](https://python-social-auth.readthedocs.io/) 都是不错的选择。鉴于 [dj-rest-auth](https://dj-rest-auth.readthedocs.io/) 依赖于 [django-allauth](https://django-allauth.readthedocs.io/) ， [python-social-auth](https://python-social-auth.readthedocs.io/) 作为一个独立的包运行，也可以与 django 之外的其他框架一起使用。另外，如果你想建立自己的 OAuth 提供者， [drf-social-oauth2](https://github.com/wagnerdelima/drf-social-oauth2) 可能适合你，包装 [python-social-auth](https://python-social-auth.readthedocs.io/) 和 [django-oauth-toolkit](https://django-oauth-toolkit.readthedocs.io/) 。