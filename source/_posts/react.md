---
title: react 学习
tags: 
  - react
  - 文档
categories: 
  - react
  - react文档
---

## 核心概念

### state&生命周期

#### state的更新会被合并

当你调用  >setState() 的时候,React 会把你提供的对象合并到当前的 state。

例如，你的 state 包含几个独立的变量：

```
     constructor(props) {
        super(props);
        this.state = {
        posts: [],
        comments: []
        };
    }

```

然后你可以分别调用 setState() 来单独地更新它们：

```
 componentDidMount() {
    fetchPosts().then(response => {
      this.setState({
        posts: response.posts
      });
    });

    fetchComments().then(response => {
      this.setState({
        comments: response.comments
      });
    });
  }
```

这里的合并是浅合并，所以 this.setState({comments}) 完整保留了 this.state.posts， 但是完全替换了 this.state.comments。


### 事件处理
避免在父传子的时候 函数使用箭头函数，因为这样的话，每次渲染都会生成一个新的函数，会导致子组件重复渲染，可能实际上并不需要渲染。

### 列表&key
避免使用index作为组件的唯一key值
元素的key 只有放在就近数组的上下文中才会有意义。就是key需要放到循环组件的最外层，放置在内层没有多大意义

```
function ListItem(props) {
  const value = props.value;
  return (
    // 错误！你不需要在这里指定 key：
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 错误！元素的 key 应该在这里指定：
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}

const numbers = [1, 2, 3, 4, 5];
ReactDOM.render(
  <NumberList numbers={numbers} />,
  document.getElementById('root')
);


```

key 只是在兄弟组件中需要唯一。但是在全局当中，可以存在相同的key
如果一个map中嵌套了多个层级，可以考虑把逻辑给提取出来，减少复杂性。


### 表单
组件整体来说可以分为两种：
1. 受控组件
2. 非受控组件

#### 受控组件
受控和非受控，主要是看组件对应的vaule，是不是由开发者自己控制。
比如一个input组件，你输入的时候就能看到输入的值。那我可不可以把显示的值由我控制，进行组装后再由页面显示
```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: ''};

    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(event) {
    this.setState({value: event.target.value});
  }


  render() {
    return (
          <input type="text" value={this.state.value} onChange={this.handleChange} />
    );
  }
}

```
如上面的事例所展示，input显示是由onChange 事件来控制的，由开发者给value赋值，展示给用户看的。
这样的方式就叫受控组件，组件可以由你自由修改。

#### 受控组件空值
当一个受控组件传递了vaule，但是并没有相对应的处理逻辑的时候，那么受控组件是无法编辑的。如果你发现了可以编辑，那么可能是你传递的vaule为null或者undefined的

### 状态提升
主要就是共用的状态可以提升到共同的父组件，由父组件进行分发，可以在usecontent中体现。

### 组合和继承
 #### 直接包含
 自定义的组件，可能需要接受非本组件的内容，这时就可以使用children来接受外部的内容

 ```
 父组件
<Test>
    {
    <div>123</div>
    }
</Test>

子组件

 const Test = (props:any) => {
    console.log(props)
    const { tabList, count,children,letf,right } = props
    console.log(children)

    return (
        <>
        {children}

        </>
    )
}
 ```

 #### 存在多个源
 有可能外部内容来源并不是一个方向，可能有多个方向。这时候可以借助属性的方式来实现。就类似于vue的插槽属性

 ```
 父组件

  <Test
    letf={<div>123</div>}
    right={<div>456</div>}
  />

子组件
 const Test = (props:any) => {
    console.log(props)
    const { tabList, count,children,letf,right } = props
    console.log(children)

    return (
        <>
        {letf}
        {right}

        </>
    )
}
 ```



 ## 高级指引

 ### 无障碍
 因为jsx组件最外层需要唯一一个根组件。但是有时候我们可能并不想要多添加一个div标签，于是Fragment 就应运而生，当空标签上面不存在任何的props的时候，我们也可以使用省略的写法<></>

 ### 代码分割
 现代的web技术，都是把多个文件打包成一个体积大的文件，这样一开始只要加载一个文件即可，避免一次性加载过多的文件，浏览器的http请求，同时只能存在6个，当6个满的话，后续的请求会等待，等待到前面有请求执行完毕后再继续执行，所以合并请求，代码合并也是前端性能优化的一个点。
 但是当一个包体积过大的时候，首屏的时间就会特别的长，这样对用户的体验感是非常差的，于是出现了很多模式，按照模块打包，每一个模块一个包。同时包加载的时候，使用懒加载的形式，也就是说：我只有用到了这个包才会进行加载，避免过多的加载数据。还有就是通过路由懒加载，每一个路由一个包，只有跳转到当前路由才会对包进行请求。
 #### React.lazy
 React.lazy 和 Suspense 技术还不支持服务端渲染。
 ```
 import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
 ```

 Suspense 是需要包裹在懒加载组件外部的。

 #### 异常捕获边界
 当模块加载失败的，需要一个错误的页面来给用户进行反馈。
 ```
 import React, { Suspense } from 'react';
import MyErrorBoundary from './MyErrorBoundary';

const OtherComponent = React.lazy(() => import('./OtherComponent'));
const AnotherComponent = React.lazy(() => import('./AnotherComponent'));

const MyComponent = () => (
  <div>
    <MyErrorBoundary>
      <Suspense fallback={<div>Loading...</div>}>
        <section>
          <OtherComponent />
          <AnotherComponent />
        </section>
      </Suspense>
    </MyErrorBoundary>
  </div>
);
 ```

 ### content

 #### content使用场景
  content 主要是用户多层级组件传递很少改动值所使用的。当多个子组件需要多个来源相同的数据的时候，可以考虑在他们共同的父组中获取数据，然后通过content进行向下分发数据，因为react数据流是自上向下的。

 #### 不要滥用content 
  因为使用content 需要添加Provider，这样的话，子组件在当前的时候需要父组件传递对应的值，如果在别的地方需要别的来源的值，这样的话会存在冲突，也就是滥用的话会导致组件的复用性会变差很多的。所以看情况使用content。当发现使用content可能会导致组件复用性下降的时候，可以考虑组件组合的方式来实现具体业务。

 #### API

 ##### Provider
多个Provider 组件是可以嵌套使用的，但是里层的数据会把外层的数据给覆盖掉。当Provider的vaule值发生变化的时候，那么内层的嵌套组件全都会重新进渲染

 ### ref转发

 ### 高阶组件
 组件是将props转化为页面，高阶组件是把组件转换成一个新的组件。也就是高阶组件就是参数为组件返回也是组件的一个函数。

 HOC 不会修改原组件，也不会使用集成等方式来复制组件，只会把组件包装到一个新的容器中形成一个新的组件。HOC 是纯函数，没有副作用

 ### 深入jsx

 #### 用户自定义的组件，首字母要大写
 开头小写的元素代表的是内置的组件，比如<div></div>，但是首字母大写的话，会被当成一个自定义的组件，如 <Foo /> 会编译为 React.createElement(Foo)
 #### 在运行时选择类型
 你不能将通用表达式作为 React 元素类型。如果你想通过通用表达式来（动态）决定元素类型，你需要首先将它赋值给大写字母开头的变量。这通常用于根据 prop 来渲染不同组件的情况下:
 ```
 import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 错误！JSX 类型不能是一个表达式。
  return <components[props.storyType] story={props.story} />;
}
 ```

 要解决这个问题, 需要首先将类型赋值给一个大写字母开头的变量：
 ```
 import React from 'react';
import { PhotoStory, VideoStory } from './stories';

const components = {
  photo: PhotoStory,
  video: VideoStory
};

function Story(props) {
  // 正确！JSX 类型可以是大写字母开头的变量。
  const SpecificStory = components[props.storyType];
  return <SpecificStory story={props.story} />;
}
 ```

 #### JavaScript 表达式作为子元素
 JavaScript 表达式可以被包裹在 {} 中作为子元素。例如，以下表达式是等价的：
 ```
 <MyComponent>foo</MyComponent>

<MyComponent>{'foo'}</MyComponent>
 ```

 这对于展示任意长度的列表非常有用。例如，渲染 HTML 列表：
 ```
 function Item(props) {
  return <li>{props.message}</li>;
}

function TodoList() {
  const todos = ['finish doc', 'submit pr', 'nag dan to review'];
  return (
    <ul>
      {todos.map((message) => <Item key={message} message={message} />)}
    </ul>
  );
}
 ```

 因为 if和for不算JavaScript的表达式，所以在子元素中无法使用。如果需要使用if for等逻辑，可以参考下面的函数作为子元素

 #### 函数作为子元素
 通常，JSX 中的 JavaScript 表达式将会被计算为字符串、React 元素或者是列表。不过，props.children 和其他 prop 一样，它可以传递任意类型的数据，而不仅仅是 React 已知的可渲染类型。例如，如果你有一个自定义组件，你可以把回调函数作为 props.children 进行传递：
 ```
 // 调用子元素回调 numTimes 次，来重复生成组件
function Repeat(props) {
  let items = [];
  for (let i = 0; i < props.numTimes; i++) {
    items.push(props.children(i));
  }
  return <div>{items}</div>;
}

function ListOfTenThings() {
  return (
    <Repeat numTimes={10}>
      {(index) => <div key={index}>This is item {index} in the list</div>}
    </Repeat>
  );
}
 ```

 #### 布尔类型、Null 以及 Undefined 将会忽略
 false, null, undefined, and true 是合法的子元素。但它们并不会被渲染。以下的 JSX 表达式渲染结果相同：
 ```
  <div />

  <div></div>

  <div>{false}</div>

  <div>{null}</div>

  <div>{undefined}</div>

  <div>{true}</div>
 ```


### 协调
#### 设计动力
  每次调用render方法的时候，react内部都会生成一个新的元素组成的树。同时会比较元素树与上一次生成的是否有什么不一样的地方，如果有不一样的会进行更新操作。

#### diff 算法
  当对比两颗元素树时，首先会比对根节点。不同类型的根节点会有不同的形态。
##### 比对不同类型的元素
  当更新后的元素类型与一开始的类型不一样的时候，那么会销毁掉原有的元素树，重新建立新的元素树。根节点下面的所有子元素也会一同销毁掉。
##### 比对相同类型的元素
  当前后的元素类型一致的时候，更新只会更新对应的props属性的值，style比较特殊一些，style只会更新内部变更的数据，并不会把所有的都更新掉。
##### 对子节点进行递归
当递归子节点的时候，会默认同时检测两个子元素的列表，当两者不一样的时候会返回一个mutation。这样的话，也就是变更的话，最好是把变化的最加到更新最下面，这样更新的开销会比较小。如果放到开头的话，那么会导致每一项都会不一样的，每次比对都会生成mutation。开销会变大很多。
```
<ul>
  <li>first</li>
  <li>second</li>
</ul>

<ul>
  <li>first</li>
  <li>second</li>
  <li>third</li>
</ul>
```
##### Keys
 为了解决上面的元素增加位置存在的问题，react添加了key属性来解决。当元素中存在key属性的时候，元素发生更新时就会对比两者的key，以便于快速找到对应的key，如果没有找到对应的key那么就会新增一个元素。
 key并不需要你在全局中是唯一的，只需要你在当前列表中是唯一的即可。
 key不建议使用index下标来标识，因为如果当元素发生移动的时候，对比会产生问题。他的key会随着变化，会增加开销。
### Refs and the DOM

#### 访问 Refs
当 ref 被传递给 render 中的元素时，对该节点的引用可以在 ref 的 current 属性中被访问。
```
const node = this.myRef.current;
```
ref 的值根据节点的类型而有所不同：
1. 当 ref 属性用于 HTML 元素时，构造函数中使用 React.createRef() 创建的 ref 接收底层 DOM 元素作为其 current 属性。
2. 当 ref 属性用于自定义 class 组件时，ref 对象接收组件的挂载实例作为其 current 属性。
3. 你不能在函数组件上使用 ref 属性，因为他们没有实例。

### Render Props
render prop 是一个用于告知组件需要渲染什么内容的函数 prop。
```
class Cat extends React.Component {
  render() {
    const mouse = this.props.mouse;
    return (
      <img src="/cat.jpg" style={{ position: 'absolute', left: mouse.x, top: mouse.y }} />
    );
  }
}

class Mouse extends React.Component {
  constructor(props) {
    super(props);
    this.handleMouseMove = this.handleMouseMove.bind(this);
    this.state = { x: 0, y: 0 };
  }

  handleMouseMove(event) {
    this.setState({
      x: event.clientX,
      y: event.clientY
    });
  }

  render() {
    return (
      <div style={{ height: '100vh' }} onMouseMove={this.handleMouseMove}>

        {/*
          Instead of providing a static representation of what <Mouse> renders,
          use the `render` prop to dynamically determine what to render.
        */}
        {this.props.render(this.state)}
      </div>
    );
  }
}

class MouseTracker extends React.Component {
  render() {
    return (
      <div>
        <h1>移动鼠标!</h1>
        <Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
      </div>
    );
  }
}
```
这个和函数式组件中，子给父传值的时候是通过回调函数执行的，那么可以在回调的同时渲染组件来模拟实现render props
```
import styles from './index.less';

export default function IndexPage() {
  const test=(x,y)=>{
    return <p>{x,y}</p>
  }
  return (
    <div renderParent={test}>
      <h1 className={styles.title}>Page index</h1>
      {test()}
    </div>
  );
}
```


### 使用 PropTypes 进行类型检查
现有的项目可以使用ts interface来替代。

### 非受控组件

受控组件、非受控组将，常见于form表单中，所有的数据控制交于表单控制的组件称之为非受控组件。如果一个组件，赋值以及onchange方法是由程序控制的话，那么这个组件就是受控组件。
经典的非受控组件就是 input file组件，这个只能由用户进行控制。




 