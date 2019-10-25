# 异步请求接口数据

## ajax 请求

* 介绍：
    - Ajax的全称是 Asynchronous JavaScript and XML，意思就是用 JavaScript 执行异步网络请求。

    - ajax()方法通过 HTTP 请求加载远程数据，该方法是 jQuery 底层 AJAX 实现。
    
    - 简单易用的高层实现见 $.get, $.post 等。$.ajax() 返回其创建的 XMLHttpRequest 对象。
    
    - 大多数情况下你无需直接操作该函数，除非你需要操作不常用的选项，以获得更多的灵活性。
    最简单的情况下，$.ajax() 可以不带任何参数直接使用。
    
* 用法：jQuery.ajax([settings])

* 注意：
    所有的选项都可以通过 $.ajaxSetup() 函数来全局设置，如果仔细观察一个Form的提交，你就会发现，
    一旦用户点击“Submit”按钮，表单开始提交，浏览器就会刷新页面，然后在新页面里告诉你操作是成功了还是失败了。
    如果不幸由于网络太慢或者其他原因，就会得到一个404页面。这就是Web的运作原理：一次HTTP请求对应一个页面。
    如果要让用户留在当前页面中，同时发出新的HTTP请求，就必须用JavaScript发送这个新请求，接收到数据后，再用JavaScript更新页面，
    这样一来，用户就感觉自己仍然停留在当前页面，但是数据却可以不断地更新。
    最早大规模使用AJAX的就是Gmail，Gmail的页面在首次加载后，剩下的所有数据都依赖于AJAX来更新。
    用JavaScript写一个完整的AJAX代码并不复杂，但是需要注意：AJAX请求是异步执行的，也就是说，要通过回调函数获得响应。

* 跨域问题：
    Access-Control-Allow-Origin 设置为*(即通配符)，或者本域的名称时，可解决跨域请求，origin表示本域，即当前页面的域

* $.when().done(function() {}).fail(function() {})

    - 用法：
        ``` 
        $.when($.ajax("test1.html"), $.ajax("test2.html"))
            .done(function(){ alert("哈哈，成功了！"); })
            .fail(function(){ alert("出错啦！"); });
        ```
    - 先执行两个操作$.ajax("test1.html")和$.ajax("test2.html")，如果都成功了，就运行done()指定的回调函数；如果有一个失败或都失败了，就执行fail()指定的回调函数
        ```
        注释：
            .done(function() {}) => success函数
            .fail(function() {}) => error函数
        ```
    - 参考网址： https://www.cnblogs.com/tiancai/p/5817996.html

## axios 请求

* 