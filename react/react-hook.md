# React Hook 
Hook 是React 16.8版本中新增的内容，它的出现让函数组件拥有了类似class组件的能力（生命周期等），同时也优化了一些过去的缺点，例如逻辑复用复杂、多个生命周期函数多次调用等。

## `useState`
> useState 是允许你在 React 函数组件中添加 state 的 Hook。它很像class组件中的`this.setState`方法。

首先引入 React 中 useState 的 Hook，然后在函数组件的顶部声明state变量 和 更新state变量的方法

useState 接收一个initialValue，它是一个state变量的初始值， 其类型可以是一个对象、数字、字符串或者其他都可以。

一般来说，在函数退出后变量就会”消失”，而 state 中的变量会被 React 保留， 有点闭包的感觉喔。

```javascript

import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量
  const [count, setCount] = useState(0);
}

```

### useState 方法的返回值

当前 state 以及更新 state 的函数，你需要成对的获取它们。所以我们可以使用数组解构的方式成对获取，这样更加的方便和具有语义化。
它并不难懂，就像这样：

```javascript
var fruitStateVariable = useState('banana'); // 返回一个有两个元素的数组
var fruit = fruitStateVariable[0]; // 数组里的第一个值
var setFruit = fruitStateVariable[1]; // 数组里的第二个值
```

### 读取State

在函数中，我们可以直接用 count:

```javascript
  <p>You clicked {count} times</p>
```

### 更新State

```javascript {highlight: [1]}
<button onClick={() => setCount(count + 1)}>
  Click me
</button>
```

### 注意

**不像 class 中的 this.setState，更新 state 变量总是<span style="color: red">替换它</span>而不是合并它。**




## `useEffect`

> Effect Hook 可以让你在函数组件中执行副作用操作: 数据获取，设置订阅以及手动更改 React 组件中的 DOM 
> 你可以把 useEffect Hook 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合, 所以在有些时候，使用函数组件会比class组件更加方便。

但与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect **不会阻塞浏览器更新屏幕**，这让你的应用看起来响应更快。

```javascript {highlight: [1,'6-10']}
import React, { useState, useEffect } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  // Similar to componentDidMount and componentDidUpdate:
  useEffect(() => {
    // Update the document title using the browser API
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### 使用

`useEffect`像`useState`一样，可以多次使用

`useEffect`接收2个参数：

- 第一个参数是一个函数：`fn`, 如果你的 effect 返回一个函数，React 将会在执行清除操作时调用它（clean up function）,相当于React会自动的在`componentWillUnmount`帮我们执行它

- 第二个参数是一个数组：`[var, ...]`

  - 可以省略，如果省略的话那么useEffect 会在第一次渲染之后和每次更新之后都会执行。
  - 或是一个空数组：只会在第一次渲染(`mount`)之后时执行一次，从此后不再执行，这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。
  - 当然很多情况下是一个有值的数组`[props, data, count,...]`，数组中的值代表副作用依赖的一些变量，如果这些值中有任意一个发生变化，那么useEffect会重新执行

有清除副作用的`useEffect`
```javascript {highlight: ['6-16']}
import React, { useState, useEffect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    // 指定清理函数，当然并不是必须为 effect 中返回的函数命名。
    return function cleanup() {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'Loading...';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

### 通过跳过 Effect 进行性能优化，在组件卸载时有条件的执行清理函数
在某些情况下，每次渲染后都执行清理或者执行 effect 可能会导致性能问题。
在 class 组件中，我们可以通过在 componentDidUpdate 中添加对 prevProps 或 prevState 的比较逻辑解决：
```javascript
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

如果某些特定值在两次重渲染之间没有发生变化，你可以通知 React 跳过对 effect 的调用，只要传递数组作为 useEffect 的第二个可选参数即可：
```javascript {highlight: [3]}
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

对于有清除操作的 effect 同样适用：
```javascript
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // 仅在 props.friend.id 发生变化时，重新订阅
```

## `useLayoutEffect`

> *官方提示我们：尽可能使用标准的 useEffect 以避免阻塞视觉更新。*

![](http://www.godrry.com/usr/uploads/2020/03/333513058.png)



`useLayoutEffect` 和 `useEffect` 的作用以及使用方式是相似的，只是在**触发的时机**上有不同。

- useEffect 在全部渲染完毕后才会执行, 也就是DOM树和CSSOM树全部绘制完毕之后DIsplay才会执行，这个时候页面已经展示完毕了

- useLayoutEffect 会在 浏览器 layout 之后，painting 之前执行


我们可以使用它来读取 DOM 布局并同步触发重渲染，也就是说可以使用它做一些干预DOM的事情，但不可否认的是，它会阻塞视觉更新，让页面展示慢了一点。

### 使用

```javascript

useLayoutEffect = () => {
  // do something...
}

```




## `useContext`
> useContext(MyContext) 相当于 class 组件中的 `static contextType = MyContext` 或者 `<MyContext.Consumer>`。
> 它只是让你能够读取 context 的值以及订阅 context 的变化。你仍然需要在上层组件树中使用 `<MyContext.Provider>` 来为下层组件提供 `context`。

在class组件中，通过使用`React.creatContext()`方式创建一个Context并指定Context value，在其子组件下，挂载在 class 上的 contextType 属性会被重赋值为一个由 `React.createContext()` 创建的 Context 对象。这能让你使用 <span style="color: red">__this.context__</span> 来消费最近 Context 上的那个值。你可以在任何生命周期中访问到它，包括 render 函数中。

在函数组件中，使用<span style="color: red">__useContext(CONTEXT)__</span>, `CONTEXT`必须是React.createContext创建出来的context 对象本身


```javascript {highlight: [13, '17-19', 32]}
const themes = {
  light: {
    foreground: "#000000",
    background: "#eeeeee"
  },
  dark: {
    foreground: "#ffffff",
    background: "#222222"
  }
};

// 创建一个顶层上下文Context，用于子节点获取。themes.light是一个默认值
const ThemeContext = React.createContext(themes.light); 

function App() {
  return (
    <ThemeContext.Provider value={themes.dark}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton() {
  const theme = useContext(ThemeContext);

  return (
    <button style={{ background: theme.background, color: theme.foreground }}>
      I am styled by theme context!
    </button>
  );
}
```

### 何时更新
当组件上层最近的 `<MyContext.Provider>` 更新时，该 Hook 会触发重渲染，并使用最新传递给 `MyContext provider` 的 `context value` 值。即使祖先使用 `React.memo` 或 `shouldComponentUpdate`，也会在组件本身使用 `useContext` 时重新渲染。

这个机制和class组件一致。


## `useReducer`

> 听这个api的名字，感到熟悉了吗？是的，就像是Redux似的。

```javascript
/*
  reducer: fn
  initialArg: 指定初始值
  init 可以省略: fn, 会自动的传入initialArg, init(initialArg)
*/
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

它可以看做是`useState`的替代方案，像Redux一样提供 数据管理 的方式， 但是Redux是全局的，`useReducer`是局部的。

接收一个形如 `(state, action) => newState` 的 reducer，返回当前的state 以及 配套的 `dispatch` 方法。

如果`state`逻辑复杂且包含多个子值，当你使用`useState`重新赋值时会感到非常麻烦，又要保存之前不必更改的部分，又要修改你需要修改的地方，这很让人头疼...

使用 `useReducer` 还能给那些会触发深更新的组件做性能优化，因为你可以向子组件传递 `dispatch` 而不是回调函数。


### 使用

- 指定初始值

  ```javascript {highlight: [2]}
  ...
  const initialState = {count: 0};

  function reducer(state, action) {
    switch (action.type) {
      case 'increment':
        return {count: state.count + 1};
      case 'decrement':
        return {count: state.count - 1};
      default:
        throw new Error();
    }
  }

  function Counter() {
    const [state, dispatch] = useReducer(reducer, initialState);
    return (
      <>
        Count: {state.count}
        <button onClick={() => dispatch({type: 'decrement'})}>-</button>
        <button onClick={() => dispatch({type: 'increment'})}>+</button>
      </>
    );
  }
  ...
  ```

- 惰性初始化
  也就是：使用第三个`init`函数对`initialArg`进行某些操作

  ```javascript {highlight: ['1-3', 11, 12, 24]}
  function init(initialCount) {
    return {count: initialCount};
  }

  function reducer(state, action) {
    switch (action.type) {
      case 'increment':
        return {count: state.count + 1};
      case 'decrement':
        return {count: state.count - 1};
      case 'reset':
        return init(action.payload);
      default:
        throw new Error();
    }
  }

  function Counter({initialCount}) {
    const [state, dispatch] = useReducer(reducer, initialCount, init);
    return (
      <>
        Count: {state.count}
        <button
          onClick={() => dispatch({type: 'reset', payload: initialCount})}>

          Reset
        </button>
        <button onClick={() => dispatch({type: 'decrement'})}>-</button>
        <button onClick={() => dispatch({type: 'increment'})}>+</button>
      </>
    );
  }
  ```

## `useCallback`

useCallback 是一种__调优__手段，返回一个`memoized`回调__函数__。

### 使用

```javascript
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

把内联回调函数`doSomething()`及 依赖数组`[a, b]`作为参数传入`useCallback`，它将返回该回调函数的记忆化版本，该回调`memoizedCallback`仅在某个依赖项改变时财辉更新。

当把`memoizedCallback`传递给经过优化的并使用引用相等性去避免非必要渲染（例如 shouldComponentUpdate）的子组件时，它将非常有用。

子组件就可以在某些地方（例如 shouldComponentUpdate）里根据某些条件去判断是否执行以更新

__PS__:

- 依赖项数组不会作为参数传给回调函数。
- `useCallback(fn, deps)` 相当于 `useMemo(() => fn, deps)`。



## `useMemo`

`useMemo` 是一种__调优__手段，返回一个`memoized`回调__值__。

### 使用

```javascript
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

把“创建”函数和依赖项数组作为参数传入`useMemo`，当依赖项更改时才会重新计算memoized值。

传入 useMemo 的函数会在渲染期间执行，所以建议在这个函数内部执行和渲染有关系的操作，比如页面渲染某个数值，而你又会在useMemo中进行计算等操作。

如果没有提供依赖项数组，useMemo 在每次渲染时都会计算新的值。

### 注意

先编写在没有 `useMemo` 的情况下也可以执行的代码 —— 之后再在你的代码中添加 useMemo，以达到优化性能的目的。不要过度耦合才是关键。

`useMemo` 本身也有开销。
useMemo 会「记住」一些值，同时在后续 render 时，将依赖数组中的值取出来和上一次记录的值进行比较，如果不相等才会重新执行回调函数，否则直接返回「记住」的值。
这个过程本身就会消耗一定的内存和计算资源。因此，__过度使用 useMemo 可能会影响程序的性能__。


## `useRef`

> ref: referrence, 参考、引用
> useRef() Hook 不仅可以用于 DOM refs。「ref」 对象是一个 current 属性可变且可以容纳任意值的通用容器，类似于一个 class 的实例属性。

`useRef` 返回一个可变的ref对象，其`.current`属性被初始化为传入的参数（initialValue）。

<span style="color: red">注意</span>：返回的ref对象在组件的整个生命周期内保持不变，所以如果你把ref对象作为依赖项传入`useEffect`的依赖数组里，那这个hook就不会再次执行了

```javascript
const refContainer = useRef(initialValue);
```

### 使用

```javascript {highlight: [2, 5, 9]}
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```
- 使用`useRef`创建一个ref对象（inputEl），初始值可以设为`null`
- 在DOM中某个元素通过自定义属性 `ref={inputEl}` 进行挂载
- 在需要使用的地方通过`inputEl.current` 对DOM元素进行访问

## forwardRef

顾名思义：**转发Ref对象，以供操作**

 在使用类组件的时候，创建 ref 返回一个对象，该对象的 current 属性值为空

 只有当它被赋给某个元素的 ref 属性时，才会有值

 所以父组件（类组件）创建一个 ref 对象，然后传递给子组件（类组件），子组件内部有元素使用了

 那么父组件就可以操作子组件中的某个元素

 但是函数组件无法接收 ref 属性 <Child ref={xxx} /> 这样是不行的

 所以就需要用到 `forwardRef` 进行转发

- forwardRef 可以在父组件中操作子组件的 ref 对象
- forwardRef 可以将父组件中的 ref 对象转发到子组件中的 dom 元素上
- 子组件接受 props 和 ref 作为参数

### 使用：

```javascript {highlight: [2, 8, 12, 19]}

function Child(props,ref){  // 子组件接收2个参数：props、ref
  return (
    <input type="text" ref={ref}/>
  )
}
// 子组件通过forwardRef进行再次包裹，你可以想象它是一个高阶组件的形式
Child = React.forwardRef(Child);  

function Parent(){
  let [number,setNumber] = useState(0); 
  const inputRef = useRef();  // 父组件创建一个空的Ref对象 {current:''}
  function getFocus(){
    inputRef.current.value = 'focus';
    inputRef.current.focus();
  }
  return (
      <>
        <Child ref={inputRef}/> // 将Ref对象通过传值得方式挂载到子组件上
        <button onClick={()=>setNumber({number:number+1})}>+</button>
        <button onClick={getFocus}>获得焦点</button>
      </>
  )
}

```


## 使用Hooks 请求数据

如果要使用Hooks 请求数据，那么请求数据的函数需要放置在 `useEffect`中，这是因为`useEffect`是一个专注**副作用**的钩子函数。

它的执行时机相当于class组件中的`componentDidMount`、`componentWillUpdate`、`componentWillUnmount` 的组合体。

但是在`useEffect`中请求数据，我们需要一下：

默认情况下，请求数据的过程可能会执行多次（`mount/update`），甚至会有进入死循环，获取数据 => state变化 => 更新dom => 导致`useEffect`再次执行=> loop loop


```javascript {highlight:['8-14', 39]}
// bad
import React, { useState, useEffect } from 'react';
import axios from 'axios';

function App() {
  const [data, setData] = useState({ hits: [] });

  useEffect(async () => {
    const result = await axios(
      'http://hn.algolia.com/api/v1/search?query=redux',
    );

    setData(result.data); //  如果每次拿到的data不同，则会陷入死循环
  });


  return (
    <ul>
      {data.hits.map(item => (
        <li key={item.objectID}>
          <a href={item.url}>{item.title}</a>
        </li>
      ))}
    </ul>
  );
}

export default App;

// good
...

useEffect(async () => {
  const result = await axios(
    'http://hn.algolia.com/api/v1/search?query=redux',
  );

  setData(result.data);
}, []); // 这样，只会在组件mount时执行一次，update时就不更新了

...
```

但是这样还有一个问题，如果我们使用的是`async/await`的方式，那么它默认是会返回一个promise的，但是对于`useEffect`来说，它应该是什么都不返回的，或者返回一个clean up函数，用于清除副作用（组件unmount时会执行）。
```javascript {highlight:['7-9']}
 useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {  // 清除函数，避免内存泄漏等问题
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

```

所以我们在之前的那一步async/await 会出现一个error：
`index.js:1452 Warning: useEffect function must return a cleanup function or nothing. 
Promises and useEffect(async () => …) are not supported, but you can call an async function inside an effect.`

知晓了这个原因，那我们可以改变思路，只要不让`useEffect` 直接返回promise就可以了:
```javascript {highlight: [12]}
...

useEffect(() => {
  
  const fetchData = async () => {
    const result = await axios(
      'http://hn.algolia.com/api/v1/search?query=redux',
    );
    setData(result.data);
  }

  fetchData();
}, []);

...
```

## HOOK FAQ

接下来 可能需要再细看一些比较具体的场景了

那么请移步官方文档吧：https://react.docschina.org/docs/hooks-faq.html