---
title: Vue-cli多页面开发
date: 2018-11-24
categories: web 前端
tags: vue-cli
---
现如今比较流行的三大前端框架，vue、react、angular，其中优势是实现前端页面的单页面应用，但是也会有需求是需要多页面开发的，这篇文章总结了 vue-cli 实现多页面配置的步骤...
<!-- more -->

## 搭建vue-cli项目

* 1、全局安装vue-cli

    npm install -g vue-cli

* 2、搭建新项目

    vue init webpack vue-multiple-pages

* 3、进入项目 

    cd vue-multiple-pages

* 4、初始化安装依赖

    npm install  

* 5、执行项目

    npm run dev

* 6、项目打包

    npm run build

## 向 build/utils.js 添加两个函数
有两种方式

* 1、使用package.json,配置地址信息

    - package.json文件
    ```
    // 添加需要显示的页面路径名称
    "buildDirs": [
        "demo/es-activity-list"
    ]
    ```

    - build/utils.js文件
    ```
    /**
    * @method 多页面配置
    */
    const packageConfig = require('../package.json')
    const entry_prefix = "./src/pages/"
    const getEntry = () => {
        const buildDirs = packageConfig.buildDirs; 
        return buildDirs;
    };
    exports.getEntry = getEntry;
    exports.entries = function() {
        let entryObj = {};
        getEntry().forEach(item => {
            let paths = item.split('/');
            // console.info('item: ', item)
            // console.info('path: ', paths)
            entryObj[item + '/' + paths[paths.length - 1]] = `${entry_prefix}${item}/main.js`;
            paths = null;
        });
        // console.info('entryObj: ', entryObj)
        return entryObj;
    };

    exports.htmlPlugin = function() {
        let arr = [];
        getEntry().forEach(item => {
            let paths = item.split('/');
            let tempItem = item + '/' + paths[paths.length - 1];
            let templateFile = `${entry_prefix}${item}/index.html`;
            let filename = `html/${item}.html`;
            let conf = {
                template: templateFile,
                filename: filename,
                chunks: [ item + '/' + "manifest",  item + '/' + "vendor", tempItem],
                inject: true
            };
            // console.info('conf', conf)
            if (process.env.NODE_ENV === "production") {
                conf = merge(conf, {
                    minify: {
                        removeComments: false,
                        collapseWhitespace: false,
                        removeAttributeQuotes: true
                    },
                    chunksSortMode: "dependency"
                });
            }
            arr.push(new HtmlWebpackPlugin(conf));
        });
        return arr;
    };
    ```

* 2、通过glob第三方模块获取文件后缀名
    
    ```
    const getEntry = () => { 
        let entryFiles = glob.sync(PAGE_PATH + '/*/*.js');
        let buildDirs = [];

        entryFiles.forEach((filePath) => {
            let item = filePath.substring(filePath.indexOf('/pages/') + 7, filePath.lastIndexOf('/'));
            buildDirs.push(item)
        })
        return buildDirs;
    };
    exports.getEntry = getEntry;
    /**
    * @method 多入口配置
    * @param 通过glob模块读取pages文件夹,(**) 表示查找文件夹, *.js表示查找.js文件
    */
    exports.entries = function() {
        let entryFiles = glob.sync(PAGE_PATH + '/*/*.js');
        let entryObj = {};

        getEntry().forEach((item) => {
            let paths = item.split('/');
            entryObj[item + '/' + paths[paths.length - 1]] = `${entry_prefix}${item}/${item}.js`;
            paths = null;
        })
        return entryObj;
    }

    /**
    * @method 多页面输出配置
    * @ 与上面的多页面入口配置相同，读取pages文件夹下的对应的html后缀文件，然后放入数组中
    */
    exports.htmlPlugin = function() {
        let arr = [];
        getEntry().forEach(item => {
            let paths = item.split('/');
            let tempItem = item + '/' + paths[paths.length - 1];
            let templateFile = `${entry_prefix}${item}/index.html`;
            let filename = `html/${item}/index.html`;
            let conf = {
                template: templateFile,
                filename: filename,
                chunks: [ item + '/' + "manifest",  item + '/' + "vendor", tempItem],
                inject: true
            };
            console.info('conf', conf)
            if (process.env.NODE_ENV === "production") {
                conf = merge(conf, {
                    minify: {
                        removeComments: false,
                        collapseWhitespace: false,
                        removeAttributeQuotes: true
                    },
                    chunksSortMode: "dependency"
                });
            }
            arr.push(new HtmlWebpackPlugin(conf));
        });
        return arr;
    };

    ```

* 注意

    两者的区别在于获取页面名称及路径的方式不同，即入口文件的拼接。

## 修改 webpack.base.conf.js 文件

修改入口文件的引入配置
```
// entry: {
//   app: './src/main.js'
// },
entry: utils.entries(),
```

## 修改 webpack.dev.conf.js 文件

注释 new HtmlWebpackPlugin，添加concat内容,修改 module.export
```
plugins: [
    。。。
    // new HtmlWebpackPlugin({
    //   filename: 'index.html',
    //   template: 'index.html',
    //   inject: true
    // }),
    。。。
].concat(utils.htmlPlugin())

module.exports = new Promise((resolve, reject) => {
    。。。
    portfinder.getPort((err, port) => {
    if (err) {
    reject(err)
    } else {
        // 遍历需要输出的页面url
        const newsArr = [];
        utils.getEntry().forEach(item => {
            let filename = `${item}.html`;
            //let filename = `${item}/index.html`; // 使用 glob 模块时
            let msg = `Your application is running here: 
                http://${devWebpackConfig.devServer.host}:${port}/html/${filename}`;
            newsArr.push(msg);
        });
        // Add FriendlyErrorsPlugin
        devWebpackConfig.plugins.push(new FriendlyErrorsPlugin({
            compilationSuccessInfo: {
                messages: [...newsArr]
            },
            。。。
})

```

## 修改 webpack.prod.conf.js 文件

注释 new HtmlWebpackPlugin，添加concat内容
```
plugins: [
    。。。
    // new HtmlWebpackPlugin({
    //   filename: config.build.index,
    //   template: 'index.html',
    //   inject: true,
    //   minify: {
    //     removeComments: true,
    //     collapseWhitespace: true,
    //     removeAttributeQuotes: true
    //     // more options:
    //     // https://github.com/kangax/html-minifier#options-quick-reference
    //   },
    //   // necessary to consistently work with multiple chunks via CommonsChunkPlugin
    //   chunksSortMode: 'dependency'
    // }),
    。。。
].concat(utils.htmlPlugin())
```