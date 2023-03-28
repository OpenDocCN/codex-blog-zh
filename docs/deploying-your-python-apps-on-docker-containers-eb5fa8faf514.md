# 在 Docker 容器上部署 Python 应用程序

> 原文：<https://medium.com/codex/deploying-your-python-apps-on-docker-containers-eb5fa8faf514?source=collection_archive---------2----------------------->

## 如何构建 Docker 容器来托管 Python 应用程序

在我的上一篇[文章](/codex/automate-repetitive-tasks-with-flask-and-python-77dd4a96e07d)中，我已经向您展示了如何构建简单的 Python 应用程序，该应用程序可以通过使用 Flask 这样的轻量级框架来自动化重复的任务。该应用程序由非常简单的 UI 界面组成，用户可以在其中键入用户的电子邮件并触发在后端服务器上执行的脚本。所有这些都是通过几个 API 调用完成的，这些 API 调用提供了 UI 和后端服务器之间的映射。如果你感兴趣，我…