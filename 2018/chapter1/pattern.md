# 项目搭建 及 结构

## 工作模式

* 1、工作分工
    涉及部门：产品经理、交互设计师、视觉设计师、前端、后端、测试
    产品：负责需求调研、汇总并输出需求文档 http://www.woshipm.com/
    交互： 按照需求输出交互设计网页
    视觉： 输出Sketch网页设计稿
    前端： 使用相应技术栈开发页面
    后端： 与前端同步开发，输出接口文档
* 2、开会确认工期
    根据需求各部门开会讨论需求及其合理性，计算各部门所需工时，安排每个人的任务并上传至看板，实时跟踪项目进度。
* 3、项目分支
     master: 主分支
    gamma: 打包提交测试部门测试分支
    beta: 自动化用例测试分支(前一天晚上提交至当前分支，第二天上班前会收到自动化测试结果邮件)
    alpha: 开发人员测试分支
    20180818-interhotel-booking: 当前开发分支 (预计上线时间-业务名-需求统称)
* 4、技术栈

    老业务：jQuery、php、nodejs、gulp、sass

    后台系统：Angularjs、webpack、ng-zorro-antd、router、mock、axios、sass
    
    新业务： react、typescript、mock、axios、webpack、sass、antd-design、nginx
        路由方面直接用node，然后nginx分发，不需要使用react-router
    资源服务后台：Vue、vuex、axios、mock、sass、webpack、router、element-ui、echart

    webapp: Vue、React-Native

* 5、各类小需求，直接提交jira，指定相应部门实施任务
* 6、测试提bug至禅道
* 7、添加注释
    1）整个js文件的描述性注释，解释这个js的功能是什么，开发者是谁
    ```
    /**
    * @file LBS控制器
    * @author limingle
    * @copyright Synway SFE
    * @createDate 2017-10-16 09:40:11
    */
    ```

    2）每个function的注释，描述这个function的用途是什么，如果时间允许最好有参数和返回值的描述。
    ```
    /**
    * @method
    * @param {Type} data 目标对象
    * @returns {Type} 运营商名称
    * @desc 根据目标对象获取运营商
    */
    ```
* 8、项目迭代流程

- 需求分析（requirements analysis）
- 设计（design）
- 编码（coding）
- 测试（testing）
- 部署和评估（deployment / evaluation）

## 项目src目录结构
```
* src --
    |-- App.js // app入口文件
    |-- index.js
    |-- layout // 布局组件
    |     |-- Header // 头部
    |     |-- Footer // 尾部
    |     |-- leftBar // 左侧边栏
    |     |-- container // 内容框架
    |-- components  // 公共组件
    |-- pages // 路由页面
    |     |-- Home
    |     |   |-- index.js  
    |     |   |-- index.less
    |     |   |-- img
    |     |   |-- components
    |-- assets // 公共样式或图片
    |     |-- scss
    |     |-- img
    |-- mock // mock数据
    |-- store 
    |-- reducer
    |-- utils
```

## 搭建React项目

* 1、使用create-react-app脚手架搭建
    ```
    npm install -g create-react-app
    ```
* 2、创建my-app项目
    ```
    create-react-app my-app
    ```
* 3、进入my-app项目
    ```
    cd my-app
    或
    yarn create react-app antd-demo
    ```
* 4、运行my-app工程
    ```
    npm start
    或
    yarn start
    ```
* 5、解压隐藏的webpack配置文件
    ```
    npm run eject
    或
    yarn run eject
    ```
* 6、引入ant-design UI 库
    ```
    npm add antd
    或
    yarn add antd
    ```
* 7、引入 antd 框架
    - 全局引入

        ```
        <!-- js文件中引入组件 -->
        import Button from 'antd/lib/button';
        <!-- App.css中引入css文件 -->
        @import '~antd/dist/antd.css';
        ```

    - 按需加载组件

        ```
        <!-- 按需加载组件 babel-plugin-import -->
        npm install babel-plugin-import --save-dev
        或
        yarn add babel-plugin-import --save-dev
        <!-- 然后只需从 antd 引入模块即可，无需单独引入样式。等同于下面手动引入的方式。 -->
        在 package.json 中添加一下代码：
        "babel": {
            "presets": [
                "react-app"
            ],
            "plugins": [
                [
                    "import",
                    {
                    "libraryName": "antd",
                    "libraryDirectory": "es",
                    "style": "css"
                    }
                ]
            ]
        },
        // babel-plugin-import 会帮助你加载 JS 和 CSS
        import { Button } from 'antd';
        ```

* 8、安装 less 依赖

    ```
    npm install less less-loader postcss-loader --save-dev
    修改配置文件 webpack.config.dev.js webpack.config.pro.js
    // 在module中做了三处修改
    // 第一处是找到 `test: /\.css$/` 将其改为  `test: /\.(css|less)$/`
    // 第二处是增加 `less-loader`的配置
    // 第三处是在exclude属性中增加 `/\.(css|less)$/`

    修改antd中的主题色，需在babel-loader的options中添加 或在 package.json 中直接修改 `style: true`
    plugins: [
        ['import', { libraryName: 'antd', libraryDirectory: 'es', style: true }], // `style: true` 会加载 less 文件
    ]

    在rule中添加less-loader modifyVars
    const autoprefixer = require('autoprefixer');
    const getLessVariables = require('./get-less-variables');

    {
        test: /\.less$/,
        use: [
        require.resolve('style-loader'),
        {
            loader: require.resolve('css-loader'),
            options: {
            importLoaders: 1
            },
        },
        {
            loader: require.resolve('postcss-loader'),
            options: {
            // Necessary for external CSS imports to work
            // https://github.com/facebookincubator/create-react-app/issues/2677
            ident: 'postcss',
            plugins: () => [
                require('postcss-flexbugs-fixes'),
                autoprefixer({
                browsers: [
                    '>1%',
                    'last 4 versions',
                    'Firefox ESR',
                    'not ie < 9', // React doesn't support IE8 anyway
                ],
                flexbox: 'no-2009',
                }),
            ],
            },
        },
        {
            loader: require.resolve('less-loader'),
            options:{
            javascriptEnabled: true,
            // modify-var会将所传入的参数写入所有less文件的底部，我们自己定义的变量会被覆盖
            // 覆盖变量
            modifyVars: {
                'primary-color': '#1DA57A',
            },
            // global-var 会将所传入的参数写入所有的less文件的顶部，我们可以在less文件中重写这些参数的值
            // 变量前置
            globalVars: getLessVariables('./src/assets/theme.less')
            }
        },
        ],
    },
    ```
    get-less-variables.js
    ```js
    const fs = require('fs');
    module.exports = function getLessVariables(file) {
        let themeContent = fs.readFileSync(file, 'utf-8');
        let variables = {};

        themeContent.split('\n').forEach(function(item) {
            if (item.indexOf('//') > -1 || item.indexOf('/*') > -1) {
                return;
            }
            
            let _pair = item.split(':');
            if (_pair.length < 2) return;

            let key = _pair[0].replace('\r', '').replace('@', '');
            if (!key) return;

            let value = _pair[1].replace(';', '').replace('\r', '').replace(/^\s+|\s+$/g, '')
            variables[key] = value;
        });

        return variables;
    }
    ```

    需要注意的是：antd.less 在 less@2.x 是可以的，但在 less@3.x 会报错，需要开启 配置项 javascriptEnabled: true
    ```
    { loader: 'less-loader', options: { javascriptEnabled: true } }
    ```

* 9、安装 sass 依赖
    ```
    <!-- 安装 sass-loader node-sass 依赖包 -->
    npm install sass-loader node-sass --save-dev

    <!-- 在 rule 中添加 sass 配置 -->
    <!-- 实际在原有基础上增加 /\.scss$/  -->
    {
        exclude: [/\.js$/, /\.html$/, /\.json$/, /\.scss$/],
        loader: require.resolve('file-loader'),
        options: {
            name: 'static/media/[name].[hash:8].[ext]',
        },
    },
    {
        test:/\.scss$/,
        loaders:['style-loader','css-loader','sass-loader']
    }
    ```

* 10、安装redux、resct-redux、redux-logger、redux-thunk

    ```
    npm install redux react-redux redux-logger redux-thunk --save
    或
    yarn add redux react-redux redux-logger redux-thunk

    (1) import { createStore, combineReducers } from 'redux';
        // createStore 用来生成store， createStore函数接受另一个函数作为参数，返回新生成的 Store 对象
        // combineReducers方法，用于 Reducer 的拆分。你只要定义各个子 Reducer 函数，然后用这个方法，将它们合成一个大的 Reducer
        const reducer = (state = {}, action = {}) => {
            switch(action.type) {
                case '':
                    const newState = Object.assign({}, state);
                    return newState;
                default:
                    return state;
            }
        }
        const store = createStore(reducer, {});
    (2) store.subscribe()
        Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数
    ```

* 11、安装 axios 接口依赖
    ```
    npm install --save axios
    ```

## React项目中 less 引入全局变量

* 1、创建get-less-variables.js文件
    ```
    const fs = require('fs');
    module.exports = function getLessVariables(file) {
        var themeContent = fs.readFileSync(file, 'utf-8');
        var variables = {};
        
        themeContent.split('\n').forEach(function(item) {
            // 判断是否为注释行
            if (item.indexOf('//') === 0 || item.indexOf('/*') === 0) {
                return
            }

            // 判断该行中是否有注释，做处理
            if (item.indexOf('//') > 0) {
                item = item.substr(0, item.indexOf('//'))
            }
            if (item.indexOf('/*') > 0) {
                item = item.substr(item.indexOf('/*'))
            }
        
            // 注释中不能出现英文冒号 : 
            var _pair = item.split(':');
            if (_pair.length < 2) return;

            var key = _pair[0].replace('\r', '').replace('@', '');
            if (!key) return;

            var value = _pair[1].replace(';', '').replace('\r', '').replace(/^\s+|\s+$/g, '');
            variables[key] = value;
        })
        return variables;
    }
    ```

* 2、在src下面创建styles/theme.less文件，写入全局变量
    ```
    @audit-task: audit-task;
    @primary-color: #439abf;
    ```

* 3、在 webpack 文件中添加引入 less-loader，配置
    ```
    const getLessVariables = require('./get-less-variables');
    {
        loader: require.resolve('less-loader'),
        options:{
            // modify-var会将所传入的参数写入所有less文件的底部
            //我们自己定义的变量会被覆盖, 覆盖变量
            modifyVars: {
                'primary-color': '#1DA57A',
            },
            // global-var 会将所传入的参数写入所有的less文件的顶部
            //我们可以在less文件中重写这些参数的值, 变量前置
            globalVars: getLessVariables('./src/styles/theme.less')
        }
    }
    注意：在webpack 1.x版本中需要将方法中的对象转换成json字符串
    JSON.stringfy({
        globalVars: getLessVariables('./src/styles/theme.less')
    })
    ```
    
* 4、直接在各组件的less文件中使用该变量
    ```
    @audit-task-ready: ~"@{audit-task}-ready";
    .@{audit-task-ready} {
        .content-title {
            color: @primary-color;
        }
    }
    ```

## React 项目中引入文件地址基于 src

需要在配置文件中 webpack.config.js 中添加 `'@': path.resolve('src')`，之后在项目中直接使用@，例如 `import Routes from '@/routes';` 即可。
```
alias: {
    // Support React Native Web
    // https://www.smashingmagazine.com/2016/08/a-glimpse-into-the-future-with-react-native-for-web/
    'react-native': 'react-native-web',
    '@': path.resolve('src'),
},
```
