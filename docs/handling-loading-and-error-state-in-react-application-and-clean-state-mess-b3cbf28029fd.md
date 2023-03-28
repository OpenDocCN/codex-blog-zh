# React 应用程序中处理加载和错误状态的干净方法。

> 原文：<https://medium.com/codex/handling-loading-and-error-state-in-react-application-and-clean-state-mess-b3cbf28029fd?source=collection_archive---------1----------------------->

![](img/6eaacdcf947a1fc5a895b0e12e8cbe33.png)

由 [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral) 拍摄

面向用户的应用程序处理不同状态的数据。有许多方法可以根据应用程序的状态来显示用户界面。这里我们将举一个主要基于 HTTP 请求的应用程序的例子。

大部分 react 前端应用主要是做 HTTP 请求，并向用户展示响应。为了更好的 UX，我们的用户界面需要向用户传达整个过程。

我们把它写在产品需求声明里吧。获取用户列表并显示用户。同时，正在获取数据，显示加载程序/消息。当请求完成时，显示错误响应。

```
*export* *default* function DisplayUsers() {
// Keeping code short with one API responseconst [state, setState] = useState({loading: true,response: null,error: null});useEffect(()=>{*// make http request*fetch('https://jsonplaceholder.typicode.com/users').then<IUser[]>(res=>res.json()).then(response=>setState({loading: false,response,error:null}))
.catch(error=>setState({
loading: false,
response: null,error}));},[]);*return* <div>{state.error ? 'Error' : null}{state.loading? <div>Loading...</div>: <div>Display users:<div>we have total users : {state.response.length}{state.response.map(user=><User user={user}/>)}</div></div>}</div>}
```

随着代码的增长或其他情况的出现，例如，如果用户列表为空，则显示自定义消息。它变成在`JSX`模板中导航。

第一步是清理这些混乱，分离出`state blocks.` Having，这将使浏览状态块变得容易，我们将能够讨论代码。

```
*export* *default* function DisplayUsers() {const [state, setState] = useState({
loading: true,
response: null,
error: null
});useEffect(()=>{*// make http request*fetch('https://jsonplaceholder.typicode.com/users').then<IUser[]>(res=>res.json()).then(response=>setState({loading: false,response,error:null})).catch(error=>setState({loading: false,response: null,error}));},[]);*if*(state.error){
// This is Error block. Do everything related to error hangling.*return* <div style={{color:'red'}}> Error state/ every thing related to Error</div>}*if*(state.loading){
// Loading state block. Do everything related to loading show loader/ animation. *return* <div>Loading...</div>}// Better and clean code with less ternary conditions.*return* <div>
<h3>Display users:</h3>
we have total users : {state.response.length}{state.response.map(user=><User user={user}/>)}
</div>}
```

有了这些状态块，我们对相关的状态有了更多的控制。现在我们有了更好的积木。

这是针对单个组件完成的，但是对于一个大的应用程序，会有更多基于状态的组件。

会有很多重复。这个问题可以用这个通用组件来解决并简化。

// StateHandlerComponent.js 来解决这种重复。

```
//This component will deal with state blocks 
const StateHandler = (props) =>{const {
showLoader = false,
showError = false,
showEmpty = false,
retryHandler = () => { console.log("Retry!!") } } = props;*if* (showLoader) {
*return* <Loader fullScreen={false} />;
}*if* (showError) {
*return* <InfoComponent  icon={<ErrorIcon />} buttonClickHandler={retryHandler} />
}*if* (showEmpty) {
*return* <div class={style.exceptionContainer}>
<Empty message='No data available' />
</div>;
}*return* props.children;
}*export* *default* StateHandler;
```

使用此`StateHandler`组件的最终输出。

```
*export* *default* function DisplayUsers() {const [state, setState] = useState({
loading: true,
response: null,
error: null
});useEffect(()=>{*// make http request*fetch('https://jsonplaceholder.typicode.com/users').then<IUser[]>(res=>res.json())
.then(response=>setState({
loading: false,
response,
error:null
})).catch(error=>setState({
loading: false,
response: null,
error
}));},[]);//This component deals will only the user list display *return* <StateHandler showError={state.error} showLoader={state.loading} showEmpty={state.response.length == 0}><div><h3>Display users:</h3>
we have total users : {state.response.length}
{state.response.map(user => <User user={user} />)}</div></StateHandler>;}
```

> 现在，状态处理由“StateHandler”组件完成。弄得干净利落，容易讲道理。

我将很快分享更多的技巧。继续学习，让我知道这个组件是否帮助你清理了代码库中的一些混乱状态:)