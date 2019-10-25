---
title: React 项目性能优化
date: 2018-10-11
categories: web 前端
tags:
- React
- Route
- Immutable
- require.ensure
---
随着 react 项目的不断扩大，为了提升用户的体验性，对前端工程进行性能优化是必要的，其中如果项目中使用了路由，可以考虑使用路由的按需加载，进行 webpack 配置，提升首次加载的速度...
<!-- more -->

## 一、避免重复渲染

* 描述：

    shouldComponentUpdate函数 返回Boolean类型的值，默认返回true，判断组件更新与否，
    
    可以通过重写对应组件的生命周期函数shouldComponentUpdate来提升速度（重新渲染过程开始前触发）。

* 前提：

    在已知某些情况下不需要更新该组件，可以在shouldComponentUpdate内返回false来跳过整个渲染进程（包括对该组件及之后的内容调用render()指令）。

* 写法：
    ```
    shouldComponentUpdate(nextProps, nextState) {
        for (let prop in nextProps) {
            if (nextProps[prop] !== this.props[prop]) {
                return true;
            }
        }
        for (let prop in nextState) {
            if (nextState[prop] !== this.state[prop]) {
                console.log(
                   'nextState[prop] !== this.state[prop]: ' +
                    nextState[prop] !== this.state[prop]
                );
                return true;
            }
        }
        return false;
    }
    ```
* 优点：

    避免组件重新渲染，在未更改组件数据的情况下可以不更新该组件

* 缺点：维护比较困难

    1、每新增state或props都需要在相应组件中的shouldComponentUpdate 方法中添加判断新旧值是否发生变化
    2、在判断传入的props值是否变化时，需指定相应的props变量，若仅判断this.props与nextProps，即使props值并未改变，shouldComponentUpdate也会判断其发生了改变。nextProps == this.props，它总返回 false 因为它们是堆中内存不同的两个对象。
    3、遇到使用值可能会突变的属性或状态(详情见React.PureComponent)

## 二、React.PureComponent

* 描述：

    PureComponent 属于浅比较(shadow comparison)，浅比较会忽略属性或状态突变的情况，改变组件内部的props或者state，
    
    它将不会发挥作用，但是如果state或props指向同一个对象，即使值发生了改变也不会产生重新渲染，除非修改其引用的对象地址。
    
    原理就是重写shouldComponentUpdate方法

* 问题：

    PureComponent将会在this.props.words的新旧值之间做一个简单的比较。
    
    由于代码中words数组在父组件中的handleClick方法中被改变了，尽管数组中的实际单词已经改变，
    
    this.props.words的新旧值还是相等的，因此即便子组件中具有应该被渲染的新单词，它还是不会更新。
    
    因为老的数组push一个words，然后赋值给新的数组。再拿老的数组和新数组比较。比较结果必然相等啊(两个相同地址的对象必然相等)。
    
    所以只能判断为相等，不能使组件更新

* 解决：避免使用值可能会突变的属性或状态

    1、例如：使用ES6的spread语法或者Object.assign
    
        [...words, 'world'] || Object.assign({}, words, 'world')

    2、Immutable.js，通过结构共享提供不可突变的，持久的集合：

    不可突变:一旦创建，集合就不能在另一个时间点改变。

    持久性:可以使用原始集合和一个突变来创建新的集合。原始集合在新集合创建后仍然可用。

    结构共享:新集合尽可能多的使用原始集合的结构来创建，以便将复制操作降至最少从而提升性能。

    使用：
    ```javascript
        import { List } from 'immutable';
        handleClick = () => {
            let todo = this.todo.value;
            let todos = List(this.state.todos);//将老的对象变为immutable对象
            todos = todos.push(todo);
            this.setState({ todos });
        }
    ```

* 场景：使用PureComponent的最佳情况就是展示组件，它既没有子组件，也没有依赖应用的全局状态

## 三、Immutable

* 实则解决了一个问题：

    Shared mutable state is the root of all evil（共享的可变状态是万恶之源）
    
* React性能优化时最常用的就是shouldComponentUpdate 方法
    
    但是他默认返回 true，即始终会执行 render() 方法，然后需要做 Virtual DOM 比较，并得出是否需要做真实 DOM 更新，这里往往会带来很多无必要的渲染并成为性能瓶颈。
    
    当然我们也可以在 shouldComponentUpdate() 中使用使用 deepCopy 和 deepCompare 来避免无必要的 render()，但 深拷贝 和 深比较 一般都是非常耗性能的。

* Immutable 则提供了简洁高效的判断数据是否变化的方法，只需 === 和 is 比较就能知道是否需要执行 render()，而这个操作几乎 0 成本，所以可以极大提高性能。修改后的 shouldComponentUpdate 是这样的：
```
import { is } from 'immutable';

shouldComponentUpdate: (nextProps = {}, nextState = {}) => {
    const thisProps = this.props || {}, thisState = this.state || {};
    
    if (Object.keys(thisProps).length !== Object.keys(nextProps).length ||
        Object.keys(thisState).length !== Object.keys(nextState).length) {
        return true;
    }
    
    for (const key in nextProps) {
        if (!is(thisProps[key], nextProps[key])) {
            return true;
        }
    }
    
    for (const key in nextState) {
        if (thisState[key] !== nextState[key] || !is(thisState[key], nextState[key])) {
            return true;
        }
    }
    return false;
}
```

* 注意：

    1、React 中规定 state 和 props 只能是一个普通对象，所以比较时要比较对象的 key;

    2、Immutable 可以给应用代打极大的性能提升，但是否使用还得看项目情况，由于入侵性较强，新项目引入比较容易，

    老项目迁移需要谨慎的评估迁移成本，对于一些提供给外部使用的公共组件，
    最好不要把Immutable对象直接暴露在对外的接口中。

    3、与 Redux 搭配使用

    Redux 是目前流行的 Flux 衍生库。它简化了 Flux 中多个 Store 的概念，只有一个 Store，
    数据操作通过 Reducer 中实现；同时它提供更简洁和清晰的单向数据流
    (View -> Action -> Middleware -> Reducer)，也更易于开发同构应用。
    目前已经在我们项目中大规模使用。
    由于 Redux 中内置的 combineReducers 和 reducer 中的 initialState 都为原生的 Object 对象，
    所以不能和 Immutable 原生搭配使用。幸运的是，Redux 并不排斥使用 Immutable，
    可以自己重写 combineReducers 或使用 redux-immutablejs来提供支持。

* 参考网址：

[facebook](https://facebook.github.io/immutable-js/docs/#/)

[manster](https://www.manster.me/?p=656)

[react.docschina](https://react.docschina.org/docs/optimizing-performance.html#%E6%A1%88%E4%BE%8B)

[segmentfault](https://segmentfault.com/a/1190000007811296)

[掘金](https://juejin.im/post/5ac34810f265da23870f0748)

## 组件按需加载

### 一、angular-router

angular-router配合懒加载实现路由的按需加载，模块划分，从根模块中分离出来，减轻根模块负重
```javascript
angular.module('app', ['ngRoute', 'oc.lazyLoad'])
.config(['$routeProvider', function($routeProvider) {
    $routeProvider
        .when('/user', {
            templateUrl: 'page1.html',
            resolve: ['$ocLazyLoad', function($ocLazyLoad) {
                    return $ocLazyLoad.load('js/page1.js'); // 按需加载目标 js file
                }]
        })
        .when('/people', {
            templateUrl: 'page2.html',
            resolve: ['$ocLazyLoad', function($ocLazyLoad) {
                    return $ocLazyLoad.load('js/page2.js'); // 按需加载目标 js file
                }]
        })
        .otherwise({
            redirectTo: '/'
        });
}]);
```

### 二、vue-router

vue-router懒加载，即路由的按需加载

第一种 按需加载方式
```javascript
const Home = () => import('../pages/home.vue')
{
    path: '/_home',
    component: Home
    // 2、另外一种按需加载的方式
    // component: resolve => require(['../pages/home.vue'], resolve)
}
```
    
###  react 组件按需加载

* (一)、在使用webpack require.ensure([], function(require) {}, path)
    
我们看到react-router的按需加载，作用主要表现在可以减少首页请求的文件的大小。 
注意事项：
    1、require(‘components/Index’).default中require方法的参数不能使用变量，只能使用字符串！
    2、如果你的组件是使用es5的module.exports导出的话，那么只需要require(‘components/Index’)即可。
        而如果你的组件是使用es6的export default导出的话，那么需要加上default！
        例如：require('components/Index').default
    3、如果在路由页面使用了按需加载（require.ensure）加载路由级组件的方式，那么在其他地方（包括本页面）就不要再import了，
        否则不会打包生成chunk文件。
        简而言之，需要按需加载的路由级组件require('components/Index').default中require方法的参数不能使用变量，
        只能使用字符串！也不能直接将require('components/Index').default当成一个变量传入，
        否则就直接加载组件了，起不到按需加载的作用必须在路由页面进行加载。
    
    特别是第3点，因为这个原因导致按需加载失败，特别注意import不能在任何地方引入按需加载的组件
    代码如下：
        /*
        * require.ensure(dependencies: String[], callback: function(require), chunkName: String)\
        */
        <IndexRoute getComponent = {(location, cb) => {
            require.ensure([], require => {
                cb(null, require('../components/Welcome').default)
            },'Welcome');
        }} />

* (二)、react-router 4.x 中的按需加载
方式一：
```
Bundle文件：
import { Component } from 'react';

export default class Bundle extends Component {
    constructor(props) {
        super(props);
        this.state = {
            mod: null
        };
    }

    componentWillMount() {
        this.load(this.props)
    }

    componentWillReceiveProps(nextProps) {
        if (nextProps.load !== this.props.load) {
            this.load(nextProps)
        }
    }

    load(props) {
        this.setState({
            mod: null
        });
        //注意这里，使用Promise对象; mod.default导出默认
        props.load().then((mod) => {
            this.setState({
                mod: mod.default ? mod.default : mod
            });
        });
    }

    render() {
        return this.state.mod ? this.props.children(this.state.mod) : null;
    }
}

路由文件：
import Bundle from '../Bundle';

// 加载组件 公共函数
function BundleComponent (ComItem) {
    return (props) => (
        <Bundle load={() => import('../components/' + ComItem)}>
            {(ComItem) => <ComItem {...props}/>}
        </Bundle>
    );
}

<Route exact path="/" component={ BundleComponent('HomePage') } /> {/* exact 严格匹配'/'路由 */}
<Route path="/inbox" component={BundleComponent('Inbox')} />
```

方式二：
```
asyncComponent文件：
import React, { Component } from "react";

export default function asyncComponent(importComponent) {
    class AsyncComponent extends Component {
        constructor(props) {
            super(props);

            this.state = {
                component: null
            };
        }

        async componentDidMount() {
            const { default: component } = await importComponent();

            this.setState({
                component: component
            });
        }

        render() {
            const C = this.state.component;
            return C ? <C {...this.props} /> : null;
        }
    }

    return AsyncComponent;
}

路由文件：
import asyncComponent from '../components/AsyncComponent';
const AsyncHomePage = asyncComponent(() => import('../components/HomePage'));
const routes = [
    {
        name: 'Inbox',
        path: '/inbox'
    },
    ...
]

<Route exact path="/" component={AsyncHomePage} /> {/* exact 严格匹配'/'路由 */}
{
    routes.map((item, index) => {
        return (
            <Route
                path={ item.path }
                component={asyncComponent(() => import('../components/' + item.name))} key={ index }
            />
        )
    })
}

```
    
这个asyncComponent 函数接受一个importComponent 的参数，importComponent 调用时候将动态引入给定的组件。

在componentDidMount 我们只是简单地调用importComponent 函数，并将动态加载的组件保存在状态中。

最后，如果完成渲染，我们有条件地提供组件。在这里我们如果不写null的话，也可提供一个Loading图，代表着组件正在渲染。
   
## 优化思路

* 构建优化
    - 1、减少编译体积 ContextReplacementPugin、IgnorePlugin、babel-plugin-import、babel-plugin-transform-runtime。
    - 2、并行编译 happypack、thread-loader、uglifyjsWebpackPlugin开启并行
    - 3、缓存 cache-loader、hard-source-webpack-plugin、uglifyjsWebpackPlugin开启缓存、babel-loader开启缓存
    - 4、预编译 dllWebpackPlugin && DllReferencePlugin、auto-dll-webapck-plugin

* 性能优化
    - 1、减少编译体积 Tree-shaking、Scope Hositing。
    - 2、hash缓存 webpack-md5-plugin
    - 3、拆包 splitChunksPlugin、import()、require.ensure

## 参考网址：

[CSDN](https://blog.csdn.net/liwusen/article/details/53944321)
        
[webpack1.13.3以下版本github](https://github.com/love-fay/fay-webpack-redux-code-splitting/tree/master/react)
        
[不同webpack版本对应链接](https://blog.csdn.net/valada/article/details/79909786)
