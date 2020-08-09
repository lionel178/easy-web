# react 面试题

## 目录

- [1. React 中 setState 是同步的还是异步的](#1)
- [2. React 组件通信如何实现](#2)
- [3. React 中函数组件和普通组件有什么区别](#3)
- [4. 说一下 React 生命周期，以及新版本中都有哪些改变，为什么去掉了那几个旧的生命周期](#4)
- [5. 知道 Portal 吗，有哪些使用场景？](#5)
- [6. React 中 StrictMode (严格模式) 是什么？](#6)
- [7. 什么是纯函数 && 什么是纯组件](#7)
- [8. 请求接口放在哪个生命周期](#8)
- [9. React 事件合成机制是什么？为什么要合成？](#9)

## 题解

#### <a href="#1" id="1">1. React 中 setState 是同步的还是异步的</a>

有时表现出同步，有时表现出异步

1. setState 只在 React 自身的合成事件和钩子函数中是异步的，在原生事件和 setTimeout 中都是同步的。

2. setState 的异步并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形成了所谓的异步，当然可以通过第二个参数 `setState(partialState, callback)` 中的 callback 拿到更新后的结果。
3. setState 的批量更新优化也是建立在异步（合成事件、钩子函数）之上的，在原生事件和 setTimeout 中不会批量更新，在异步中如果对同一个值进行多次 setState， setState 的批量更新策略会对其进行覆盖，取最后一次的执行，如果是同时 setState 多个不同的值，在更新时会对其进行合并批量更新。

<br/>
<br/>

#### <a href="#2" id="2">2. React 组件通信如何实现</a>

React 组件通信方式：

1. 父 -> 子：父组件可以向子组件通过传 props 的方式，向子组件进行通讯

2. 子 -> 父：props + 回调，父组件向子组件传递 props 进行通讯，此 props 为作用域为父组件自身的函数，子组件调用该函数，将子组件想要传递的信息，作为参数，传递到父组件的作用域中

3. 兄弟组件通信：找到这两个兄弟节点共同的父节点，结合上面两种方式由父节点转发信息进行通信

4. 跨层级通信： Context 设计目的是为了共享那些对于一个组件树而言是全局的数据，对于跨越多层的全局数据通过 Context 通信再合适不过

5. 发布订阅模式：我们可以通过引入 event 模块进行通信

6. 全局状态管理工具：借助 Redux 或者 Mobx 等全局状态管理工具进行通信，这种工具会维护一个全局状态中心 Store，并根据不同的事件产生新的状态。

<br/>
<br/>

#### <a href="#3" id="3">3. React 中函数组件和普通组件有什么区别 </a>

- 类组件： 可以使用其他特性，如状态和生命周期函数
- 函数组件：也称为哑组件、展示组件、无状态组件，当组件只接收 props 渲染到页面时，就是无状态组件，称为函数组件

函数组件的性能比类组件性能高，因为类组件在使用时要实例化，而函数组件之间调用函数取返回结果即可。

函数组件没有 this、生命周期、state

<br/>
<br/>

#### <a href="#4" id="4">4. 说一下 React 生命周期，以及新版本中都有哪些改变，为什么去掉了那几个旧的生命周期</a>

初始化阶段

- `constructor` : class 的构造函数

挂载阶段

- `componentWillMount`: 组件初始化渲染前执行 `16设为不安全 17弃用`
- `static getDerivedStateFromProps`: `16 新增`
- `render`: 组件渲染
- `componentDidMount`: 组件被挂载到 DOM 后触发

更新阶段

- `componentWillReceiveProps`: 组件将要接收新的 prop 前执行 `16设为不安全 17弃用`
- `static getDerivedStateFromProps`: 替代 componentWillReceiveProps `16 新增`
- `shouldComponentUpdate`: 组件是否需要更新
- `componentWillUpdate`: 组件更新前执行 `16设为不安全 17弃用`
- `render` 组件渲染
- `getSnapshotBeforeUpdate`: render 后，可以读取但无法使用 DOM 的时候 `16 新增`
- `componentDidUpdate`: 组件更新后执行

组件卸载

- `componentWillUnmount`: 组件卸载前执行

错误处理

- `componentDidCatch：` `16新增`

废弃的原因主要是因为 react 在 16 版本重构了调度算法，新的调度可能会导致一些生命周期被反复调用，所以在 16 中就不建议使用了，而改在其他时机中暴露出其他生命周期钩子用来替代。

<br/>
<br/>

#### <a href="#5" id="5">5. 知道 Portal 吗，有哪些使用场景？</a>

Portal（传送门），提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的方案。

常见使用场景

1. 父组件 overflow: hidden, 但是子组件又想展示
2. 父组件的 z-index 太小
3. fixed 需要放在 body 第一层
4. Modal 模态框、Popover 弹出的 Drawer 抽屉等

使用方法

```js
render() {
  return ReactDOM.createProtal(
    <div>{this.props.children}</div>,
    body
  )
}
```

<br/>
<br/>

#### <a href="#6" id="6">6.  React 中 StrictMode (严格模式) 是什么？ </a>

React 的 StrictMode 是一种辅助组件，可以帮助我们编写更好的 React 组件，可以使用 `<StrictMode />`包装一组组件，并且可以帮我们做一下检查：

1. 验证内部组件是否遵循某些推荐做法，如果没有，会在控制台给出警告
2. 验证是否使用了已经废弃的方法，如果有，会在控制台给出警告，例如：
   - 失败不安全的生命周期
   - 过时的字符串 ref API 警告
   - 废弃的 findDOMNode 方法警告
   - 过时的 context API 警告
3. 通过识别一些潜在的风险预防一些副作用

<br/>
<br/>

#### <a href="#7" id="7">7. 什么是纯函数 && 什么是纯组件 </a>

- 纯函数是不依赖并且不会在其作用域外修改变量状态的函数。本质上，纯函数始终在给定相同参数的情况下返回相同的结果。

- 纯组件是通过控制 sholdComponentUpdate 生命周期函数，减少 render 渲染次数来减少性能损耗的。

  1. 优点：这相对于 Component 来说，减少了手动判断 state 变化的繁琐操作。
  2. 缺点：以为 PureComponent 只进行了一层浅比较，简单来说，它只比较了 props 和 state 的内存地址，如果内存地址相同，则 shouldComponentUpdate 就会返回 false
  3. PureComponent 应用场景：局部数据发生改变的时候，比如带有 输入框、switch 开关等的 UI 组件就可以使用 PureComponent 组件封装， PureComponent 中如果有数据操作最好配合一个第三方组件——Immutable 一起使用，Immutable 可以保证数据的不变性。

PureComponent 浅比较出现的问题的解决方案：

- 当知道有深度数据结构更新是，可以直接调用 forceUpdate 强制更新
- 考虑使用 immutable objects，实现快速的比较对象。

<br/>
<br/>

#### <a href="#8" id="8">8. 请求接口放在哪个生命周期</a>

建议放在 componentDidMount() 中。

不能放在 render() 中，因为会重复调用

不能放在 componentWillMount() 中，react16.0 以后，该生命周期可能会被执行多次，即将在 react17 中弃用。

constructor() 之中，如果请求时间太长或者出错，会导致组件渲染失败，另外 constructor 本意是作为组件 state 初始化工作，不建议添加有副作用的代码进去 ​。​

<br/>
<br/>

#### <a href="#9" id="9">9. React 事件合成机制是什么？为什么要合成？</a>

React 根据 W3C 规范定义了每个事件处理函数的参数，即事件合成。

1. div 或其他元素触发事件，该事件会冒泡到 document，然后被 React 的事件处理程序捕获
2. 事件处理程序随后将事件传递给 SyntheticEvent 的实例，这是一个跨浏览器原生事件包装器。
3. SyntheticEvent 触发 dispatchEvent，将 event 对象交由对应的处理器执行。

为什么要合成事件机制？

- 更好的兼容性和跨平台，摆脱传统 DOM 事件
- react 合成的 SyntheticEvent 采用了事件池，这样可以大大节省内存，而不会频繁的创建和销毁事件对象。
- 方便事件的统一管理，如：事务机制

<br/>
<br/>