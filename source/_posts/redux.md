---
title: redux
date: 2021-01-05 00:00:00
tags: 
  - react
  - redux
categories: 
  - react
  - redux
---

## redux使用规则
1. 使用configureStore创建 Redux store
2. 为 React 应用程序组件提供 Redux store
    1. 使用 React-Redux <Provider> 组件包裹你的 <App />
    2. 传递 Redux store 如 <Provider store={store}>
3. 使用 createSlice 创建 Redux "slice" 切片
    1. 使用字符串名称、初始状态和命名的 reducer 函数调用“createSlice”
    2. Reducer 函数可以使用 Immer 来“改变”状态
    3. 导出生成的 slice reducer 和 action creators
4. 在 React 组件中使用 React-Redux useSelector/useDispatch 钩子
    1. 使用 useSelector 钩子从 store 中读取数据
    2. 使用 useDispatch 钩子获取 dispatch 函数，并根据需要 dispatch actions

## redux基础概念
1. Redux 是什么？
   Redux 是一个使用叫做“action”的事件来管理和更新应用状态的模式和工具库 它以集中式Store（centralized store）的方式对整个应用中使用的状态进行集中管理，其规则确保状态只能以可预测的方式更新。
2. 为什么要使用 Redux？
   Redux 提供的模式和工具使您更容易理解应用程序中的状态何时、何地、为什么以及如何更新，以及当这些更改发生时您的应用程序逻辑将如何表现. Redux 指导您编写可预测和可测试的代码，这有助于让您确信您的应用程序将按预期工作
3. 我什么时候应该使用 Redux？
   redux有利有弊端，因为是一个新型的数据管理模式，这就需要去学习他本身的一些思想，这样就增加了学习成本。如果你的项目比较小的，那么并不需要引用redux，
   如有以下的情况的话，那么建议使用redux
   1. 在应用的大量地方，都存在大量的状态
   2. 应用状态会随着时间的推移而频繁更新
   3. 更新该状态的逻辑可能很复杂
   4. 中型和大型代码量的应用，很多人协同开发
4. Redux 术语和概念
   1. State 管理
      单向数据流（one-way data flow）
      1. 用 state 来描述应用程序在特定时间点的状况
      2. 基于 state 来渲染出 View
      3. 当发生某些事情时（例如用户单击按钮），state 会根据发生的事情进行更新，生成新的 state
      4. 基于新的 state 重新渲染 View
      解决多个组件共享一个state；可以把共享的state抽取出来放置到组件树之外的地方，这样的话，组件树中任意一个组件就可以直接访问到共享的state。这样就使得view和state之间变的独立。
      Redux 背后的基本思想：应用中使用集中式的全局状态来管理，并明确更新状态的模式，以便让代码具有可预测性。
   2. 不可变性 Immutability
      对象和数组都是可变的。虽然引用的地址没有变化，但是实际的指向值确变化了
      如果想要不可变的方式来更新，代码必需先复制原来的 object/array，然后更新它的复制体。JavaScript array/object 的展开运算符（spread operator）可以实现这个目的
      Redux 期望所有状态更新都是使用不可变的方式。
   3. 术语
      1. Action
         action 是一个具有 type 字段的普通 JavaScript 对象。你可以将 action 视为描述应用程序中发生了什么的事件.
      2. Action Creator
         action creator 是一个创建并返回一个 action 对象的函数。它的作用是让你不必每次都手动编写 action 对象
      3. Reducer
         reducer 是一个函数，接收当前的 state 和一个 action 对象，必要时决定如何更新状态，并返回新状态。函数签名是：(state, action) => newState。 你可以将 reducer 视为一个事件监听器，它根据接收到的 action（事件）类型处理事件。
         reducer和Array.reduce()有些类似，都是接收上一个结果和当前项进行计算获取到一个最新的值。不同是的是Array.reduce()会立即执行。Redux 时，它会在您正在运行的应用程序的整个生命周期内发生。
      4. Store
         当前 Redux 应用的状态存在于一个名为 store 的对象中。
         store 是通过传入一个 reducer 来创建的，并且有一个名为 getState 的方法，它返回当前状态值
      5. Dispatch
         更新 state 的唯一方法是调用 store.dispatch() 并传入一个 action 对象。
      6. Selector
         Selector 函数可以从 store 状态树中提取指定的片段。
   





