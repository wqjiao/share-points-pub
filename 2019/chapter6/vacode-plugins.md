# VsCode 插件开发学习

## 安装参考资料

[搭建环境](https://juejin.im/entry/5b50509d5188251967307780)
[Github](https://github.com/any86/any-rule)

```
<!-- 搭建环境 -->
nodejs
npm
npm install -g yo
npm install -g generator-code

<!-- 初始化项目 -->
yo code

<!-- F5 调试 -->
<!-- command + shift + p 调用插件 -->

<!-- 打包 -->
npm install -g vsce

<!-- 发布 -->
vsce create-publisher xxxx
vsce publish
```

package.json
```json
{
    "name": "city-aqi", // 插件名称
    "displayName": "city-aqi", // 插件显示名称
    "description": "Air Quaility Index of City", // 插件描述
    "version": "0.0.1",
    "publisher": "beanleecode", // 插件发布者
    "activationEvents": [ // 活动事件列表
        "onCommand:extension.sayHello"
    ],
    "main": "./extension", // 入口文件
    "contributes": {
        "commands": [ // 对应命令列表
            {
                "command": "extension.sayHello",
                "title": "Hello World"
            }
        ]
    },
}

```

extension.js
```js
<br />
const vscode = require('vscode');
 
function activate(context) {
 
    // 这里的代码将只会在插件激活时执行一次
    console.log('Congratulations, your extension "city-aqi" is now active!');
    
    // 定义在 package.json 中的命令在这里定义
    // 提供 registerCommand 来注册实现代码
    // commandId 参数必须与 package.json 匹配
    let disposable = vscode.commands.registerCommand('extension.sayHello', function () {
        // 这里的代码每次执行 这个命令 的时候都会被执行
        
        // 显示信息框
        vscode.window.showInformationMessage('Hello World!');
    });
    
    context.subscriptions.push(disposable);
}
exports.activate = activate;
 
// 插件被停用的时候被调用
function deactivate() {}

exports.deactivate = deactivate;
```
