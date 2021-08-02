## 前言
本文是学习React Hooks记录的学习笔记。
[推荐看一下Dan Abramov的博客](https://overreacted.io/)
## ❓什么是Hook?为什么需要Hook?

1. 逻辑复用，如果使用高阶组件等特性，较为复杂。
2. 传统的函数组件无法存储`state`状态。
3. `Hooks`允许在函数组件中，调用`React`的功能。
4. 使用自定义`Hook`可以简化逻辑复用。
5. `Hooks`是`React`的未来
6. `Hooks`不是什么魔法。`Hooks`的设计也与`React`无关。



## useState
`useState`可以在函数组件中，添加state Hook。
调用useState会返回一个state变量，以及更新state变量的方法。useState的参数是state变量的初始值，**初始值仅在初次渲染时有效**。
**更新state变量的方法，并不会像this.setState一样，合并state。而是替换state变量。**
下面是一个简单的例子, 会在页面上渲染count的值，点击setCount的按钮会更新count的值。
```js
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量
  const [count, setCount] = useState(0);

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

### 函数式更新
如果新的state需要之前的state计算得到。可以向useState返回的更新函数中，传递一个函数。函数的参数是前一个state。
![2019-08-16-16-16-53-2019816](http://images.zyy1217.com/2019-08-16-16-16-53-2019816.png)

### 惰性的初始值
`useState`的初始值是惰性的，只会在初次渲染组件的时候起作用。如果state的初始值需用通过复杂计算得到，useState的初始值也可以是一个函数，函数的返回值将是useState的初始值。
![2019-08-16-16-17-58-2019816](http://images.zyy1217.com/2019-08-16-16-17-58-2019816.png)
### 跳过state更新
调用`state`的更新函数，传入和当前一样的state时。React会跳过子组件的渲染，以及effect的执行。

![2019-08-16-16-18-30-2019816](http://images.zyy1217.com/2019-08-16-16-18-30-2019816.png)

### 小节

1. useState的更新是替换，而不是合并。
2. useState可以接收函数参数，并将函数的返回值作为初始值
3. useState的更新函数可以接收函数作为参数，函数的参数是前一状态的state值。
4. 使用当前的值，对state进行更新不会触发渲染。
5. 可以使用多个useState，声明多个state变量。

## useEffect
除了官方文档外，推荐阅读一下[Um guia completo para useEffect](https://overreacted.io/pt-br/a-complete-guide-to-useeffect/)
`useEffect`可以让我们在函数组件中执行副作用操作。事件绑定，数据请求，动态修改`DOM`。
`useEffect`将会在**每一次React渲染之后执行**。无论是初次挂载时，还是更新。（当然这种行为我们可以控制）

![2019-08-16-16-19-33-2019816](http://images.zyy1217.com/2019-08-16-16-19-33-2019816.png)

### 需要清除的effect
在传统的`class`组件中，通常在`componentDidMount`中添加对事件的监听。在`componentWillUnmount`中会清除对事件的监听。
我们需要在不同的生命周期函数中，拆分我们的逻辑。
而`effect`可以返回一个函数，当`react`进行清除时, 会执行这个返回的函数。每当执行本次的`effect`时，都会对上一个`effect`进行清除。组件卸载时也会执行进行清除。
也就是说，下面的代码中。每一次更新，都会对上一次的`effect`进行卸载，并执行本次的`effect`。

![2019-08-16-16-20-27-2019816](http://images.zyy1217.com/2019-08-16-16-20-27-2019816.png)

### effect性能优化
每次执行`effect`，清除上一次`effect`可能会造成不必要的性能浪费。我们可以通过`effect`的第二个参数，控制`effect`的执行。 第二个参数是`useEffect`的依赖，只有当依赖发生变化时，`useEffect`才会更新。
![2019-09-15-21-12-00-2019915](http://images.zyy1217.com/2019-09-15-21-12-00-2019915.png)
当我们传递传递一个空数组作为依赖时，会告诉`React`，`effect`不依赖任何`state`或者`props`。我们可以使用此行为模拟`componentDidMount`或者`componentWillUnmount`。

⚠️ 请记住使用空数组的`effect`和`componentDidMount`是有差异的
![2019-09-15-21-12-50-2019915](http://images.zyy1217.com/2019-09-15-21-12-50-2019915.png)


### 🤔️useEffect(fn, [])和componentDidMount的差异有什么差异？
类似的问题，为什么有时候在`effect`里拿到的是旧的`state`或`prop`呢？
![2019-09-15-21-14-20-2019915](http://images.zyy1217.com/2019-09-15-21-14-20-2019915.png)

**useEffect会捕获props, state，但是始终是定义时的值**。 如上图所示，当我们点击`button`多次，`effect`在3秒之后的回调，打印的依然是`state`的初始值。
#### 为什么会这样？
这是因为javascript闭包的机制，函数组件被调用后，函数内部的state由于被内部定时器的回调所依赖，所以没有被垃圾回收机制所清除，而是保存在内存里了。所以等定时器的回掉执行时，打印的是之前闭包中存储的变量。
#### 如何避免拿到旧的prop或者state？

1. 使用useRef
2. 查看是否遗漏了依赖, 导致effect没有更新

### 🤔️如何正确的useEffect中请求数据?
阅读这篇文章是一个不错的选择[How to fetch data with React Hooks?](https://www.robinwieruch.de/react-hooks-fetch-data)

#### 如何请求数据？
![2019-09-15-21-16-26-2019915](http://images.zyy1217.com/2019-09-15-21-16-26-2019915.png)

#### 如何在useEffect中使用async/await?
`async`函数默认会返回一个`Promise`对象，而`useEffect`中，只允许什么都不返回或者返回一个清除函数。控制台中，会触发如下警告
**Warning: useEffect function must return a cleanup function or nothing. Promises and useEffect(async () => …) are not supported, but you can call an async function inside an effect.. **
解决方案如下👇
![2019-09-15-21-17-18-2019915](http://images.zyy1217.com/2019-09-15-21-17-18-2019915.png)
### 小节

1. 可以使用多个`useEffect`分离逻辑。在`class`组件中，我们通常会在`componentDidMount`中，混杂添加事件绑定，请求数据等多种无关的逻辑。
2. 如果只需要`useEffect`在加载时执行一次，或者卸载组件时执行一次，可以向`useEffect`传递一个空数组。
3. `useEffect`不支持直接使用async函数
4. 如果在`useEffect`中形成了闭包，将会拿到旧的`props`，或者`state`。（这个与`Hook`无关，与函数组件本身的特性相关）
5. `useEffect`返回的函数——清除函数。会在上一个`effect`被清除时调用。

## 📰Hook规则

1. 只在最顶层使用Hook，不在判断，循环语句中使用Hook
2. 只在React函数中使用Hook

### 🤔️为什么Hook高度依赖执行顺序？
我们借助`preact`的源码进行分析，在`preact`中`Hook`的存储在组件的私有属性`__hooks._list`的数组中。读取和存储都依赖`currentIndex`的指针，如果`hook`的执行顺序改变，`currentIndex`也会被改变，获取的`hook`可能是完成错误的。
![2019-09-15-21-19-40-2019915](http://images.zyy1217.com/2019-09-15-21-19-40-2019915.png)


## 自定义Hook

1. 自定义`Hook`必须以`use`开头
2. 自定义`Hook`只是逻辑复用，其中的`state`是不会共享的。
3. 自定义Hook内部可以调用其他`Hook`。
4. 避免过早的拆分抽象逻辑

下面是一个自定义`Hook`的🌰
### 💡自定义数据获取Hook
我们通过`ajax`请求表格数据时，很多逻辑都是通用的。比如`loading`的状态的处理，错误信息的处理，翻页的处理。我们可以把这些逻辑抽象成一个公共的`Hook`。不同的`api`，作为自定义`Hook`的参数。
下面是一个数据请求自定义`Hook`的例子：
![2019-09-15-21-27-29-2019915](http://images.zyy1217.com/2019-09-15-21-27-29-2019915.png)

如何使用？从自定义Hook向外暴露一些state，和setState。当page发生改变时，会重新请求数据。
![2019-09-15-21-27-44-2019915](http://images.zyy1217.com/2019-09-15-21-27-44-2019915.png)


## 🚀其他API
### useContext
接收一个`context`对象，并返回当前的`context`的值。`useContext`可以订阅`context`的变化。但是仍然需要上层组件使用`<MyContext.Provider>`来为下层组件提供`context`。

![2019-09-15-21-28-41-2019915](http://images.zyy1217.com/2019-09-15-21-28-41-2019915.png)

### useReducer
`useReducer`接收三个参数，`reducer`函数，`initialArg`初始值，`init`惰性初始值函数。`reducer`函数和`Redux`的`reducer`类似，接收`state`，以及`action`。返回更新后的`state`。如果传入三个参数，`init`(initialArg)将作为初始值。
`useReducer`在复杂场景下比`useState`更适用。

#### 指定初始state
`useReducer`的第二个参数可以指定初始的`state`
#### 惰性初始化
如果指定`useReducer`的第三个参数，`useReducer`的初始值会被设置为`init`(`initialArg`)

![2019-09-15-21-44-18-2019915](http://images.zyy1217.com/2019-09-15-21-44-18-2019915.png)

#### 跳过dispatch
如果`ReducerHook`的返回的`state`与当前`state`相同，React将跳过子组件的渲染及`effect`的执行。
#### 🌰例子
在自定义`Hook`的例子中，使用了多个`useState`进行状态管理，当出现大量状态时，`useState`会使得逻辑变得很复杂。我们现在可以使用`useReducer`管理多个`state`，也可向子组件传递`dispatch`，而不是回调函数。
下面是将自定义`Hook`中，请求数据自定义`Hook`的例子，改造成`useReducer`的方法。

![2019-09-15-21-45-39-2019915](http://images.zyy1217.com/2019-09-15-21-45-39-2019915.png)

### useCallback
useCallback接收回调函数和依赖数组作为参数。useCallback会返回memoized函数。当依赖项改变的时候，会返回的新的memoized函数。
![2019-09-15-21-46-10-2019915](http://images.zyy1217.com/2019-09-15-21-46-10-2019915.png)


### useMemo
useMemo和useCallback类似。useMemo会返回memoized值。当依赖项改变时，会重新计算memoized值。

![2019-09-15-21-46-30-2019915](http://images.zyy1217.com/2019-09-15-21-46-30-2019915.png)

### useRef
useRef除了获取dom节点的功能外，useRef的current属性，可以方便保存任何可变值。useRef每一次渲染时，都会返回同一个ref对象。
设想下面👇这种情况, 组件重新渲染时，由于timer发生了变化，我们会永远无法清除定时器。
![2019-09-15-21-46-52-2019915](http://images.zyy1217.com/2019-09-15-21-46-52-2019915.png)


如果想要在更新后，依然可以清除定时器，可以将timer，保存到useRef的current属性上


![2019-09-15-21-47-02-2019915](http://images.zyy1217.com/2019-09-15-21-47-02-2019915.png)


### useLayoutEffect
![2019-09-15-21-47-27-2019915](http://images.zyy1217.com/2019-09-15-21-47-27-2019915.png)
（红圈中是同步的操作）
useLayoutEffect和useEffect类似，但是不同的是：

- useEffect，使用useEffect不会阻塞浏览器的重绘
- useLayoutEffect, 使用useLayoutEffect，会阻塞浏览器的重绘。如果你需要手动的修改Dom，推荐使用useLayoutEffect。因为如果在useEffect中更新Dom，useEffect不会阻塞重绘，用户可能会看到因为更新导致的闪烁，
![2019-09-15-21-47-49-2019915](http://images.zyy1217.com/2019-09-15-21-47-49-2019915.png)



## 🚗参考

- [30分钟精通React Hooks](https://juejin.im/post/5be3ea136fb9a049f9121014)
- [🌟HOOKS(提案)](https://www.reactjscn.com/docs/hooks-intro.html)
- [精读《怎么用 React Hooks 造轮子》](https://juejin.im/post/5bf20ce6e51d454a324dd0e6#heading-12)
- [深入浅出 React Hooks](https://juejin.im/post/5cf475d66fb9a07ea944594e#heading-15)
- [🌟A Complete Guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)
- [🌟Making setInterval Declarative with React Hooks](https://overreacted.io/making-setinterval-declarative-with-react-hooks/)
- [React’s useCallback and useMemo Hooks By Example](https://nikgrozev.com/2019/04/07/reacts-usecallback-and-usememo-hooks-by-example/)
