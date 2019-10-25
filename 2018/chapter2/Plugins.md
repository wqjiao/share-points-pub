# 插件功能 汇总

## webpack 工具插件

### 1、Redux-DevTools 实时监控Redux的状态树的store
```
    redux-devtools
    redux-devtools-log-monitor log信息组件
    redux-devtools-dock-monitor log信息展示组件
    DevTools.js文件
        import React from 'react';
        import { createDevTools } from 'redux-devtools';
        import ChartMonitor from 'redux-devtools-chart-monitor';

        export default createDevTools(
            <ChartMonitor />
        );
```

### 2、webpack-bundle-analyzer
- 安装
    ```
    npm install webpack-bundle-analyzer --save-dev
    ```
- 配置 webpack.config.js
    ```
    const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
    new BundleAnalyzerPlugin({
        //  可以是`server`，`static`或`disabled`。
        //  在`server`模式下，分析器将启动HTTP服务器来显示软件包报告。
        //  在“静态”模式下，会生成带有报告的单个HTML文件。
        //  在`disabled`模式下，你可以使用这个插件来将`generateStatsFile`设置为`true`来生成Webpack Stats JSON文件。
        analyzerMode: 'server',
        //  将在“服务器”模式下使用的主机启动HTTP服务器。
        analyzerHost: '127.0.0.1',
        //  将在“服务器”模式下使用的端口启动HTTP服务器。
        analyzerPort: 8866, 
        //  路径捆绑，将在`static`模式下生成的报告文件。
        //  相对于捆绑输出目录。
        reportFilename: 'report.html',
        //  模块大小默认显示在报告中。
        //  应该是`stat`，`parsed`或者`gzip`中的一个。
        //  有关更多信息，请参见“定义”一节。
        defaultSizes: 'parsed',
        //  在默认浏览器中自动打开报告
        openAnalyzer: true,
        //  如果为true，则Webpack Stats JSON文件将在bundle输出目录中生成
        generateStatsFile: false, 
        //  如果`generateStatsFile`为`true`，将会生成Webpack Stats JSON文件的名字。
        //  相对于捆绑输出目录。
        statsFilename: 'stats.json',
        //  stats.toJson（）方法的选项。
        //  例如，您可以使用`source：false`选项排除统计文件中模块的来源。
        //  在这里查看更多选项：https：  //github.com/webpack/webpack/blob/webpack-1/lib/Stats.js#L21
        statsOptions: null,
        logLevel: 'info'
    })
    ```

### 3、extract-text-webpack-plugin

* 作用：
    该插件的主要是为了抽离css样式,防止将样式打包在js中引起页面样式加载错乱的现象

* 安装引用：
    const ExtractTextPlugin = require('extract-text-webpack-plugin');

## Chrome Plugin

### 1.ReRes Chrome插件
    
替换Charles 用于地址映射， 例如可以将测试地址映射到本地进行测试

### 2.Octotree 插件

用 `Chrome` 插件 `Octotree`，左侧会出现树形结构，方便你浏览源代码。

### 3.Github Desktop 桌面应用

用 `Github Desktop` 桌面应用。方便把代码clone到本地，然后进行各种git操作。

### 4.如果你开发前端(Angular,React,ES6,Typescipt)

直接把 `Chrome` 变成一个在线IDE。帮你把 `npm` 包都准备好，直接可以运行。

只要把github地址改成 `https://stackblitz.com/github` 开头就可以了。

Github项目地址是 `https://github.com/wqjiao/simple-weather`

改成 `https://stackblitz.com/github/wqjiao/simple-weather`

注意：一定有 `package.json` 文件的项目

## webpack plugin汇总

### 功能类
* html-webpack-plugin

    自动生成html,基本用法：
    ```
    new HtmlWebpackPlugin({
        filename: 'index.html', // 生成文件名
        template: path.join(process.cwd(), './index.html') // 模班文件
    })
    ```

* copy-webpack-plugin

    拷贝资源插件,基本用法：
    ```
    new CopyWebpackPlugin([
        {
            from: path.join(process.cwd(), './vendor/'),
            to: path.join(process.cwd(), './dist/'),
            ignore: ['*.json']
        }
    ])
    ```

* webpack-manifest-plugin && assets-webpack-plugin

    俩个插件效果一致，都是生成编译结果的资源单，只是资源单的数据结构不一致而已。
    - webpack-manifest-plugin 基本用法：
        ```
        module.exports = {
            plugins: [
                new ManifestPlugin()
            ]
        }
        ```

    - assets-webpack-plugin 基本用法：
        ```
        module.exports = {
            plugins: [
                new AssetsPlugin()
            ]
        }
        ```
* clean-webpack-plugin

    在编译之前清理指定目录指定内容,基本用法：
    ```
    // 清理目录
    const pathsToClean = [
        'dist',
        'build'
    ]
    
    // 清理参数
    const cleanOptions = {
        exclude:  ['shared.js'], // 跳过文件
    }
    module.exports = {
        // ...
        plugins: [
            new CleanWebpackPlugin(pathsToClean, cleanOptions)
        ]
    }
    ```

* compression-webpack-plugin

    提供带 Content-Encoding 编码的压缩版的资源,基本用法：
    ```
    module.exports = {
        plugins: [
            new CompressionPlugin()
        ]
    }
    ```

* progress-bar-webpack-plugin

    编译进度条插件,基本用法：
    ```
    module.exports = {
        //...
        plugins: [
            new ProgressBarPlugin()
        ]
    }
    ```

### 代码相关类

* webpack.ProvidePlugin

    自动加载模块，如 $ 出现，就会自动加载模块；$ 默认为'jquery'的exports,基本用法
    ```
    new webpack.ProvidePlugin({
    $: 'jquery',
    })
    ```

* webpack.DefinePlugin

    定义全局常量,用法：
    ```
    new webpack.DefinePlugin({
    'process.env': {
        NODE_ENV: JSON.stringify(process.env.NODE_ENV)
    }
    })
    ```

* mini-css-extract-plugin && extract-text-webpack-plugin

    - 提取css样式，对比：
        mini-css-extract-plugin 为webpack4及以上提供的plugin，支持css chunk
        extract-text-webpack-plugin 只能在webpack3 及一下的版本使用，不支持css chunk

    - extract-text-webpack-plugin：
        ```
        const ExtractTextPlugin = require("extract-text-webpack-plugin");
        
        module.exports = {
            module: {
                rules: [
                    {
                        test: /\.css$/,
                        use: ExtractTextPlugin.extract({
                            fallback: "style-loader",
                            use: "css-loader"
                        })
                    }
                ]
            },
            plugins: [
                new ExtractTextPlugin("styles.css"),
            ]
        }
        ```

    - mini-css-extract-plugin：
        ```
        const MiniCssExtractPlugin = require("mini-css-extract-plugin");
        module.exports = {
            module: {
                rules: [
                    {
                        test: /\.css$/,
                        use: [
                            {
                                loader: MiniCssExtractPlugin.loader,
                                options: {
                                    publicPath: '/'  // chunk publicPath
                                }
                            },
                            "css-loader"
                        ]
                    }
                ]
            },
            plugins: [
                new MiniCssExtractPlugin({
                filename: "[name].css", // 主文件名
                chunkFilename: "[id].css"  // chunk文件名
                })
            ]
        }
        ```

### 编译结果优化类

* wbepack.IgnorePlugin

    忽略regExp匹配的模块,用法：
    ```
    new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
    ```

* uglifyjs-webpack-plugin

    代码丑化，用于js压缩,用法：
    ```
    module.exports = {
        //...
        optimization: {
            minimizer: [new UglifyJsPlugin({
                cache: true,   // 开启缓存
                parallel: true, // 开启多线程编译
                sourceMap: true,  // 是否sourceMap
                uglifyOptions: {  // 丑化参数
                    comments: false,
                    warnings: false,
                    compress: {
                        unused: true,
                        dead_code: true,
                        collapse_vars: true,
                        reduce_vars: true
                    },
                    output: {
                        comments: false
                    }
                }
            }]
        }
    };
    ```

* optimize-css-assets-webpack-plugin
    css压缩，主要使用 cssnano 压缩器,用法：

    ```
    module.exports = {
        //...
        optimization: {
            minimizer: [new OptimizeCssAssetsPlugin({
                cssProcessor: require('cssnano'),   // css 压缩优化器
                cssProcessorOptions: { discardComments: { removeAll: true } } // 去除所有注释
            })]
        }
    };
    ```

* webpack-md5-hash
    使你的chunk根据内容生成md5，用这个md5取代 webpack chunkhash。
    ```
    var WebpackMd5Hash = require('webpack-md5-hash');
    
    module.exports = {
        // ...
        output: {
            //...
            chunkFilename: "[chunkhash].[id].chunk.js"
        },
        plugins: [
            new WebpackMd5Hash()
        ]
    };
    ```

* SplitChunksPlugin

    CommonChunkPlugin 的后世，用于chunk切割。

    webpack 把 chunk 分为两种类型：

        一种是初始加载initial chunk，另外一种是异步加载 async chunk，
        
        如果不配置SplitChunksPlugin，webpack会在production的模式下自动开启，
        
        默认情况下，webpack会将 node_modules 下的所有模块定义为异步加载模块，
        
        并分析你的 entry、动态加载（import()、require.ensure）模块，
        
        找出这些模块之间共用的node_modules下的模块，并将这些模块提取到单独的chunk中，
        
        在需要的时候异步加载到页面当中，其中默认配置如下：

            ```
            module.exports = {
                //...
                optimization: {
                    splitChunks: {
                        chunks: 'async', // 异步加载chunk
                        minSize: 30000,
                        maxSize: 0,
                        minChunks: 1,
                        maxAsyncRequests: 5,
                        maxInitialRequests: 3,
                        automaticNameDelimiter: '~', // 文件名中chunk分隔符
                        name: true,
                        cacheGroups: {
                            vendors: {
                                test: /[\\/]node_modules[\\/]/,  // 
                                priority: -10
                            },
                            default: {
                                minChunks: 2,  // 最小的共享chunk数
                                priority: -20,
                                reuseExistingChunk: true
                            }
                        }
                    }
                }
            };
            ```

### 译优化类

* DllPlugin && DllReferencePlugin && autodll-webpack-plugin
    - dllPlugin 将模块预先编译，DllReferencePlugin 将预先编译好的模块关联到当前编译中，
    当 webpack 解析到这些模块时，会直接使用预先编译好的模块。

    - autodll-webpack-plugin 相当于 dllPlugin 和 DllReferencePlugin 的简化版，
    其实本质也是使用 dllPlugin && DllReferencePlugin，
    它会在第一次编译的时候将配置好的需要预先编译的模块编译在缓存中，
    第二次编译的时候，解析到这些模块就直接使用缓存，而不是去编译这些模块。

    - dllPlugin 基本用法：
    ```
    const output = {
        filename: '[name].js',
        library: '[name]_library',
        path: './vendor/'
    }

    module.exports = {
        entry: {
            vendor: ['react', 'react-dom']  // 我们需要事先编译的模块，用entry表示
        },
        output: output,
        plugins: [
            new webpack.DllPlugin({  // 使用dllPlugin
                path: path.join(output.path, `${output.filename}.json`),
                name: output.library // 全局变量名， 也就是 window 下 的 [output.library]
            })
        ]
    }
    ```

    - DllReferencePlugin 基本用法：
    ```
    const manifest = path.resolve(process.cwd(), 'vendor', 'vendor.js.json')

    module.exports = {
        plugins: [
            new webpack.DllReferencePlugin({
                manifest: require(manifest), // 引进dllPlugin编译的json文件
                name: 'vendor_library' // 全局变量名，与dllPlugin声明的一致
            }
        ]
    }
    ```

    - autodll-webpack-plugin 基本用法：
    ```
    module.exports = {
        plugins: [
            new AutoDllPlugin({
                inject: true, // 与 html-webpack-plugin 结合使用，注入html中
                filename: '[name].js',
                entry: {
                    vendor: [
                        'react',
                        'react-dom'
                    ]
                }
            })
        ]
    }
    ```

* happypack && thread-loader

    多线程编译，加快编译速度，thread-loader不可以和 mini-css-extract-plugin 结合使用。

    - happypack 基本用法：
    ```
    const HappyPack = require('happypack');
    const os = require('os');
    const happyThreadPool = HappyPack.ThreadPool({ size: os.cpus().length });
    const happyLoaderId = 'happypack-for-react-babel-loader';

    module.exports = {
        module: {
            rules: [{
                test: /\.jsx?$/,
                loader: 'happypack/loader',
                query: {
                    id: happyLoaderId
                },
                include: [path.resolve(process.cwd(), 'src')]
            }]
        },
        plugins: [new HappyPack({
            id: happyLoaderId,
            threadPool: happyThreadPool,
            loaders: ['babel-loader']
        })]
    }
    ```

    - thread-loader 基本用法:
    ```
    module.exports = {
        module: {
            rules: [
                {
                    test: /\.js$/,
                    include: path.resolve("src"),
                    use: [
                        "thread-loader",
                        // your expensive loader (e.g babel-loader)
                        "babel-loader"
                    ]
                }
            ]
        }
    }
    ```

* hard-source-webpack-plugin && cache-loader

    使用模块编译缓存，加快编译速度。

    - hard-source-webpack-plugin 基本用法：
    ```
    module.exports = {
        plugins: [
            new HardSourceWebpackPlugin()
        ]
    }
    ```

    - cache-loader 基本用法：
    ```
    module.exports = {
        module: {
            rules: [
                {
                    test: /\.ext$/,
                    use: [
                        'cache-loader',
                        ...loaders
                        ],
                    include: path.resolve('src')
                }
            ]
        }
    }
    ```

### 编译分析类

* webpack-bundle-analyzer

    编译模块分析插件,基本用法：
    ```
    new BundleAnalyzerPlugin({
        analyzerMode: 'server',
        analyzerHost: '127.0.0.1',
        analyzerPort: 8889,
        reportFilename: 'report.html',
        defaultSizes: 'parsed',
        generateStatsFile: false,
        statsFilename: 'stats.json',
        statsOptions: null,
        logLevel: 'info'
    }),
    ```

* stats-webpack-plugin && PrefetchPlugin

    stats-webpack-plugin 将构建的统计信息写入文件，该文件可在 http://webpack.github.io/analyse中上传进行编译分析，
    
    并根据分析结果，可使用 PrefetchPlugin 对部分模块进行预解析编译。

    - stats-webpack-plugin 基本用法：
    ```
    module.exports = {
        plugins: [
            new StatsPlugin('stats.json', {
                chunkModules: true,
                exclude: [/node_modules[\\\/]react/]
            })
        ]
    };
    ```

    - PrefetchPlugin 基本用法：
    ```
    module.exports = {
        plugins: [
            new webpack.PrefetchPlugin('/web/', 'app/modules/HeaderNav.jsx'),
            new webpack.PrefetchPlugin('/web/', 'app/pages/FrontPage.jsx')
        ];
    }
    ```

* speed-measure-webpack-plugin

    统计编译过程中，各loader和plugin使用的时间。
    ```
    const SpeedMeasurePlugin = require("speed-measure-webpack-plugin");
    
    const smp = new SpeedMeasurePlugin();
    
    const webpackConfig = {
        plugins: [
            new MyPlugin(),
            new MyOtherPlugin()
        ]
    }
    module.exports = smp.wrap(webpackConfig);
    ```

### 图片编辑 zrender

```
const React = require('react');
const ReactDOM = require('react-dom');

const {
    Zrender,
    getShape
} = require('react-zrender');

require('zrender/graphic/shape/Circle');
const Circle = getShape('Circle');

const App = React.createClass({

    getInitialState() {
        return {
            scale: [1, 1]
        };
    },

    componentDidMount() {
        const {circle} = this.refs;
        const me = this;
        circle
            .animate('', false)
            .when(1000, {
                scale: [5, 5]
            })
            .done(() => {
                me.setState({scale: [5, 5]});
            })
            .start('cubicOut');
    },

    render() {

        return (
            <Zrender
                renderer="canvas"
                width={500}
                height={300}>
                <Circle
                    ref="circle"
                    cx={0}
                    cy={0}
                    r={20}
                    scale={this.state.scale}
                    position={[250, 150]}
                    stroke="#000"
                    fill="transparent"
                ></Circle>
            </Zrender>
        );
    }
});
```

### 参考网址：
    [](https://segmentfault.com/a/1190000016816813)
    [webpack](https://webpack.docschina.org/plugins/limit-chunk-count-plugin/)

