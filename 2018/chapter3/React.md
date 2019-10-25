# React

[React 小书](http://huziketang.mangojuice.top/books/react/lesson9)

## React 生命周期函数
  组件的生命周期分成三个状态

* 生命周期

    Mounting 已插入真实 DOM

    Updating 正在被重新渲染

    Unmounting 已移出真实 DOM

* 生命周期钩子

    React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理函数。

    componentWillMount()

    componentDidMount()

    componentWillUpdate(object nextProps, object nextState)

    componentDidUpdate(object prevProps, object prevState)

    componentWillUnmount()

* 此外，React 还提供两种特殊状态的处理函数

    componentWillReceiveProps(object nextProps) 已加载组件收到新的参数时调用

    shouldComponentUpdate(object nextProps, object nextState) 组件判断是否重新渲染时调用

* 总结

    组件的数据来源，通常是通过 Ajax 请求从服务器获取，可以使用 componentDidMount 方法设置 Ajax 请求，等到请求成功，再用 this.setState 方法重新渲染 UI

    componentWillReceiveProps 在组件接收到一个新的 prop (更新后)时被调用。这个方法在初始化 render 时不会被调用，但在组件内的 props/redux 数据发生变化时执行，可用在异步请求数据时，若数据未及时请求到，可在这里获取值或者赋值。

    但是，需要注意的是，如果传递到组件中的某个 props 是通过当前组件中的 state 传递的，需要注意当前状态或属性被重复加载。此时需要加强该属性的执行条件，例如：
    ```javascript
     if (!!nextProps.type && (nextProps.type != this.props.type)) {
          this.setState({
            type:nextProps.type
          })
          this.init(nextProps.type);
      }
    ```

* 父子组件

    父组件 `constructor` -> `componentWillMount` -> `render` -> `componentDidMount` -> 子组件 `constructor` -> `componentWillMount` -> `componentDidMount` -> `render` -> `componentWillUnmount`

    如父组件 `render`,此时子组件满足条件即 `render`

* 生命周期图解

    ![](../../assets/life cycle.png)

* 参考网址：

    [React 中文](https://react.docschina.org/docs/state-and-lifecycle.html)

## Redux 状态管理

state 是只读的， 唯一改变 state 的方法就是触发 action，action 是一个用于描述已发生事件的普通对象。

* redux 组成有 5 个

    createStore 创建仓库，接受 reducer 作为参数

    bindActionCreator 绑定 store.dispatch 和 action 的关系

    combineReducers 合并多个 reducers

    applyMiddleware  洋葱模型的中间件,介于 dispatch 和 action 之间，重写 dispatch

    compose  整合多个中间件

    存在 redux 中的数据会暂时存起来，通过 props 传给子组件存在state中，修改子组件的 state后，在进入该页面时，即使已经请求到数据，仍然会使用之前存下来的状态数据，需要清数据或者在

* reducer 返回一个全新的对象

形式为 (state, action) => state 的纯函数。描述了 action 如何把 state 转变成下一个 state。state 的形式取决于你，可以是基本类型、数组、对象、甚至是 Immutable.js 生成的数据结构。惟一的要点是当 state 变化时需要返回全新的对象，而不是修改传入的参数。

* 在 redux 中存储数据，需先定义变量进行赋值，保证后面返回的是全新的 state

    ```
    export default function statisticReducer(state = initState, action) {
        switch (action.type) {
            case 'updateFinanceQueryOptions':
                let newState = JSON.parse(JSON.stringify(state));
                newState.queryOptions[action.data.key] = action.data['value']
                return newState;
            default:
                return state;
        }
    }
    ```

* mapStateToProps 和 mapDispatchToProps

```javascript
import React from 'react';
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux';
import * as actions from './redux';

class About extends React.Component {}

const mapStateToProps = state => {
	return state.about
}

const mapDispatchToProps = dispatch => {
	return {
		actions: bindActionCreators(actions, dispatch)
	}
}
const AboutConnect = connect(
	mapStateToProps,
	mapDispatchToProps
)(About);

export default AboutConnect;
```

`mapStateToProps` 用于建立 `store` 与 `state` 之间的映射关系，其返回了保存在 `store` 中，且用不同 `reducer` 定义的 `state` 变量值，可在本页面中进行提取使用。`mapStateToProps` 可以不传，如果不传，组件不会监听 `store` 的变化，也就是说 `Store` 的更新不会引起 UI 的更新 

`mapDispatchToProps` 用于建立组件跟 store.dispatch 的映射关系,可以是一个 object，也可以传入函数，如果 `mapDispatchToProps` 是一个函数，它可以传入 dispatch, ownProps, 定义 UI 组件如何发出 action，实际上就是要调用 dispatch 这个方法。其返回了 Action Creators，方法或者函数，指定 store 中的 state 值发生变化的方法。`bindActionCreators` 中绑定了需要的 Action Creators


## React 中的条件渲染

* 有以下几种方式：

    if 语句

    三元操作符(ternary operator)

    逻辑 && 操作符

    switch.. case.. 语句

    枚举(enums)

    多层条件渲染(multi-level conditional reandering)
    
    使用高阶组件

* 1、if语句

    ```javascript
    // 如果data不存在则不进行渲染
    if (!data) {
        return null;
    }

    // 如果 data 存在且为空，这返回对应反馈
    // if (!data.length) {
    //     return <p>data is empty</p>
    // } else {
        return (
            <ul>
                data.map(function(item, index) {
                    return (
                        <li key={index}>{item}</li>
                    )
                })
            </ul>
        )
    // }
    ```

    注意：一个组件如果return null,将不会被渲染出来。如果你想给用户一些反馈，使用if...else

* 2、三元操作符

    ```javascript
    return (
        <div>
            { isEmpty
                ? 'This is empty!'
                : {data}
            }
        </div>
    )
    ```

    注意： 使用三元运算符来代替上面的if…else… 条件渲染，三元运算符使条件渲染更加的简洁，内联中使用。

* 3、逻辑 && 操作符

    ```javascript
    return (
        <div>
            { isLoading && <div>Loading。。。</div> }
        </div>
    )
    ```
    注意：如果当前不符合条件不进行渲染时，使用逻辑 && 操作符更加简洁。

* 4、switch.. case.. 语句

    ```javascript
    function Notification({ text, state }) {
        switch (state) {
            case 'info':
                return <Info text={text} />;
                break;
            case 'warning':
                return <Warning text={text} />;
                break;
            case 'info':
                return <Info text={text} />;
                break;
            default:
                return null;
                break;
        }
    }
    ```

    另外一种将条件渲染结果内联在switch…case中的方法是，使用立即调用函数：
    ```javascript
    function Notification({ text, state }) {
        return (
            <div>
                {
                    (() => {
                        switch (state) {
                            case 'info':
                                return <Info text={text} />;
                                break;
                            case 'warning':
                                return <Warning text={text} />;
                                break;
                            case 'info':
                                return <Info text={text} />;
                                break;
                            default:
                                return null;
                                break;
                        }
                })()
                }
            </div>  
        );
    }
    ```

* 注意：
    Notification 组件根据输入状态可能渲染Error,Warning,Info三种不同的组件。使用switch…case进行多种条件渲染。

    switch…case语句永远要加上default情况，因为React组件要么返回元素，要么返回null。

    如果组件依据 state 属性 渲染时，最后添加上 React.PropTypes,即上面的组件后面添加：

    ```javascript
    Notification.propTypes = {
        text: React.PropTypes.string,
        state: React.PropTypes.oneOf(['info', 'warning', 'error'])
    }
        ```

* 5、枚举(enums)
    - 在javascript中，对象的键值对可以用作枚举：

    ```javascript
    const ENUM = {
        a: '1',
        b: '2',
        c: '3'
    };
    ```

    - 枚举是多种条件渲染中很好的一种方式，上面的 Notification 组件可以写为：

    ```javascript
    function Notification({ text, state }) {
        return (
            <div>
                {
                    {
                        info: <Info text={text} />,
                        warning: <Warning text={text} />,
                        error: <Error text={text} />
                    }[state]
                }
            </div>
        )
    }
    ```

    上面的 state 属性用于取回对象中的值，这比switch…case可读性更强。

    - 因为对象的值依赖 text 属性，所以我们必须使用内联对象。如果我们不需要text属性，我们可以使用外部静态枚举：

    ```javascript
    const NOTIFICATION_STATES = {
        info: <Infor />,
        warning: <Warning />,
        error: <Error />,
    };
    
    function Notification({ state }) {
        return (
            <div>
                {NOTIFICATION_STATES[state]}
            </div>
        )
    }
    ```

    - 如果我们需要text属性，我们可以使用函数取回对象的值

    ```javascript
    const getSpecificNotification = (text) => ({
        info: <Info text={text} />,
        warning: <Warning text={text} />,
        error: <Error text={text} />,
    });
    
    function Notification({ state, text }) {
        return (
            <div>
                {
                    getSpecificNotification(text)[state]
                }
            </div>  
        )
    }
    ```

    - 对象用作枚举，使用场景很灵活，下面例子：


        function FooBarOrFooOrBar({ isFoo, isBar }) {

            const key = `${isFoo}-${isBar}`;
            
            return (
                <div>
                    {
                        {
                            ['true-true']: <FooBar />,
                            ['true-false']: <Foo />,
                            ['false-true']: <Bar />,
                            ['false-false']: null,
                        }[key]
                    }
                </div>
            );
        }
        FooBarOrFooOrBar.propTypes = {
            isFoo: React.PropTypes.boolean.isRequired,
            isBar: React.PropTypes.boolean.isRequired
        }

        注意： 多种条件渲染最好的方式是枚举。

* 6、多层条件渲染
    嵌套条件选择渲染，参考一下例子：
    
    List组件可能显示一个list,或者一个empty text提示，或者什么都没有。
    ```javascript
    function List({ list }) {
        const isNull = !list;
        const isEmpty = !isNull && !list.length;
        
        return (
            <div>
            { isNull
                ? null
                : ( isEmpty
                    ? <p>Sorry, the list is empty</p>
                    : <div>{list.map(item => <ListItem item={item} />)}</div>
                )
            }
            </div>
        );
    }
    ```
 
    ```javascript
    // 实例
    <List list={null} /> // <div></div>
    <List list={[]} /> // <div><p>Sorry, the list is empty</p></div>
    <List list={['a', 'b', 'c']} /> // <div><div>a</div><div>b</div><div>c</div></div>
    ```

    但是最好保持嵌套的层数最小化，这样代码可读性更强。可以将组件划分成更小的组件的方式
    
    ```javascript
    function List({ list }) {
        const isList = list && list.length;
        
        return (
            <div>
                { isList
                    ? <div>{list.map(item => <ListItem item={item} />)}</div>
                    : <NoList isNull={!list} isEmpty={list && !list.length} />
                }
            </div>
        );
    }
 
    function NoList({ isNull, isEmpty }) {
        return (!isNull && isEmpty) && <p>Sorry, the list is empty</p>;
    }
    ```

* 7、使用高阶组件
    HOCs 在React中很适合条件渲染。HOCs的一种使用方式就是改变组件的外观。

    例如使用高阶组件来展示一个加载显示器或者一个想要的组件：

    ```javascript
    function withLoadingIndicator(Component) {
        return function EnhancedComponent({ isLoading, ...props }) {
            if (!isLoading) {
                return <Component { ...props } />;
            }
        
            return <div><p>Loading...</p></div>;
        }
    }
    
    // 使用
    const ListWithLoadingIndicator = withLoadingIndicator(List);
    
    <ListWithLoadingIndicator
        isLoading={props.isLoading}
        list={props.list}
    />
    ```

    这个示例中，List组件能关注渲染list上，而不必担心loading状态，另外，HOCs可以屏蔽list为null或empty的情况。
        
* 总结

    if-else

        最简单的条件渲染 
        适用于新手 
        使用if,从渲染方法中返回null提前退出函数

    三元操作符 

        比if-else更好，优先使用 
        比if-else更加简洁 
        逻辑 ‘&&’ 操作符 
        不返回元素就返回null时使用

    switch…case 

        比较冗长 
        可以通过立即调用函数内联使用 
        使用枚举的方式代替这种方式更好

    枚举(enums) 

        使用于不同的状态使用 
        超过一种条件选择时使用

    多层次条件选择渲染

        避免使用这种方式对可读性的影响 
        将组件划分为更小的组件，小组件自身带有条件选择 
        偏向于使用高阶组件(HOCs) 

    HOCs

        使用高阶组件而屏蔽条件渲染 
        组件能关注主要的逻辑目的

## React 存储数据至localStorage -- store.js

* 作用：
    本地存储localstorage的封装，提供简单的API，没有依赖，压缩只有 2.48kb(gzipped: 1.07kb);

* 安装：
    npm install storejs

* 使用：
    store('id', text); // 向localStorage中存储id数据
    store('id'); // 获取localStorage中的id数据

## setState -- '异步'

### 过程分析

* 生命周期函数中 -- setState

```javascript
import React from 'react';

class SetState extends React.Component {

    state = { val: 0 }

    componentDidMount() {
        this.setState({ val: this.state.val + 1 })
       console.log(this.state.val) // 输出的还是更新前的值 --> 0
    }
    
    increment = () => {
        this.setState({ val: this.state.val + 1 })
        console.log(this.state.val) // 输出的是更新前的val --> 0
    }

    render() {
        return (
            <div onClick={this.increment}>
                {`Counter is: ${this.state.val}`}
            </div>
        )
    }
}

export default SetState;
```

* 原生事件中 -- setState

```javascript
import React from 'react';

class SetState extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            val: 0
        }
    }

    componentDidMount() {
       document.body.addEventListener('click', this.changeValue, false)
    }

    changeValue = () => {
        this.setState({ val: this.state.val + 1 })
        console.log(this.state.val) // 输出的是更新后的值 --> 1
    }

    render() {
        return (
            <div>
                {`Counter is: ${this.state.val}`}
            </div>
        )
    }
}

export default SetState;
```

* setTimeout 中 -- setState

```javascript
import React from 'react';

class SetState extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            val: 0
        }
    }

    componentDidMount() {
        setTimeout(_ => {
            console.log('执行->第二步', this.state.val) // 输出更新前的值 --> 0
            this.setState({ val: this.state.val + 1 })
            console.log('执行->第三步', this.state.val) // 输出更新后的值 --> 1
        }, 0);
        console.log('执行->第一步', this.state.val) // 输出更新前的值 --> 0
    }

    render() {
        return (
            <div>
                {`Counter is: ${this.state.val}`}
            </div>
        )
    }
}

export default SetState;
```

* setState 批量更新

```javascript
import React from 'react';

class SetState extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            val: 0
        }
    }

    batchUpdates = () => {
        this.setState({ val: this.state.val + 1 })
        this.setState({ val: this.state.val + 1 })
        this.setState({ val: this.state.val + 1 })
    }

    render() {
        return (
            <div onClick={this.batchUpdates}>
                {`Counter is: ${this.state.val}`}
            </div>
        )
    }
}

export default SetState;
```

* 钩子函数 及 setTimmout 中 setState 的执行结果

```javascript
import React from 'react';

class SetState extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            val: 0
        }
    }

    // 钩子函数中的 setState 无法立马拿到更新后的值;
    // setState 批量更新的策略;
    // setTimmout 中 setState 是可以同步拿到更新结果
    componentDidMount() {
        this.setState({ val: this.state.val + 1 })
        console.log('第一步：', this.state.val); // 0
    
        this.setState({ val: this.state.val + 1 })
        console.log('第二步：', this.state.val); // 0
    
        setTimeout(_ => {
            this.setState({ val: this.state.val + 1 })
            console.log('第三步：', this.state.val); // 2
        
            this.setState({ val: this.state.val + 1 })
            console.log('第四步：', this.state.val); //  3
        }, 0)
    }

    render() {
        return (
            <div>
                {`Counter is: ${this.state.val}`}
            </div>
        )
    }
}

export default SetState;
```

### setState 第二个参数是一个回调函数

```javascript
this.setState(
    {
        data: newData
    },
    () => {
        console.log('最新的 state 值', me.state.data)
    }
);
```

### 总结

在 react 中使用 this.setState({}) 时，发现状态值并没有及时更改，无法立马拿到更新后的值，那是因为：

* setState 只在合成事件和钩子函数中是“异步”的，在原生事件和 setTimeout 中都是同步的。

* setState 的 "异步" 并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形式了所谓的 "异步"，当然可以通过第二个参数 setState(partialState, callback) 中的 callback 拿到更新后的结果。

* setState 的批量更新优化也是建立在 "异步"（合成事件、钩子函数）之上的，在原生事件和 setTimeout 中不会批量更新，在 "异步" 中如果对同一个值进行多次 setState ， setState 的批量更新策略会对其进行覆盖，取最后一次的执行，如果是同时 setState 多个不同的值，在更新时会对其进行合并批量更新。

### npm install 报错解决

```
npm ERR! Unexpected end of JSON input while parsing near '...5":{"name":"immutable'
```

解决方案：
```
npm cache clean --force
npm set registry https://registry.npmjs.org/
```

### 参考

[掘金之 setState](https://juejin.im/post/5b45c57c51882519790c7441)


## React @16.4 版本

### getDerivedStateFromProps

* React @16.4 版本中新增了 getDerivedStateFromProps

他与 `componentWillReceiveProps` 类型，都是在 `props` || `state` 发生变化的时候执行，

```js
static getDerivedStateFromProps(props, state, context) {
    if ( props.value !== state.value ) {
        // 它会改变state状态，然后再次调用 getDerivedStateFromProps，再次改变 state 状态...
        state.myFetch(props.value)
        return {
            value: props.value
        };
    }
    return null;
}

myFetch() {
    this.setState({
        list: "newList"
    })
}
```

以上写法会导致 多次循环渲染直到报错，可以在 `componentDidUpdate` 中更新

```js
// 纯函数，无副作用
static getDerivedStateFromProps(props, state) {
    if ( props.value !== state.value ) {
        return {
            value: props.value,
            list: null
        };
    }
    return null;
}

componentDidUpdate() {
    if (this.props.value != this.state.value) {
        this.myFetch(this.props.value);
    }
}

myFetch() {
    this.setState({
        list: "newList"
    })
}
```

* `getDerivedStateFromProps` 中如何调用方法

    antd Form 中校验后如何动态添加值 `form.validateFields`，动态选中数据后添加至 Form 前，先 `form.resetFields`() 清空 `Form` 数据，否则会出现数据暂存，无法更新数据的情况


## Reat 中可能会出现的报错

* 在组件中的DOM 上绑定道具属性时，必需小写字母，比如绑定 `hasChildren`,必需使用 `haschildren`，否则会报以下错误：
```
index.js:1 Warning: React does not recognize the `hasChildren` prop on a DOM element. If you intentionally want it to appear in the DOM as a custom attribute, spell it as lowercase `haschildren` instead. If you accidentally passed it from a parent component, remove it from the DOM element.
```

## dangerouslySetInnerHTML

将 `React` 项目中的 `innerHTML` 转换成 `DOM` 中的 `innerHTML` 内容，需要借助使用 `__html` ，尽量减少使用 `dangerouslySetInnerHTML` 例如:
```js
function createMarkup() {
    return {__html: 'First &middot; <br /> Second'};
}
function MyComponent() {
    return <div dangerouslySetInnerHTML={createMarkup()} />;
}
```
