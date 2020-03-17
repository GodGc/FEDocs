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



## `useCallback`




## `useMemo`



## `useRef`


## `useLayoutEffect`



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