# 用 Angular-Material 编写一个简单的“忙碌”微调指令

> 原文：<https://medium.com/codex/writing-a-simple-busy-spinner-directive-in-angular-material-5b1462b525f8?source=collection_archive---------3----------------------->

![](img/87ccc284e046afbb31aa0a960cb90f8c.png)

作为异步服务调用领域的前端开发人员，向用户提供反馈是非常重要的。然而，通过大量的`*ngIf`和基于某种布尔标志的切换来逐个案例地做这件事是很痛苦的。让我们写一个[结构指令](https://angular.io/guide/structural-directives)来用尽可能少的工作为我们处理这个问题。

我们将要写的结构指令将接受一个布尔表达式，并交换它所附加的元素的内容。当表达式为真时，它将换入一个`MatProgressSpinner`组件，当表达式为假时，我们将显示用户在组件中编码的内容。

这里有一个演示:

[](https://stackblitz.com/edit/app-spin-on?file=src/app/app.component.ts) [## *appSpinOn - StackBlitz

### *appSpinOn 指令的示例。

stackblitz.com](https://stackblitz.com/edit/app-spin-on?file=src/app/app.component.ts) 

# 保持干燥

这一点的重要性不仅仅在于找到一种为用户提供可视反馈的方法，还在于以一种一致的方式做到这一点，这种方式符合 DRY 原则，我们不会到处使用复制/粘贴来重复代码。

> 干:不要重复自己

一种不遵循 DRY 实现等效的方法很简单:

```
<div *ngIf="isLoading"><mat-spinner [diameter]="18"></mat-spinner></div>
<div *ngIf="!isLoading">
  <entity-details></entity-details>
</div>
```

然而，为了重用它，你必须复制/粘贴旋转器代码。即使把它简化成它自己的组件，我们也只会稍微减少对两个对立的`*ngIf`语句的复制/粘贴。然而，如果我们在一个结构化的指令中这样做，我们可以使用编写一个单独的`*ngIf`语句来实现相同的目标。

# 那么，它是如何工作的？

您可以从上面的`spin-on-directive.ts`中的 StackBlitz 获得完整的源代码，在`styles.scss`中有一个小的样式花絮，但是指令代码的重要部分看起来像这样:

```
@Input() set appSpinOn(condition: boolean) {
    if (!!condition !== this.#isSpinning) {
      this.#spinner = null;
      this.viewContainer.clear();
      this.#isSpinning = condition;
      if (!condition) {
        // Render the template
        this.viewContainer.createEmbeddedView(this.templateRef);
      } else if (condition) {
        this.addSpinner();
      }
    }
  }private addSpinner() {
    const componentFactory = this.componentFactoryResolver.resolveComponentFactory(MatProgressSpinner);
    const { instance } = this.viewContainer.createComponent<MatProgressSpinner>(componentFactory);
    instance.diameter = this.#diameter;
    instance.color = this.#color;
    instance.mode = 'indeterminate';
    instance._elementRef.nativeElement.classList.add('spin-on-instance');
    this.#spinner = instance;
  }
}
```

`appSpinOn`输入值的设置器将表达式计算为布尔值。如果表达式之前已经计算了相同的结果，它不会做任何事情，所以它只保留 dom 中的内容。如果它还没有求值，或者值不同，它将决定条件是真还是假。这里我们使用`this.viewContainer.createEmbeddedView(this.templateRef);`来写如果条件是`false`的话，指令附加到的 templateRef。

然而，如果条件是`true`的话，事情就变得有点复杂了。我们使用[动态组件创建](https://angular.io/guide/dynamic-component-loader)来创建`ViewContainerRef`中的`MatProgressSpinner`，Angular 将它注入到指令中，然后配置它进行样式化，然后

我们还需要添加一些样式，否则我们将无法使用它来替代图标之类的东西。我在 root styles.scss 文件中放置了以下内容，它允许适当的对齐和边距来代替图标:

```
.mat-progress-spinner.spin-on-instance {
  vertical-align: middle;
  display: inline-block;
  margin: 3px;
}
```

从那以后，使用它就像添加任何使用它的模块的导入并在代码中使用它一样简单。

使用它的一种方法是细分内容。例如:

```
<div>
  <entity-details ***appSpinOn="isLoading"**></entity-details>
</div>
```

缺省值对于替换图标很有用。例如，该演示在“保存”按钮上使用了以下内容:

```
<button mat-raised-button [disabled]="isSaving || isLoading" (click)="save()">
  <mat-icon ***appSpinOn="isSaving"**>save</mat-icon>  Save
</button>
```

![](img/9db9e4250643c6ed631e59ba6783db0a.png)![](img/e083fc8bcbc747abd3acf6bba29c274b.png)

保存之前，图像有一个“保存”图标。该指令仅用微调器替换图标。

我们还可以使用变量`color`来改变指令使用的大小:`*appSpinOn="isLoading; **color: 'primary': diameter: 40**"`，该变量接受一个角度材质`ThemePalette`字符串和一个数字`diameter`。

我们在这里所做的是一个有用的指示，当一些事情在幕后发生时，它给用户反馈。它减少了我们必须编写的代码量，而且灵活、可配置。