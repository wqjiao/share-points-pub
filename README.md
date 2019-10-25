# 知识汇总 * Wqjiao

## gitbook安装与使用

  https://www.cnblogs.com/yangykaifa/p/6707728.html

  1、npm install -g gitbook-cli // 安装gitbook-cli

  2、gitbook -V // 查看gitbook安装是否成功
  
  3、mkdir test_gitbook // 创建gitbook文件夹
  
  4、创建SUMMARY.md 文件
  
  5、gitbook init // 初始化文件目录
  
  6、tree . // 查看建立的文件夹和文件
  
  7、gitbook serve .  // 运行工程 http://localhost:4000

## 常用指令

  gitbook init // 初始化目录文件

  gitbook help // 列出gitbook所有的命令

  gitbook --help // 输出gitbook-cli的帮助信息

  gitbook build // 生成静态网页
  
  gitbook serve // 生成静态网页并运行服务器
  
  gitbook build --gitbook=2.0.1 // 生成时指定gitbook的版本, 本地没有会先下载
  
  gitbook ls // 列出本地所有的gitbook版本
  
  gitbook ls-remote // 列出远程可用的gitbook版本
  
  gitbook fetch 标签/版本号 // 安装对应的gitbook版本
  
  gitbook update // 更新到gitbook的最新版本
  
  gitbook uninstall 2.0.1 // 卸载对应的gitbook版本
  
  gitbook build --log=debug // 指定log的级别
  
  gitbook builid --debug // 输出错误信息

  
## 目录折叠

  - 安装插件 

    插件名称：toggle-chapters

    效果：默认只在目录导航中显示章的标题，而不会显示小节的标题，点击每一章或者每一节会显示当前章或节的子目录，如果有的话，但是同时会收起其它之前展开的章节。

  - 配置

    在根目录(即与SUMMARY.md同级的目录)下的配置文件 book.json(如果没有则新建)中添加插件配置：

    ```
    {
        "plugins": ["toggle-chapters"]
    }
    ```
    
    配置完成后，执行以下操作：

    $ cd gitbook根目录

    $ npm install gitbook-plugin-toggle-chapters (此时gitbook的根目录下的node_modules文件夹中已经有了该插件了)

    $ gitbook build

    $ gitbook serve

    访问 http://localhost:4000 查看插件是否已经生效。

  - 参考网址

    [目录折叠](https://yunchangwang.github.io/2018/01/28/gitbook%E7%9B%AE%E5%BD%95%E6%8A%98%E5%8F%A0/)

    [插件安装](http://www.chengweiyang.cn/gitbook/plugins/functional/toggle-chapters.html)
    
## gitbook install
