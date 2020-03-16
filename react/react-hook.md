# React Hook 
Hook 是React 16.8版本中新增的内容，它的出现让函数组件拥有了类似class组件的能力（生命周期等），同时也优化了一些过去的缺点，例如逻辑复用复杂、多个生命周期函数多次调用等。

## `useState`




## `useEffect`





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