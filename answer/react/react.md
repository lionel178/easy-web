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
- [10. 什么是 JSX](#10)
- [11. React 中 fiber 是什么](#11)
- [12. state 和 props 的区别是啥](#12)
- [13. 什么是高阶组件](#13)
- [14. 受控组件 && 非受控组件](#14)
- [15. 在构造函数调用 super 并将 props 作为参数传入的作用是啥](#15)
- [16. React 和原生事件的执行顺序是什么？可以混用吗？](#16)
- [17. react 中的方法使用 bind 和箭头函数的区别](#17)
- [18. 为何 react 事件要自己绑定 this](#18)
- [19. setState 更新是同步的还是异步的](#19)
- [20. 什么是 prop drilling？如何避免？](#20)
- [21. 描述 Flux 与 MVC](#21)
- [22. 说一下 Redux 的原理，介绍下整体的一个工作流程](#22)
- [23. 说一下 redux 的设计思想（理念、原则）](#23)
- [24. 说一下 redux 有哪些优缺点？](#24)

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

#### <a href="#6" id="6">6. React 中 StrictMode (严格模式) 是什么？ </a>

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

#### <a href="#10" id="10">10. 什么是 JSX</a>

- JSX 是 JavaScript XML，是 React 提供的一种语法糖，可以让我们在 JS 中写 html 标记语言。
- JSX 本身不能被浏览器读取，需要使用 babel + webpack 将其转换为传统的 JS。

<br/>
<br/>

#### <a href="#11" id="11">11. fiber 是什么</a>

- Fiber 是 React 16 中新的协调引擎或者说是重新实现核心算法，我理解为一种任务分割调度的算法。
- React Fiber 能够将渲染工作分割成块，并将其分散到多个帧中。
- 它的主要目标是支持虚拟 DOM 的增量渲染。 React Fiber 的目标是提高其在动画、布局、手势、暂停、中止或重用等方面的适用性，并为不同类型的更新分配优先级，以及新的并发单元。

<br/>
<br/>

#### <a href="#12" id="12">12. state 和 props 的区别是啥 </a>

state 和 props 都是普通的 JS 对象，虽然他们都包含影响渲染输出的信息，但是他们在组件方面的功能是不同的

- state 是组件自己管理数据，控制自己的状态，可变
- props 是外部传入的数据参数，不可变
- 没有 state 的叫做无状态组件，有 state 的叫做有状态组件
- 多用 props，少用 state，也就是多写无状态组件

<br/>
<br/>

#### <a href="#13" id="13">13. 什么是高阶组件</a>

- 高阶组件（HOC）是接收一个组件并返回一个新组件的函数
- 是从 React 的组合特性中衍生出来的，称其为纯组件。因为它们可以接收任何动态提供的组件，但不会修改或者复制输入组件的任何行为

HOC 可以用于以下许多用例

- 代码重写、引导和逻辑抽象
- 渲染劫持
- state 抽象和操作
- props 处理

<br/>
<br/>

#### <a href="#14" id="14">14. 受控组件 && 非受控组件</a>

受控组件 和 非受控组件 主要取决于组件是否受父级传入的 props 控制

受控组件：受 React 控制的组件，是表单数据真实的唯一来源

受控组件更新 state 流程：

1. 可以通过在初始 state 中设置表单的默认值
2. 每当表单的值发生变化时，调用 onChange 事件处理器。
3. 事件处理器通过合成事件对象 e 拿到改变后的状态，并更新 state
4. setState 触发视图的重新渲染，完成表单组件值的更新。

非受控组件：由 DOM 处理表单数据。

- 非受控组件的值不受自身的 state 或者 props 控制，通常需要为其添加 ref 来访问它的地城 DOM

> 一般建议优先选择受控组件：1. 支持即时字段校验 2. 允许有条件的禁用/启动按钮 3. 强制输入格式

<br/>
<br/>

#### <a href="#15" id="15">15. 在构造函数调用 super 并将 props 作为参数传入的作用是啥</a>

- 在调用 super 之前，子类构造函数无法使用 this 引用， ES6 子类也是这样
- super() 可以让我们使用 this 来调用各种东西
- 而 super(props) 可以让我们在 this 的基础上使用构造函数里面的东西， 或者从父元素那边传过来的一些属性
- 如果只调用了 super()，那么 this.props 在 super() 和构造函数结束之间仍是 undefined

参考 / 来源：

[github fe-interview](https://github.com/haizlin/fe-interview/issues/898)

<br/>
<br/>

#### <a href="#16" id="16">16. React 和原生事件的执行顺序是什么？可以混用吗？</a>

React 的事件处理程序要等到事件冒泡到 document 后才能捕获。

所以原生的事件会先执行，在事件流的最后冒泡结束后，才执行 React 的合成事件，然后触发真正在 document 上挂载的事件。

最好不要混用，因为如果在原生事件中执行 `stopPropagation`，会导致后续不能冒泡，最终导致所有的 React 事件都无法被触发。

参考 / 来源：
[React 高频面试题梳理，看看面试怎么答？](https://zhuanlan.zhihu.com/p/82840768)

<br/>
<br/>

#### <a href="#17" id="17">17. react 中的方法使用 bind 和箭头函数的区别</a>

bind 有两种方式，一种是在 constructor 中 bind ，另外一种是在调用也就是 render 里面 bind

箭头函数也有两种:

- 一种是在定义的时候就用箭头函数：`handleClick = () => {}`
- 第二种是在调用的时候使用 `<button onClick={() => this.handleClick}/>`

箭头函数的原理就是借助 箭头函数自身没有 this，要往定义时候的上层找， class Component 又是被 new 出来的，所以箭头函数可以保证方法可以通过 this 找到。

箭头函数的第二种使用方法有一个缺点，每次调用会产生一个新的方法，这样导致你对子组件做的 memo 优化没有效果。

参考 / 来源：
[React 高频面试题梳理，看看面试怎么答？](https://zhuanlan.zhihu.com/p/82840768)

<br/>
<br/>

#### <a href="#18" id="18">18. 为何 react 事件要自己绑定 this</a>

在 react 的事件处理流程中，React 在 document 上进行统一的事件分发，dispatchEvent 通过循环调用所有层级的事件来模拟事件冒泡和捕获。

在 React 源码中，当具体到某一事件处理函数将要调用时，将调用 invokeGuardedCallback 方法。

```js
function invokeGuardedCallback(name, func, a) {
  try {
    func(a);
  } catch (x) {
    if (caughtError === null) {
      caughtError = x;
    }
  }
}
```

可见，事件处理函数是直接调用的，并没有指定调用的组件，所以不进行手动绑定的情况下直接获取到的 this 是不准确的，所以我们需要手动将当前组件绑定到 this 上。

参考 / 来源：
[React 高频面试题梳理，看看面试怎么答？](https://zhuanlan.zhihu.com/p/82840768)

<br/>
<br/>

#### <a href="#19" id="19">19. setState 更新是同步的还是异步的</a>

- 如果是正常情况下，也就是没有使用 Concurrent 组件（React16 新加的）的情况下，setState 是同步更新的，但是不会立即获取到最新的 state 的值，因为调用 setState 只是单纯的将你传进来的新的 state 放入 updateQueue 这条链表上，等这个点击事件（react 叫这类事件为合成事件，只有合成事件会触发回调事件进行更新 state）结束后，会触发内部的一个回调函数，在这个回调函数中，才会真正的去更新 State 以及重新渲染

- 当使用了 Concurrent 组件的时候，这种情况下才是真正的异步更新模式，同样的没法立即获取最新的状态，并且在执行 React 的更新和渲染（这个渲染是创建 Fiber 的过程）的过程中，使用了真正的异步方式（API：postMessage），会将 Fiber 的更新过程放在 eventLoop 里面进行

- 当使用了 flushSync（React 新版本提供的）这个 API 的时候，React 的更新渲染完全是同步的，React 会立即触发更新 state 以及渲染的过程，这种情况是可以获取到最新状态的

<br/>
<br/>

#### <a href="#20" id="20">20. 什么是 prop drilling？如何避免？</a>

在 React 组件中，在多层嵌套组件中需要使用另一个嵌套组件提供的数据，一般使用 props 从父级组件一层层将数据传递下去。

将 props 从源组件传递到深层嵌套组件，叫做 prop drilling

1. 缺点：原本不需要该数据的组件变得复杂，难以维护
2. 如何避免： React Context、 Redux 或者 Hooks 进行数据管理

<br/>
<br/>

#### <a href="#21" id="21">21. 描述 Flux 与 MVC</a>

传统的 MVC 模式在分离数据（Model）、UI（View）和逻辑（Controller）方面工作得很好，但是 MVC 架构经常遇到两个主要问题：

1. 数据流不够清晰：跨视图发生的级联更新常常会导致混乱的事件网络，难于调试
2. 缺乏数据完整性：模型数据可以在任何地方发生突变，从而在整个 UI 中产生不可预测的结果

使用 Flux 模式的复杂用户界面不再遭受级联更新，任何给定的 React 组件都能够根据 store 提供的数据重建其状态。 Flux 模式还通过限制对共享数据的直接访问来加强数据完整性

<br/>
<br/>

#### <a href="#22" id="22">22. 说一下 Redux 的原理，介绍下整体的一个工作流程 </a>

redux 本身只是一个状态管理库，它只有三个核心概念：state、action 和 reducer。

使用流程：

1. 将需要修改的 state 都存入到 state 里，
2. 发起一个 action 用来描述发生了什么
3. 用 reducers 描述 action 如何改变 state tree。

工作流程：

1. 用户触发某种操作，发送一个 action
2. redux 接收到这个 action 后通过 reducer 函数获取到下一个状态
3. 将新状态更新进 store

redux 核心就是一个 发布-订阅 模式，一旦 store 发生了变化就会通知所有的订阅者，订阅者一般是接到通知后进行重新渲染。

<br/>
<br/>

#### <a href="#23" id="23">23. 说一下 redux 的设计思想（理念、原则）</a>

1. **单一事实来源**：整个应用的状态存储在单个 store 中的对象/状态树里。单一状态树可以更容易地追踪数据随时间的变化，并调试或检查应用程序。
2. **状态只读**：改变状态的唯一方法是去触发一个动作。动作是描述变化的普通 JS 对象。
3. **使用纯函数进行更改**

Redux 由一下组件组成：Store、Action、Reducer、View

<br/>
<br/>

#### <a href="#24" id="24">24. 说一下 redux 有哪些优缺点？</a>

优点：

- **结果的可预测性** - 由于总是存在一个真实来源，即 store ，因此不存在如何将当前状态与动作和应用的其他部分同步的问题。
- **可维护性** - 代码变得更容易维护，具有可预测的结果和严格的结构。
- **便于服务器端渲染** - 你只需将服务器上创建的 store 传到客户端即可。这对初始渲染非常有用，并且可以优化应用性能，从而提供更好的用户体验。
- **便于调试** - 从操作到状态更改，开发人员可以实时跟踪应用中发生的所有事情。
- **便于测试** - Redux 的代码主要是小巧、纯粹和独立的功能。这使代码可测试且独立。
- **社区和生态系统** - Redux 背后有一个巨大的社区，这使得它更加迷人。一个由才华横溢的人组成的大型社区为库的改进做出了贡献，并开发了各种应用。
- **组织** - Redux 准确地说明了代码的组织方式，这使得代码在团队使用时更加一致和简单。

缺点：

- 应用中，很多状态需要抽象到 store，没有统一标准何时使用 local states 何时接入 redux store
- 流程太繁琐，需要用各种 action，reducer
- 想获取异步数据，需要配合其他库

总结缺点：它没有成熟到自己阻止不需要用它的人 用它

<br/>
<br/>
