# 未能添加 CodeMirror 6(然后成功)

> 原文：<https://medium.com/codex/failing-to-add-codemirror-6-and-then-succeeding-cb4d6d2dcfa2?source=collection_archive---------9----------------------->

## 将非 React 项目引入 React 项目

在上一篇文章中，我创建了一个 React 组件，它拦截并格式化了`console.log`和`console.error`的参数。

在这篇文章中，我将从一个简单的`<textarea>`升级到一些被设计用来显示代码的东西。

# 我应该用什么？

在考虑了几个不同的选项之后，甚至考虑从头开始做一些东西，我决定 [CodeMirror](https://codemirror.net/6) 将是最适合短代码片段的。

幸运的是，CodeMirror v6 已经推出，这是一个绝佳的尝试机会。

# 创建编辑器组件

编辑器组件将驻留在`src/components/editor/index.tsx`中。现在，它将是一个空的 react 组件。

```
import React from "react";export const Editor = () => {
  return <></>;
};
```

我们还需要安装一些来自`npm`的库

```
npm i @codemirror/view @codemirror/state
```

CodeMirror 需要附加到一个 DOM 节点，所以我们将通过一个`ref`来添加它。我们还将使用一个`<section>`来添加一些语义。在 TypeScript 类型的世界中，我了解到任何不需要比基本接口提供的更多的 HTML 元素，比如`<section>`，都应该使用`HTMLElement`类型。

```
import React from "react";export const Editor = () => {
  const editorRef = React.useRef<HTMLElement>(null);
  return <section ref="editorRef"/>;
};
```

在选择 DOM 节点之前，`editorRef`将会是`null`，所以需要一个`useEffect`来附加编辑器。

```
React.useEffect(() => {
  if(editorRef.current === null) return;
  const view = new EditorView({
    state: EditorState.create({ doc: "hello" }),
    parent: editorRef.current
  });
}, [editorRef.current]);
```

# 通过状态

注意:这部分偏离了视频，因为我在那里犯的错误不是特别重要。

将使用`<Editor>`的组件将需要评估正在输入的内容。换句话说，编辑器需要接受一个回调函数，它可以调用这个函数将状态传递给父级。

我尝试使用`EditorState["doc"]`和`EditorState`，但是，成功的是返回`EditorView`。

```
type EditorProps = {
  setView => (view: EditorView) => void
}
```

我还意识到没有必要在组件内部声明`EditorState`，所以我把它移了出来。

```
import React from "react";const state = EditorState.create({ doc: "hello" });export const Editor = () => { /* ... */ };
```

在编辑器中，一旦视图准备好，就可以调用组件`setView`。卸载编辑器时销毁视图也很重要。

```
React.useEffect(() => {
  if(editorRef.current === null) return; const view = new EditorView({
    state: EditorState.create({ doc: "hello" }),
    parent: editorRef.current
  }); setView(view); return () => {
    view.destroy();
    setView(null);
  };
}, [editorRef.current]);
```

可以更新父组件以使用`<Editor>`组件，也可以更新代码评估器以使用`EditorView.state.doc`。

```
*import React from* "react";
*import* { *Editor* } *from* "../../../components/editor";
*import* { EditorView } *from* "@codemirror/view";

*const StringPage* = () => {
  *const* [view, setView] = *React*.*useState*<EditorView | *null*>(*null*);

  /* ... */ *const* evaluateCode = () => {
    *if* (view === *null*) *return*;
    *const* code = view.state.doc.toString();
    *try* {
      *new* Function(code)();
    } *catch* (e) {
      console.error(e);
    }
  };

  *return* (
    <>
      {/* irrelevant components omitted */}
      <Editor *setView*={setView} />
    </>
  );
};

*export default StringPage*;
```

# TL；速度三角形定位法(dead reckoning)

我将 CodeMirror 版本 6 放入 React 组件中。

*原籍:*

 [## 无法添加 CodeMirror 6(然后成功)| DevLog 004

### 在上一篇文章中，我创建了一个 React 组件，它截取并格式化了 console.log 和…

www.bayanbennett.com](https://www.bayanbennett.com/posts/failing-to-add-codemirror-6-and-then-succeeding-devlog-004) ![](img/51104934595d20d6676a01de1f0acc72.png)

这篇文章的缩略图