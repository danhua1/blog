---
title: React Api
---

## React

### React.Component

React.Component 是使用 ES6 classes 方式定义 React 组件的基类：
```
class Greeting extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}

```
### React.PureComponent
React.PureComponent 和React.Component很类似。区别在于React.PureComponent中使用了shouldComponentUpdate() 类简单的判断props和state中是否有变化，然后决定是否更新页面。注意这儿只能简单的判断数据是否一致，碰见复杂的数据接口判断就会不会那么准确。看情况使用。

### React.memo
这个和React.PureComponent有些类似，主要用作都是通过判断props中是否有变化来减少渲染次数。但是有些不同的地方在于，memo只能用于函数组件。其次如果组件中存在useState 或 useContext 的 Hook，当useContext发生变化的时候。那么组件还是会继续渲染的。
当数据比较复杂的时候，可以通过第二个参数来解决数据对比的问题 React.memo(MyComponent, areEqual);第一个是包裹的组件，第二个参数是对比的逻辑。

### createElement()
使用jsx编写组件的话，会进行如下的转换
```
React.createElement(
  type,
  [props],
  [...children]
)
```
### cloneElement()
```
React.cloneElement(
  element,
  [props],
  [...children]
)
```
以element元素为样板克隆一份，但是会把现有的props以及原有的props进行简单的合并。但是克隆组件的key以及ref都被留存下来。事例如下：
```
<element.type {...element.props} {...props}>{children}</element.type>
```
两者几乎是相等的。

### React.lazy
  动态加载组件，配合路由使用的情况比较多.需要配合Suspense一并使用。
```
const SomeComponent = React.lazy(() => import('./SomeComponent'));
```

### React.Suspense
配合lazy一起使用，当懒加载的时候，给出优雅的提示信息。比如loading框
```
// 该组件是动态加载的
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    // 显示 <Spinner> 组件直至 OtherComponent 加载完成
    <React.Suspense fallback={<Spinner />}>
      <div>
        <OtherComponent />
      </div>
    </React.Suspense>
  );
}
```

##  React.Component

### 组件的生命周期

[生命周期图解](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)

#### 挂载
当组件实例被创建并插入 DOM 中时，其生命周期调用顺序如下：
1. constructor()
2. static getDerivedStateFromProps()
3. render()
4. componentDidMount()

#### 更新
当组件的 props 或 state 发生变化时会触发更新。组件更新的生命周期调用顺序如下：
1. static getDerivedStateFromProps()
2. shouldComponentUpdate()
3. render()
4. getSnapshotBeforeUpdate()
5. componentDidUpdate()

#### 卸载
当组件从 DOM 中移除时会调用如下方法：
1. componentWillUnmount()

#### 错误处理
当渲染过程，生命周期，或子组件的构造函数中抛出错误时，会调用如下方法：
1. static getDerivedStateFromError()
2. componentDidCatch()

### 常用的生命周期方法
#### render()
render() 方法是 class 组件中唯一必须实现的方法。
当 render 被调用时，它会检查 this.props 和 this.state 的变化并返回以下类型之一：
1. React 元素。通常通过 JSX 创建。例如，<div /> 会被 React 渲染为 DOM 节点，<MyComponent /> 会被 React 渲染为自定义组件，无论是 <div /> 还是 <MyComponent /> 均为 React 元素。
2. 数组或 fragments。 使得 render 方法可以返回多个元素
3. Portals。可以渲染子节点到不同的 DOM 子树中。
4. 字符串或数值类型。它们在 DOM 中会被渲染为文本节点
5. 布尔类型或 null。什么都不渲染。（主要用于支持返回 test && <Child /> 的模式，其中 test 为布尔类型。)

render() 函数应该为纯函数，这意味着在不修改组件 state 的情况下，每次调用时都返回相同的结果，并且它不会直接与浏览器交互。

#### constructor()
如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数。
在为 React.Component 子类实现构造函数时，应在其他语句之前前调用 super(props)。否则，this.props 在构造函数中可能会出现未定义的 bug。
通常，在 React 中，构造函数仅用于以下两种情况：
1. 通过给 this.state 赋值对象来初始化内部 state。
2. 为事件处理函数绑定实例

### 其他 API
不同于上述生命周期方法（React 主动调用），以下方法是你可以在组件中调用的方法。

只有两个方法：setState() 和 forceUpdate()。
#### setState()
将 setState() 视为请求而不是立即更新组件的命令。为了更好的感知性能，React 会延迟调用它，然后通过一次传递更新多个组件。React 并不会保证 state 的变更会立即生效。
setState并不是立即就更新数据的，会批量延迟更新。同事如果批量更新同一个数据的时候，后面的更新会把前面的更新覆盖掉。导致前面的更新是失效的。
如果后续的计算需要上一次的数据的话，可以考虑使用setState() 回调函数的形式来获取最新的数据。
```
this.setState((state) => {
  return {quantity: state.quantity + 1};
});
```
和其类似的hook中的useState 他的更新是直接替换。setState是合并更新。








