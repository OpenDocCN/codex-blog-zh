# Rails 和 Devise 的用户会话不活动超时

> 原文：<https://medium.com/codex/user-session-inactivity-timeout-with-rails-and-devise-7269ac3a8213?source=collection_archive---------0----------------------->

## [法典](http://medium.com/codex)

![](img/ff8e6b3a34bafc13fa45a6cba4d7a2f7.png)

[NeONBRAND](https://unsplash.com/@neonbrand) 在 [Unsplash](https://unsplash.com) 上的照片

最近，我不得不在 Ruby on Rails 应用程序上实现一个特性，我必须确保用户会话在一段时间不活动后过期。应用程序使用[device gem](https://github.com/heartcombo/devise)作为用户认证的解决方案。

# **为什么我们需要终止用户会话？**