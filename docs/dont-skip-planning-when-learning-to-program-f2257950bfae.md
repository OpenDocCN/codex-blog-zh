# 学习编程时不要跳过规划

> 原文：<https://medium.com/codex/dont-skip-planning-when-learning-to-program-f2257950bfae?source=collection_archive---------22----------------------->

![](img/8284d0b115e0c4bd42e54d6c43f0ad6c.png)

在 [Unsplash](https://unsplash.com/s/photos/planning?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上由[digity Marketing](https://unsplash.com/@diggitymarketing?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)拍摄的照片

> 计算机不像工厂里的机器，在工厂里生产率取决于你频繁地按下按钮。如果你放松，花时间思考和计划，计算机会更有效率。

在教授数学和计算多年后，我在[之前的博客](https://grimleyblogs.blogspot.com/2013/03/student-programmers-dont-skip-beginning.html)中给出了这条建议。在反思中，许多学习编程的课程似乎仍然忽视了规划过程。在学习编程的道路上，规划是一项必须学习的技能。然而；就像展示一个人在做一个代数题，不知何故仍然是“不酷的”，我们仍然不愿意做看起来“无用的工作”。

开始编程时的部分问题是，开始一个问题通常是相当令人生畏的。知道如何计划是解决问题的关键部分。

因此，在努力计划的过程中，下一个问题是如何计划？

# 分析

所有的项目都不一样，但都是从分解问题开始的。
事情要注意，并询问一下:

谁为这个项目买单。他们试图解决的问题是什么？对于你的许多第一个项目来说，答案可能是你自己，问题可能是想知道如何编程。这很好，但有时有一个工作环境会让事情变得更容易。我经常以我的简历作为起点。它很少供招聘人员使用，但它有助于有所作为。

谁将使用该项目？用户是为项目付款的同一个人吗？是否有多个用户？大多数重要的问题可能有不止一个用户。想想一个简单的商店应用程序，可能会有不同的屏幕供顾客查看产品、顾客购物、仓库操作员挑选和包装产品、零售商分析购物者习惯。每个用户想从系统中得到什么信息？

项目需要处理哪些信息。在这种情况下，通常谨慎的做法是从**输出**开始，然后返回到**输入**。例如，仓库提货人需要知道要提货的产品及其位置。追溯这两段数据——系统从哪里获得信息？在学校里，我们经常在这里开始谈论变量。这个太早了，认清大局就好。

> 仓库提货人想要知道要包装的产品，客户在输入他们的购买信息时需要输入产品。

# 设计界面

这是开始规划对话如何进行的时候了。根据界面的性质设计对话。无论你是在使用网页、控制台应用程序还是后台工作人员，对话仍在进行。如果你的手指此刻渴望抚弄键盘，你可以使用[美人鱼](https://mermaid-js.github.io/mermaid-live-editor/edit#eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG4gICAgQWxpY2UtPj4rSm9objogSGVsbG8gSm9obiwgaG93IGFyZSB5b3U_XG4gICAgQWxpY2UtPj4rSm9objogSm9obiwgY2FuIHlvdSBoZWFyIG1lP1xuICAgIEpvaG4tLT4-LUFsaWNlOiBIaSBBbGljZSwgSSBjYW4gaGVhciB5b3UhXG4gICAgSm9obi0tPj4tQWxpY2U6IEkgZmVlbCBncmVhdCFcbiAgICAgICAgICAgICIsIm1lcm1haWQiOiJ7XG4gIFwidGhlbWVcIjogXCJkZWZhdWx0XCJcbn0iLCJ1cGRhdGVFZGl0b3IiOmZhbHNlLCJhdXRvU3luYyI6dHJ1ZSwidXBkYXRlRGlhZ3JhbSI6ZmFsc2V9)来组装一组好的图表。

谈话结束后，是时候做两件事之一了。要么设计数据，要么设计用户看到的内容。我总是建议先设计界面。从纸开始，在使用你可以用来润色的工具之前。纸的优点是它的触感。先勾画出大致的轮廓，然后再填充细节。一旦你有了不错的初稿，就换成数码的。

设计数据，好了回到[美人鱼](https://mermaid-js.github.io/mermaid-live-editor/edit#eyJjb2RlIjoic2VxdWVuY2VEaWFncmFtXG4gICAgQWxpY2UtPj4rSm9objogSGVsbG8gSm9obiwgaG93IGFyZSB5b3U_XG4gICAgQWxpY2UtPj4rSm9objogSm9obiwgY2FuIHlvdSBoZWFyIG1lP1xuICAgIEpvaG4tLT4-LUFsaWNlOiBIaSBBbGljZSwgSSBjYW4gaGVhciB5b3UhXG4gICAgSm9obi0tPj4tQWxpY2U6IEkgZmVlbCBncmVhdCFcbiAgICAgICAgICAgICIsIm1lcm1haWQiOiJ7XG4gIFwidGhlbWVcIjogXCJkZWZhdWx0XCJcbn0iLCJ1cGRhdGVFZGl0b3IiOmZhbHNlLCJhdXRvU3luYyI6dHJ1ZSwidXBkYXRlRGlhZ3JhbSI6ZmFsc2V9)这里。键盘摸起来很舒服。在用类图填充细节之前，先从 ER 图的大致轮廓开始。

如果你是学生，那么下一步就是展示你在思考。出于某种原因，软件规划让这里的学生去做伪代码。这是一个让规划背上恶名的规划步骤。商业上，前面的步骤是由 BAs 产品经理团队为开发人员完成的。伪代码很好地证明了你知道你在想什么。但是一旦你对任何语言足够熟悉，它就不再是一个不必要的障碍。

我建议这里更好的计划步骤是进入测试驱动开发(TDD)周期。从类图中的某个地方开始。对它的一部分做一个失败的测试。创建足够的代码来通过所有的测试。整理代码，不要破坏它，然后重复。当学习代码时，浪费时间离开计算机做伪代码是令人沮丧的，错误的。也许用记录代码来代替伪代码？