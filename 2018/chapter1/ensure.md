# 组件按需加载

## 一、angular-router

    angular-router配合懒加载实现路由的按需加载，模块划分，从根模块中分离出来，减轻根模块负重
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

## 二、vue-router

    vue-router懒加载，即路由的按需加载
    // 1、第一种 按需加载方式
    const Home = () => import('../pages/home.vue')
    {
        path: '/_home',
        component: Home
        // 2、另外一种按需加载的方式
        // component: resolve => require(['../pages/home.vue'], resolve)
    }

## 三、 react-router

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
    ```
    方式一：
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
        import React from 'react';
        import { Route, Switch } from 'react-router-dom';
        import App from '../App';
        import Bundle from '../Bundle';

        // 加载组件 公共函数
        function BundleComponent (ComItem) {
        return (props) => (
            <Bundle load={() => import('../components/' + ComItem)}>
                {(ComItem) => <ComItem {...props}/>}
            </Bundle>
        );
        }

        const Routes = (
            <App>
                <Switch>
                    <Route exact path="/" component={ BundleComponent('HomePage') } /> {/* exact 严格匹配'/'路由 */}
                    <Route path="/inbox/messages/:id" component={BundleComponent('Message')} />
                    <Route path="/inbox" component={BundleComponent('Inbox')} />
                    <Route path="/table" component={BundleComponent('Table')} />
                    <Route path="*" component={BundleComponent('NoMatch')} />
                </Switch>
            </App>
        );

        export default Routes;
    ```
    ```
    方式二：
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
        import React from 'react';
        import { Route, Switch } from 'react-router-dom';
        import App from '../App';
        import asyncComponent from '../components/AsyncComponent';

        const AsyncHomePage = asyncComponent(() => import('../components/HomePage'));
        const AsyncNoMatch = asyncComponent(() => import('../components/NoMatch'));

        const routes = [
            {
                name: 'Inbox',
                path: '/inbox'
            },
            {
                name: 'Table',
                path: '/table'
            },
            {
                name: 'Message',
                path: '/inbox/messages/:id'
            }
        ]

        const Routes = (
            <App>
            <Switch>
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
                <Route path="*" component={AsyncNoMatch} /> {/* 404组件 */}
            </Switch>
        </App>
        );

        export default Routes;
    ```
    
    这个asyncComponent 函数接受一个importComponent 的参数，importComponent 调用时候将动态引入给定的组件。

    在componentDidMount 我们只是简单地调用importComponent 函数，并将动态加载的组件保存在状态中。

    最后，如果完成渲染，我们有条件地提供组件。在这里我们如果不写null的话，也可提供一个Loading图，代表着组件正在渲染。

* 参考网址：

    https://blog.csdn.net/liwusen/article/details/53944321
        
    webpack1.13.3以下版本github：
        https://github.com/love-fay/fay-webpack-redux-code-splitting/tree/master/react
        
    不同webpack版本对应链接：https://blog.csdn.net/valada/article/details/79909786
    