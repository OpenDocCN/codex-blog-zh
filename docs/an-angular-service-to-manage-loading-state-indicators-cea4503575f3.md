# 管理装载状态指示器的角度服务

> 原文：<https://medium.com/codex/an-angular-service-to-manage-loading-state-indicators-cea4503575f3?source=collection_archive---------2----------------------->

## [法典](http://medium.com/codex)

## 创建可重用且有效的服务来管理对用户交互的视觉响应

如果您已经构建了一两个 angular 应用程序，那么您可能已经实现了异步服务，比如 REST API。当您的用户与您的应用程序交互并触发异步事件时，他们需要知道发生了什么。因此，您创建了加载微调器、状态消息和条件逻辑。这可能会变得重复、令人沮丧，并且不可维护。

使用服务来处理响应 UI 或任何其他发射器触发的事件的逻辑，可以节省大量重复工作，并在应用程序中提供一致性。

这项服务的目的是:

*   为启用可视元素和消息控制的事件创建状态对象
*   提供管理这种状态的功能
*   为组件之间共享状态提供可观察性

本文给出了这种服务的一个例子。它管理状态对象，这些对象为使用者提供用于向用户呈现信息的参数和方法。它包括反应式编程，以帮助在组件之间共享信息。

![](img/43d0b44bd41b5695931e6048d58a2a69.png)

基本服务让世界运转，而棱角分明的应用程序。(照片 M.Raju)

要添加完整的服务并开始使用它，请在您的项目中运行这个命令:`npm i @bit/mxds.angular-components.async-ui-feedback-module`。请继续阅读以理解它的构造，或者获得如何实现您自己版本的想法。

旁注:如果你还没有开始使用[位](http://bit.dev)，你应该开始使用了。它使你的代码立即可重用，易于维护，并可以在几分钟内设置好。[阅读本文，开始行动](https://max-a-raju.medium.com/creating-an-angular-library-to-share-components-with-bit-e1412106d059)。

实现分为 3 个关键部分:状态接口、事件类和服务。[点击这里查看服务](https://bit.dev/mxds/angular-components/async-uifeedback/~code)的完整代码。

## 状态界面

```
export interface AsyncUIState {   
    id: number;   
    loading: boolean;   
    error: boolean;   
    complete: boolean;   
    errMessage?: string;   
    successMessage?: string; 
}
```

这是组件将用来渲染微调器、文本按钮等的数据…取决于实现该接口的实例的当前值。

## 事件类别

这个类提供了控制状态的函数，提供了组件使用的参数，并包含了当前状态的可观察值。对于本文来说太大了，所以点击这里阅读 AsyncUIEvent 代码。

每次需要管理新事件时，服务都会实例化一个事件类对象。然后，该事件保存在要使用的组件中，以及服务中的数组中。

传递给事件构造的参数可以允许关闭反应性(可观察的)特性，并且可以传递一个`MatSnackBar`实例来使事件能够在完成或出错时打开小吃店。

`start(), complete(), error()`和`reset()`都可以在事件上被调用，以在状态上设置适当的标志/消息。然后，所有这些方法都通过`state$`可观察对象发出状态。

## 服务

这是一个单例服务，在 root 中提供。它创建、获取和删除事件。它还包含所有现有事件的可观察值。每当一个`event.state$`发出一个值时，这个可观测值就会发出。

delete 函数删除事件的现有订阅，应该用于避免任何性能问题。在这里查看服务的完整代码。

## 把所有的放在一起

有了接口、事件类和服务，您现在可以在您的组件中实现这个服务了。[您可以查看此组件代码，以了解示例实现](https://bit.dev/mxds/angular-components/async-uifeedback/~code#src/async-uifeedback/async-uifeedback.component.ts)。

希望这里提供的示例代码已经给了您实现自己的类似服务所需的想法。如果您有任何反馈或问题，请发表评论。

您可以通过调用`npm i @bit/mxds.angular-components.async-uifeedback`下载并实现本文中的服务，并使用该服务将以下内容添加到模块中:

```
import { AsyncUIFeedbackService } from '@bit/mxds.angular-components.async-uifeedback';import { MatSnackBarModule } from '@angular/material/snack-bar';@NgModule({
    declarations: [AsyncUIExampleComponent],
    imports: [
        CommonModule
        AsyncUIFeedbackModule,
        MatSnackBarModule
    ],
    providers: [AsyncUIFeedbackService]
})
export class ExampleModule { }
```