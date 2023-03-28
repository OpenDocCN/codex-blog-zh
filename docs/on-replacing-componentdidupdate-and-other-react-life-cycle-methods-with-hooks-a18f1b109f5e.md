# 用钩子替换 componentDidUpdate 和其他 React 生命周期方法

> 原文：<https://medium.com/codex/on-replacing-componentdidupdate-and-other-react-life-cycle-methods-with-hooks-a18f1b109f5e?source=collection_archive---------7----------------------->

[我以前在这个博客上讨论过 React 的生命周期](https://joshgoestoflatiron.medium.com/april-2-intervals-and-event-listeners-when-working-with-react-hooks-14bc069affe0),因为它是许多开发人员急性神经崩溃的常见原因…

![](img/30ae2e8172fcce90b996f3a343d3dc35.png)

对于一名 React 工程师来说，对组件生命周期的深刻理解就像你是一名篮球运动员多长了 15 英寸一样。当**将基于类的组件重构为功能组件时，这尤其有帮助——这是我在 2021 年作为一名 React 工程师几乎整天都在做的事情。大部分只是调整语法的问题，但是**像** `**componentDidUpdate**` **或者** `**componentWillUnmount**` **这样的生命周期方法呢？****

让我们从我的工作中举一个真实的例子:一个`class Calculator`，当我完成它时，它将是`function Calculator()`。它有很多活动部件:

```
import { connect } from 'react-redux';
import { fetchPerformance } from '...redux actions...';class Calculator extends Component { state = {
    isModalOpen: false,
    selectedDateRangePrices: []
    *...more delicious state here...* }; componentDidUpdate = ( previousProps, previousState ) => {
    if ( ...something about state/props changes ) {
      this.setState( {
        isModalOpen: true,
        *...more nice juicy state...* } );
    }
  }; render () {
    return (
 *...a beautiful calculator component...*    );
  }}const mapStateToProps = (state) => ( {
  start_date: state.closedEnds.start_date,
  end_date: state.closedEnds.end_date,
  starting_price: state.closedEnds.starting_price,
  ending_price: state.closedEnds.ending_price,
  ttl_return: state.closedEnds.ttl_return,
  avg_annual: state.closedEnds.avg_annual,
  time_calculated: state.closedEnds.time_calculated
} );export default connect( mapStateToProps, { fetchPerformance } )( Calculator );
```

在`render()` we `return` `input`字段中，`button` s，以及一个显示一些有用信息的模态，根据状态中设置的布尔标志隐藏或显示。我们还必须处理来自 Redux 商店的一些数据和我们本地的`state`。

![](img/1b7e89c09aa10a4d34ebd984d42df5bd.png)

只有当`props`和`state`的某些属性改变时，我才需要将那个`isModalOpen`标志切换到`true`；因此，在基于类的 React 中，我自然使用`previousProps`和`previousState`在`componentDidUpdate`中编写逻辑。但是……将它重构为基于钩子的 React 并不是简单的逐字符翻译。

[React 的文档在这里并不是很有帮助](https://reactjs.org/docs/hooks-effect.html):他们告诉我们**“可以把** `**useEffect**` **想成** `**componentDidMount**` **、** `**componentDidUpdate**` **和** `**componentWillUnmount**` **的组合……”**但是`useEffect`并没有像`previousProps`一样给我们任何有帮助或者熟悉的东西。*那么该怎么办呢？*

下面是 React 文档*应该*解释的内容:本质上，**在一个组件的生命周期中，在挂载/更新/卸载/s 之间发生的任何事情，都可以被描述为该组件的“副作用”或者仅仅是“影响”**(因此得名)。基于类的 React 用`Component`方法和引用参数来处理这些副作用——有点笨拙，不太具有声明性。Functional React 在一个钩子`useEffect()`中处理所有副作用，在另一个钩子`useRef()`中处理所有引用。更优雅更有反应力。

回到我的`class Calculator`，假设每当`selectedDateRangePrices`在我的组件的本地`state`中发生变化时，我想要打开模态:

```
// The modal opens if state.selectedDateRangePrices has changedcomponentDidUpdate = ( prevProps, prevState ) => {
  if ( this.state.selectedDateRangePrices !== prevState.selectedDateRangePrices ) {
    this.setState( { isModalOpen: true } );
  }
}
```

很容易将此转化为基于钩子的反应。用`useState`吊钩注意状态；然后，在回调函数体中添加一个打开模态的`useEffect`，并将`state.selectedDateRangePrices`添加到它的依赖数组中:

```
function Calculator( { *...props galore...* } ) { const [ state, setState ] = useState( { 
    isModalOpen: false,
    selectedDateRangePrices: []
    *...state, wonderful state...* }); const { start_date, end_date, starting_price, ending_price, ttl_return, avg_annual, time_calculated } = useSelector( state => state.openEnds ); useEffect( () => setState( { ...state, isModalOpen: true } ), [ state.selectedDateRangePrices ] );}
```

这就变得更棘手了:如果我只想设置状态，并在 Redux 的道具改变时打开该模态，该怎么办？更棘手的情况呢，比如只有当三个或更多的改变时才打开它？还是两个特定的都改变了？

```
// The modal only opens if both ttl_return and avg_annual have changedcomponentDidUpdate = ( prevProps, prevState ) => {
  if ( this.props.ttl_return !== prevProps.ttl_return && this.props.avg_annual !== prevProps.avg_annual ) {
    this.setState( { isModalOpen: true } );
  }
}
```

我们没有来自`componentDidUpdate`的`prevProps`引用，所以要把这样的组件翻译成钩子，我用 React 的`useRef`钩子做我自己的引用。[我不是第一个也不是唯一一个](https://javascript.plainenglish.io/july-7-triggering-file-uploads-downloads-without-a-back-end-in-react-6438cb77859)说出来的:`**useRef**` **是 React hooks 的无名英雄。**把 refs 想象成一个小小的存储盒，里面存放着任何东西——`fetch`结果、DOM 元素和其他变量——工程师(你)可以使用属性`.current`调用它们。当我们用组件副作用之外的元素或数据来微调组件的生命周期时，引用非常有用:

```
function Calculator( { *...props galore...* } ) { const [ state, setState ] = useState( { 
    isModalOpen: false,
    selectedDateRangePrices: []
    *...state, wonderful state...* }); const { start_date, end_date, starting_price, ending_price, ttl_return, avg_annual, time_calculated } = useSelector( state => state.openEnds ); const previousRef = useRef(); useEffect( () => {
    if ( previousRef.current ) {
      if ( ttl_return !== previousRef.current.ttl_return && avg_annual !== previousRef.current.avg_annual ) {
        setState( { ...state, isModalOpen: true } )
      }
    }
    return () => {
      previousRef.current = { ttl_return, avg_annual };
    };
  } );}
```

这是怎么回事？

*   我们从定义`previousRef`开始。
*   我们编写了一个`useEffect()`，当一个组件第一次挂载或者更新的时候运行。在内部，回调函数检查我们的条件是否满足——也就是说，当前的两个 Redux 属性是否等于它们在`previousRef.current`中的对应属性。我们还要检查`previousRef.current`是否是`undefined`，这样我们就不会在页面第一次加载时打开我们的模态。
*   这个`useEffect()` `return`是一个**清理功能，**定义了在组件卸载/更新之前需要运行的任何逻辑——相当于`componentWillUnmount`。这个清理函数更新了我们的`previousRef`，所以组件可以像使用`previousProps`一样挂在引用上。
*   我们定义的逻辑和清理函数意味着我们不需要依赖数组。

你也可以创建一个内部带有`useRef`的定制钩子——也许叫做`usePrevious`——但是 1)这是不必要的，2)这需要放弃很多使`useRef`如此强大的控制和特性。

在这一点上，考虑你自己的警告:`**useRef**` **不是好的 React 设计的替代品！**超出从父母到孩子的正常流程`props`是很棒的……**但是，如果超出太多**，你最终会发现一些非常古怪的错误，或者至少是*，*难以理解的代码，编辑起来是一场噩梦。每一个引用都应该有清晰的逻辑和令人信服的存在理由——缺少这两者都是在组件的副作用中找到你需要的东西，或者考虑重组你的代码。