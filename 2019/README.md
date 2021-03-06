## 2019年工作计划

* 1、将 kdj 项目中的旧组件替换完成 -- antd
    - Datatables 插件        Table
    - rc-pagination          Pagination
    - react-modal            Modal
    - react-icheck           Checkbox/Radio
    - react-select2-wrapper  Select
    - rc-calendar            DatePicker/MonthPicker/RangePicker
    - rc-checkbox

    贷后基本完成，除了在线上路由数据库未使用到且本地也未引用到的三个本地路由：(路由已清除，组件未处理)
        /mission/informationentry -- InformationEntry
        /mission/informationentry_list -- InformationEntryList
        /after/all-finance-price-list -- ALEFinanceAllPriceList

* 2、后期新增路由页面时，尽量进行组件拆分

    |-- pageDemo
    |  |-- components
    |  |-- index.js
    |  |-- index.less
    |  |-- constants.js

    遵守仅该页面中用到的组件时，放在当前页面的 components 中，否则放在外层的 components 中

* 3、弄清楚各类插件的使用，并删除未使用或不必要的插件
    - react-addons-test-utils
    - core-js
        开源库zloirock/core-js
        提供了es5、es6的polyfills，包括promises、symbols、collections、iterators、typed arrays、ECMAScript 7+ proposals、setImmediate 等等。
        如果使用了 babel-runtime、babel-plugin-transform-runtime 或者 babel-polyfill，你就可以间接的引入了 core-js 标准库

    - object-assign
    - webpack-sentry-plugin

* 4、输出适合我们公司的 React 项目架构及代码规范

* 5、继续学习新东西，巩固已有技术（温故而知新），学习使我清醒

* 6、后台系统降低耦合性
    react 高阶组件 -- 解耦和灵活性

* 7、拆分 redux
    - 很多个页面共用一个 redux，换页时，数据并没有提交而是暂存在 redux 中

* 8、尽量避免使用 refs，采用回调或其他方式代替

* 9、一个方法或流程中尽量一次 `setState({})` 参数，避免多次 `setState({})`

## 2019年知识点汇总

* [2019-01-26](./chapter1/README.md)
    * [http 协议分析](./chapter1/HTTP.md)
    * [moment 日期格式化](./chapter1/Moment.md)
    * [socketIo 消息推送](./chapter1/Socket.io.md)
    * [store 本地数据暂存](./chapter1/Store.md)
    * [手动搭建 webpack react](./chapter1/WebpackReact.md)
* [2019-02-13](./chapter2/README.md)
    * [css 新属性](./chapter2/Css.md)
    * [javascript 属性及方法](./chapter2/Javascript.md)
    * [2019年性能优化](./chapter2/PerformanceOptCriteria.md)
    * [Promise介绍](./chapter2/Promise.md)
    * [Tui.image.editor](./chapter2/Tui.image.editor.md)
* [2019-03-15](./chapter3/README.md)
    * [Ant Design Pro](./chapter3/AntdDesignPro.md)
    * [App API](./chapter3/AppAPI.md)
    * [ForEach](./chapter3/ForEach.md)
    * [高阶函数](./chapter3/HighOrder.md)
    * [React项目架构](./chapter3/React项目架构.md)
* [2019-05-22](./chapter4/README.md)
    * [meta 标签](./chapter4/meta标签.md)
    * [开发HTML5小游戏](./chapter4/开发HTML5小游戏.md)
    * [你可能不知道的点](./chapter4/你可能不知道的点.md)
    * [Ts常用规则](./chapter4/Ts常用规则.md)
* [2019-06-15](./chapter5/README.md)
    * [防抖&&节流](./chapter5/防抖&&节流.md)
    * [前端跨页面通信](./chapter5/前端跨页面通信.md)
    * [FileTarget](./chapter5/FileTarget.md)
    * [JS设计模式](./chapter5/JS设计模式.md)
    * [Node](./chapter5/Node.md)
* [2019-09-16](./chapter6/README.md)
    * [Python](./chapter6/python.md)
    * [Vscode Plugins](./chapter6/vscode-plugins.md)