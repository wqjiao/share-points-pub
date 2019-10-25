# React 性能优化标准

性能优化标准 (Performance optimization criteria)

## React 16.8 中新增 React.lazy()

```javascript
// React.lazy 动态导入组件，实现按需加载和代码分割：展示时才加载
const WqjiaoEditor = React.lazy(() => import('./WqjiaoEditor'));

function MyComponent() {
    return (
        <div>
            <WqjiaoEditor />
        </div>
    )
}
```

## Suspense 组件

`Suspense` 组件用于包装lazy组件，在 `lazy` 组件还没有完全加载时，将 `fallback` 内容呈现给用户。

用动态加载，编译时会将文件分割，从加载文件到呈现会有时间延迟，此时可以使用 `Suspense` 展示一个 loading。

## React 首屏加载白屏

[腾讯 React 加载性能优化指南](https://juejin.im/post/5b506ae0e51d45191a0d4ec9)

* 使用 html-webpack-plugin 自动插入 loading

实际业务中肯定是有很多很多页面的，每个页面都要我们手动地复制粘贴这么一个 loading 态显然太不优雅了，这时我们可以考虑使用 html-webpack-plugin 来帮助我们自动插入 loading。

```js
let HtmlWebpackPlugin = require('html-webpack-plugin');
let path = require('path');

// 读取写好的 loading 态的 html 和 css
let loading = {
    html: fs.readFileSync(path.join(__dirname, './loading.html')),
    css: '<style>' + fs.readFileSync(path.join(__dirname, './loading.css')) + '</style>'
}

let webpackConfig = {
    entry: 'index.js',
    output: {
        path: path.resolve(__dirname, './dist'),
        filename: 'index_bundle.js'
    },
    plugins: [
            new HtmlWebpackPlugin({
                title: '测试标题',
                filename: 'xxxx.html',
                template: 'template.html',
                loading: loading
            })
    ]
};
```

在 public/index.html 中引入
```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <title><%= htmlWebpackPlugin.options.title %></title>
        <%= htmlWebpackPlugin.options.loading.css %>
    </head>

    <body>
        <div id="root">
            <%= htmlWebpackPlugin.options.loading.html %>
        </div>
    </body>
</html>
```

## 打包工具

[2019年前端热门打包工具](https://juejin.im/post/5c565602e51d457fce0126c0)

## 代码质量管控，重复度检测

[美团 -- 掘金](https://juejin.im/post/59bb8b546fb9a00a4247532e)

## immutable.js

[immutable--掘金](https://juejin.im/post/5948985ea0bb9f006bed7472)

## DevTools

[developers](https://developers.google.com/web/updates/2017/04/devtools-release-notes)