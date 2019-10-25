# React 项目架构

## 项目主结构

    ```
    |-- src
    | |-- assets -- 公共资产
    | | |-- less // 公共样式
    | | |-- images // 公共本地图片
    | |-- common -- 公共 UI 组件
    | |-- components -- 业务层面 公共组件
    | |-- constants -- 公共常量
    | |-- layouts -- 页面布局组件
    | | |-- LeftBar
    | | |-- Header
    | | |-- Footer
    | | |-- index.js
    | | |-- index.less
    | |-- pages -- 页面组件
    | | |-- Audit
    | | | |-- index.js
    | | | |-- index.less
    | | | |-- constants.js -- Home 内常量
    | | | |-- components -- Home 拆分组件
    | | | |-- images -- Home 内图片
    | |-- routes -- 是否进行 -- immutable 代码分割 || 按需加载
    | | |-- routeClass -- 路由分类
    | | | |-- audit.js
    | | |-- routeJson.js
    | | |-- index.js
    <!-- | |-- redux -- Reducer、Action Types、Action Creator
    | | |-- index.js -- store
    | | |-- actionTypes.js -- Action Types
    | | |-- rootReducer.js -- Reducer 汇总
    | | |-- module1.js -- Reducer、Action Creator-->
    | |-- redux
    | | |-- audit
    | | | |-- action-type.js -- Action Type
    | | | |-- action.js -- Action Creator
    | | | |-- reducer.js -- Reducer
    | | |-- rootReducer.js -- Reducer 汇总
    | | |-- store.js -- store
    | |-- utils -- 公共工具 || 方法
    | | |-- asyncComponent.jsx
    | | |-- axios.js -- axios 封装
    | |-- index.js
    ```

## 技术栈、版本

```
react -- ^16.8.4
redux -- ^4.0.1
react-redux -- ^6.0.1
react-router-dom -- ^4.3.1
redux-logger -- ^3.0.6
redux-thunk -- ^2.3.0

<!-- css 工具 -->
less -- ^3.9.0
less-loader -- ^4.1.0

antd -- ^3.15.0
moment -- ^2.24.0
axios -- ^0.18.0
webpack -- 4.28.3
immutable -- ^4.0.0-rc.12
<!-- socket.io -->
<!-- dueljs -->
<!-- react-draggable -->
<!-- tui-image-editor -->

<!-- 代码格式化 -->
eslint
prettier

<!-- socket 消息通知 -->
socket.io
<!-- DuelJS 通信 -->
dueljs

<!-- 代码分析工具 -->
webpack-bundle-analyzer -- ^3.1.0
```

## router、redux 说明

* router -- asyncComponent 代码分割 || 按需加载

路由通过集合的形式，按照业务分类
```
| |-- routes -- 是否进行 -- 代码分割 || 按需加载
| | |-- routeClass -- 路由分类
| | | |-- audit.js
| | |-- routeJson.js
| | |-- index.js
```

/utils/asyncComponent.jsx
```js
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

            this.setState({ component });
        }

        render() {
            const C = this.state.component;

            return C ? <C {...this.props} /> : null;
        }
    }

    return AsyncComponent;
}
```

/routes/index.js
```js
import React, { Component } from 'react';
import { HashRouter, Switch, Route, Redirect } from 'react-router-dom';
import asyncComponent from '@/utils/asyncComponent';

import home from "@/pages/home/home";
const AuditReadyList = asyncComponent(
    () => import(/* webpackChunkName: "AuditReadyList" */"@/pages/AuditReadyList")
);

// react-router4 不再推荐将所有路由规则放在同一个地方集中式路由，
// 子路由应该由父组件动态配置，组件在哪里匹配就在哪里渲染，更加灵活
export default class Routes extends Component {
    render() {
        return (
            <HashRouter>
                <Switch>
                    <Route path="/" exact component={home} />
                    <Route path="/audit-ready-list" component={AuditReadyList} />
                    <Redirect to="/" />
                </Switch>
            </HashRouter>
        )
    }
}
```

* redux 数据结构

    ```
    audit
        action-type.js -- Action Type
        action.js -- Action Creator
        reducer.js -- Reducer
    rootReducer.js -- Reducer 汇总
    store.js -- store
    ```

    或

    ```
    audit.js -- Reducer、Action Creator
    actionTypes.js -- Action Types
    rootReducer.js -- Reducer 汇总
    store.js -- store
    ```

* 使用 immutable -- 控制 rerender

```js
shouldComponentUpdate(nextProps, nextState) {
    return (
        !is(fromJS(this.props), fromJS(nextProps)) || !is(fromJS(this.state), fromJS(nextState))
    );
}
```

## 项目规范

* 1、代码格式化

    eslint、prettier
    
    js 中使用单引号 `''`、html 中使用双引号 `""`
    判断语句 `===` 代替 `==`
    属性命名 -- 驼峰式
    对象中键值对间 `:` 前无空，后空格

* 2、组件拆分

    一个大的业务组件，尽量差分多个子组件，组件代码简短，便于查看、理解及后续维护。可复用的组件应拆分到 src/components(小标题、导出到Excel、下拉选择器等)，否则拆分到当前路径下 components(列表页的表单与数据列表)
    UI组件

* 3、immutable 使用

    shouldComponentUpdate(nextProps, nextState) 组件判断是否重新渲染时调用，我们可以在 shouldComponentUpdate() 中使用 deepCopy 和 deepCompare 来避免无必要的 render()，但 深拷贝 和 深比较 一般都是非常耗性能的。

    immutable 提供了简洁高效的判断数据是否变化的方法，只需 === 和 is 比较就能知道是否需要执行 render()，而这个操作几乎 0 成本，所以可以极大提高性能。`推荐` 使用，可以避免不必要的、重复的组件渲染，从而提高性能。

    ```js
    shouldComponentUpdate(nextProps, nextState) {
        return (
            !is(fromJS(this.props), fromJS(nextProps))|| !is(fromJS(this.state), fromJS(nextState))
        );
    }
    ```
    
    [facebook -- immutable 介绍](https://facebook.github.io/immutable-js/docs/#/)

    在 immutable 中 `fromJS()`允许对象的更深层次的嵌套,`is()` 与 `equals()` 用来判断两个不可变集合是否相等：

    The collections in Immutable.js are intended to be nested, allowing for deep trees of data, similar to JSON.

    ```
    import { is, fromJS } from 'immutable';
    const nested = fromJS({ a: { b: { c: [ 3, 4, 5 ] } } });
    // Map { a: Map { b: Map { c: List [ 3, 4, 5 ] } } }

    // First consider:
    const obj1 = { a: 1, b: 2, c: 3 };
    const obj2 = { a: 1, b: 2, c: 3 };
    obj1 !== obj2; // two different instances are always not equal with ===

    const { Map, is } = require('immutable');
    const map1 = Map({ a: 1, b: 2, c: 3 });
    const map2 = Map({ a: 1, b: 2, c: 3 });
    map1 !== map2; // two different instances are not reference-equal
    map1.equals(map2); // but are value-equal if they have the same values
    is(map1, map2); // alternatively can use the is() function
    ```

    `fromJS(obj)` 把传入的obj封装成 `immutable` 对象，在赋值给新对象时传递的只有本身的值而不是指向内存的地址。
        obj.set(属性名，属性值)给obj增加或修改属性，但obj本身并不变化，只返回修改后的对象
        obj.get(属性名)从immutable对象中取得属性值

    优点：深拷贝/浅拷贝本身是很耗内存，而 `immutable` 本身有一套机制使内存消耗降到最低
    缺点：你多了一整套的API去学习，并且 `immutable` 提供的 `set,map`等对象容易与ES6新增的 `set,map` 对象弄混

关于 `immutable` 的其他网站：
    
    https://www.cnblogs.com/penghuwan/p/6707254.html

* 4、注释格式

    - 变量 或 参数注释 -- 类似以下说明
    ```js
    constructor(props) {
        super(props);
        this.state = {
            statejudgment: false // ***状态判断
        }
    }
    
    let roles = ''; // 角色信息数组
    ```

    - 方法 或 函数注释 -- method、return、param、Description
    ```js
    /**
     * @method 修改***信息
     * @return {Type}
     * @param {String} value 更改的值
     * @description 其他需要的详细说明(条件或场景)
    */
    handleChange(value) {
        // ...
    }

    // 保存数据
    handleClickSave() {
        // ...
    }
    ```

    - 组件注释 -- Author、Date、Modified by or time、Description
    ```js
    /*
     * @Author: wqjiao
     * @Date: 2019-03-15 13:45:30
     * @Last Modified by:   wqjiao
     * @Last Modified time: 2019-03-15 13:45:30
     * @Description: HelpCenter 帮助中心
    */
    export default class HelpCenter extends Component {
        render() {
            return (<div></div>)
        }
    }
    ```

* 5、声明多个变量

    在 js 代码中会出现声明多个变量的情况，此时可以使用一个 let/var 语句来同时声明多个变量，以减少整个脚本的执行时间，且代码格式也相对规范。

    ```js
    // bad
    let a = 1;
    let b = 2;
    let c = 3;

    // good
    let a = 1,
        b = 2,
        c = 3;
    ```

* 6、组件中使用到的 state，应保证在 `constructor` 中定义

    ```js
    // bad
    constructor(props) {
        super(props);
    }
    componentDidMount() {
        me.setState({
            name: 'wqjiao' // name 应在 constructor 中定义
        });
    }

    // good
    constructor(props) {
        super(props);
        this.state = {
            name: '' // 收件人姓名
        }
    }
    componentDidMount() {
        me.setState({
            name: 'wqjiao' // name 应在 constructor 中定义
        });
    }
    ```

* 7、一个方法或流程中能使用一次 `setState({})` 参数的，就避免使用多次 `setState({})` 设置 state

    ```js
    constructor(props) {
        super(props);
        this.state = {
            a: 1,
            b: 2
        }
    }

    // bad
    handleClick(isShow, title, data) {
        let me = this;
        let newState = Object.assign(me.state, {
            a: data.a,
            b: data.b,
            c: data.c
        });

        // 1
        me.setState(newState);

        // 2
        me.setState({
            visible: isShow
        });

        // 3
        if (title) {
            me.setState({title});
        }
    }

    // good
    handleClick(isShow, title, data) {
        let me = this,
            newState = Object.assign(me.state, {
                a: data.a,
                b: data.b,
                c: data.c
            });

        me.setState({
            ...newState,
            visible: isShow,
            title: title || me.state.title
        });
    }
    ```

* 8、尽量减少使用 refs

    采用回调或其他方式代替，避免操作时组件 `没有实例化` 或 `undefined`，页面报错

* 9、项目中的路由比较多时，按照代码拆分 `asyncComponent()` 按需加载组件，路由引入可按照业务分类

* 10、在非业务组件中避免使用 redux(`mapStateToProps`、`mapDispatchToProps`) 通过 `props` 传递数据

* 11、$.extend() 和 Object.assign() 复制对象到目标对象

    - $.extend() 是 jQuery 的方法
    - Object.assign(target, sources) 原生 javascript 方法
        target 目标对象
        sources 源对象
        返回值 -- 目标对象
    - ES6 语法，扩展语法
