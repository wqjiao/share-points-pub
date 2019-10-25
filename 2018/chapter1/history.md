---
title: Route history
date: 2018-10-11
categories: web 前端
tags: React Route
---
在开发 react 工程的过程中，使用到了 Route，其中 Route history 包含三种方式，这篇文章讲述 Route history 三种方式的不通点及各自的特点...
<!-- more -->


## URL 组成
* protocol://hostname[:port]/path/[;parameter][?query]#fragment
    
    protocol 协议，常用的协议是http
    
    hostname 主机地址，可以是域名，也可以是IP地址
    
    port 端口 http协议默认端口是：80端口，如果不写默认就是:80端口
    
    path 路径 网络资源在服务器中的指定路径
    
    parameter 参数 如果要向服务器传入参数，在这部分输入
    
    query 查询字符串 如果需要从服务器那里查询内容，在这里编辑
    
    fragment 片段 网页中可能会分为不同的片段，如果想访问网页后直接到达指定位置，可以在这部分设置

## Route history 常用的三种形式来实现路由

browserHistory、hashHistory、createMemoryHistory

```
import { browserHistory } from 'react-router'
render(
    <Router history={browserHistory} routes={routes} />,
    document.getElementById('app')
)
```

* 1、Browser history 

    使用 React Router 的应用推荐的 history。
    
    它使用浏览器中的 History API 用于处理 URL，创建一个像example.com/some/path这样真实的 URL。
    
    需要server端支持，并向server发送request。

    服务器需要做好处理 URL 的准备。
    
    处理应用启动最初的 / 这样的请求应该没问题，但当用户来回跳转并在 /accounts/123 刷新时，
    
    服务器就会收到来自 /accounts/123 的请求，这时你需要处理这个 URL 并在响应中包含 JavaScript 应用代码。

* 2、Hash history 
    
    使用 URL 中的 hash（#）部分去创建形如 example.com/#/some/path 的路由。

    不需要想server发送request，根据url渲染相应的模块。

    在 DOM API 中，这些 hash history 通过 window.location.hash = newHash 很简单地被用于跳转，且不用存储它们的location state。
    
    但我们想全部的 history 都能够使用location state，因此我们要为每一个 location 创建一个唯一的 key，
    
    并把它们的状态存储在 session storage 中。当访客点击“后退”和“前进”时，我们就会有一个机制去恢复这些 location state

    路由跳转时，传递的 data 数据会临时存在 session storage 中，当点击 前进或后退时，该数据会被清空
    ```
    <!-- 切换路由 -->
    hashHistory.push ({
        pathname: '/after/audit-view',
        state: data
    });

    <!-- 返回上一页 -->
    hashHistory.goBack()
    ```

* 3、createMemoryHistory
    
    Memory history 不会在地址栏被操作或读取。
    
    这就解释了我们是如何实现服务器渲染的。同时它也非常适合测试和其他的渲染环境（像 React Native ）。
    
    和另外两种history的一点不同是你必须创建它，这种方式便于测试。
    ```
    const history = createMemoryHistory(location);
    ```
* 注意： 

    官方推荐使用browserHistory

    使用browserHistory,浏览器的url是这样的：/user/liuna

    使用hashHistory,浏览器的url是这样的：/#/user/liuna?_k=adseis
    
    使用createMemoryHistory,浏览器的url是这样的：/

    这样看起来当然是browerHistory更好一些，但是它需要server端支持。

    使用hashHistory时，因为有 # 的存在，浏览器不会发送request,react-router 自己根据 url 去 render 相应的模块。

    使用browserHistory时，从 / 到 /user/liuna, 浏览器会向server发送request，所以server要做特殊请求，比如用的 express 的话，
    
    你需要 handle 所有的路由 app.get('*', (req, res) => { ... })，使用了 nginx 的话，nginx也要做相应的配置。

    react-router-dom v4 版本已经做了以下修改：
    ```
    browserHistory 替换成 BrowserRouter
    hashHistory 替换成 HashRouter
    createMemoryHistory 替换成MemoryRouter
    ```

* 参考网址：
[React 导航](http://react-guide.github.io/react-router-cn/docs/API.html)
[cnblogs](https://www.cnblogs.com/dudeyouth/p/6617059.html)

## 清空react-router（hashHistory）产生的多余的session
``` 
    export default function() {
        try {
            let sessionLength = window.sessionStorage.length;
            let sessionKeys = [];
            // 遍历key中包含@@History的项
            for(let i = 0; i < sessionLength; i ++) {
                if (window.sessionStorage.key(i).match('@@History')) {
                    sessionKeys.push(window.sessionStorage.key(i));
                }
            }
            // 删除该项session
            for(let j = 0; j < sessionKeys.length; j ++) {
                window.sessionStorage.removeItem(sessionKeys[j]);
            }
        } catch(err) {
            window.Raven && window.Raven.captureException(err);
        }
    }
```

## React-router v4 路由跳转方式

* Link 组件跳转

```javascript
import React from 'react';
import { Link } from "react-router-dom";

class LeftBar extends React.Component {
    constructor(props) {
        super(props);
    }
    render() {
        return (
            <Link to="/about">路由跳转</Link>
        )
    }
}
export default withRouter(LeftBar);
```

* this.context.router.history.push

```javascript
import React from 'react';
import PropTypes from 'prop-types';

class LeftBar extends React.Component {
    constructor(props) {
        super(props);
    }
    static contextTypes = {
        router: PropTypes.object.isRequired,
    }
    handleClick = () => {
        this.context.router.history.push('/about');
    }
    render() {
        return (
            <button onClick={this.handleClick}>路由跳转</button>
        )
    }
}
export default LeftBar;
```

* this.props.history.push

```javascript
import React from 'react';
import { withRouter } from "react-router-dom";

class LeftBar extends React.Component {
    constructor(props) {
        super(props);
    }
    handleClick = () => {
        this.props.history.push('/about');
    }
    render() {
        return (
            <button onClick={this.handleClick}>路由跳转</button>
        )
    }
}
export default withRouter(LeftBar);
```

## React 进阶 -- 高阶组件

[React 中文官网](https://react.docschina.org/docs/higher-order-components.html)

### 最简单的高阶组件

高阶组件则是将一个组件转换成另一个组件：
```javascript
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```
高阶组件在React第三方库中很常见，比如 常用的状态管理 Redux 中的 connect 方法

首先引入一个最简单的高阶组件，以便理解高阶组件的定义及使用

```javascript
import React, { Component } from 'react';

// 高阶组件
const simpleHoc = WrappedComponent => {
    console.log('simpleHoc');
    return class extends Component {
        render() {
            return <WrappedComponent {...this.props} />
        }
    }
}

// 使用高阶组件的组件
class Usual extends Component {
    render() {
        console.log(this.props, 'props');
        return (
            <div>
                Usual
            </div>
        )
    }
}

export default simpleHoc(Usual);
```

### 不修改原始组件

常见做法是通过修改原组件的 prototype 来重写其生命周期方法等（如给WrappedComponent.prototype.componentWillReceiveProps 重新赋值）。请使用纯函数返回新的组件，因为一旦修改原组件，就失去了组件复用的意义。
