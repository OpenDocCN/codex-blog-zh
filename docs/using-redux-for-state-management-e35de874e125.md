# 使用 Redux 进行状态管理

> 原文：<https://medium.com/codex/using-redux-for-state-management-e35de874e125?source=collection_archive---------8----------------------->

## *通过维护全局数据存储，避免不必要的道具钻探。*

![](img/6e69c129a3f957f5fba4fab1b11f5861.png)

第一次学 Redux…不喜欢。我的项目越来越大，我可以看到有一个地方存储全球数据的优势，但设置这一切感觉有点乏味和过于复杂。

在很大程度上，这是因为我使用 Redux 的唯一方式，使用`connect`和`mapStateToProps`。通过这种方法，商店中的对象被调用到一个`mapStateToProps`函数中，然后作为常规道具添加到组件中。它可以工作，但是**每次**我访问商店的时候，我都必须在组件的底部写一些像这样的东西:

```
const mapStateToProps = (state) => { return { propOne: state.propOne, propTwo: state.propTwo, propThree: state.propThree, propFour: state.propFour, propFive: state.propFive, };};export default connect(mapStateToProps)(MyComponent);
```

这似乎是不值得做的更多的工作！仅仅访问几个状态对象就会显著地延长我的代码。此外，对于其他开发人员来说，它不是最易读或最清晰的。

# 一种改进

这一切都改变了，当我了解了`useSelector()`钩。那时，我正在做一个个人项目，我把它发布在 Reddit 上征求建议。我很幸运，有人看到了我正在使用的`mapStateToProps`方法，并建议了一个替代方法。

使用`useSelector()`，您可以在一行上调用商店中的道具，就像这样:

```
const exampleProp = useSelector((state) => state.exampleProp);
```

它更清晰，也更容易被其他人理解。立刻，我回去重构了我的整个项目。

此外，除了复杂的`connect`方法，您还可以使用钩子`useDispatch()`钩子来改变状态。一旦导入，它就像这样简单地工作:

```
const dispatch = useDispatch();const handleClick = () => { dispatch(myAction());};
```

如果你一直在使用`connect`，这种简单性是显著的。

就是这样！用`useSelector()`和`useDispatch()`访问和更改 Redux 存储就是这么简单。:)