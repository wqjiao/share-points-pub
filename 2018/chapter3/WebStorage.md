---
title: Web Storage
date: 2018-11-24
categories: web 前端
tags: Web Storage
---
Web Storage 实际上由两部分组成：sessionStorage 与 localStorage...
<!-- more -->

## 概述
* Web Storage 实际上由两部分组成：sessionStorage 与 localStorage。

* 什么是 localStorage、sessionStorage

    在HTML5中，新加入了一个localStorage特性，这个特性主要是用来作为本地存储来使用的，
    
    解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k)，
    
    localStorage中一般浏览器支持的是5M大小，这个在不同的浏览器中localStorage会有所不同。

* localStorage 的优势与局限

    - localStorage的优势

        1、localStorage拓展了cookie的4K限制

        2、localStorage会可以将第一次请求的数据直接存储到本地，这个相当于一个5M大小的针对于前端页面的数据库，相比于cookie可以节约带宽，但是这个却是只有在高版本的浏览器中才支持的

    - localStorage的局限

        1、浏览器的大小不统一，并且在IE8以上的IE版本才支持localStorage这个属性

        2、目前所有的浏览器中都会把localStorage的值类型限定为string类型，这个在对我们日常比较常见的JSON对象类型需要一些转换

        3、localStorage在浏览器的隐私模式下面是不可读取的

        4、localStorage本质上是对字符串的读取，如果存储内容多的话会消耗内存空间，会导致页面变卡

        5、localStorage不能被爬虫抓取到

    - localStorage与sessionStorage的唯一一点区别:
    
        localStorage属于永久性存储，而sessionStorage属于当会话结束的时候，sessionStorage中的键值对会被清空

## userData
* 语法：

    XML  <Prefix: CustomTag ID=sID STYLE="behavior:url('#default#userData')" />

    HTML  <ELEMENT STYLE="behavior:url('#default#userData')" ID=sID>

    Scripting  object .style.behavior = "url('#default#userData')"

    object .addBehavior ("#default#userData")

* 属性:

    expires 设置或者获取 userData behavior 保存数据的失效日期。

    XMLDocument 获取 XML 的引用。

* 方法:

    getAttribute() 获取指定的属性值。

    load(object) 从 userData 存储区载入存储的对象数据。

    removeAttribute() 移除对象的指定属性。

    save(object) 将对象数据存储到一个 userData 存储区。

    setAttribute() 设置指定的属性值。

## sessionStorage
* 作用

    用于本地存储一个会话（session）中的数据，

    这些数据只有在同一个会话中的页面才能访问并且当会话结束后数据也随之销毁。

    因此sessionStorage不是一种持久化的本地存储，仅仅是会话级别的存储。

* 方法

    sessionStorage.getItem(key):获取指定key本地存储的值

    sessionStorage.setItem(key,value)：将value存储到key字段

    sessionStorage.clear():删除所有sessionStorage的数据

    sessionStorage.removeItem(key):删除指定key本地存储的值

* 使用

    sessionStorage 在使用上与 localStorage 的类似。

## localStorage
* 作用

    用于持久化的本地存储，除非主动删除数据，否则数据是永远不会过期的。

* 方法

    localStorage.getItem(key):获取指定key本地存储的值

    localStorage.setItem(key,value)：将value存储到key字段

    localStorage.clear():删除所有的localStorage的数据

    localStorage.removeItem(key):删除指定key本地存储的值

* 使用

    - localStorage 的三种 写入/读取 数据的方法
    ```
    if (！window.localStorage) {
            alert("浏览器支持localstorage");
            return false;
    } else {
        var storage=window.localStorage;
        //写入a字段
        storage["a"] = 1;
        //写入b字段
        storage.a = 1;
        //写入c字段
        storage.setItem("c", 3);
        console.log(typeof storage["a"]);
        console.log(typeof storage["b"]);
        console.log(typeof storage["c"]);
        //第一种方法读取
        var a=storage.a;
        console.log(a);
        //第二种方法读取
        var b=storage["b"];
        console.log(b);
        //第三种方法读取
        var c=storage.getItem("c");
        console.log(c);
    }
    ```

    - localStorage 的删除
        ```
        var storage = window.localStorage;
            storage.a = 1;
            storage.setItem("c", 3);
            console.log(storage);
            storage.removeItem("a"); // 删除localStorage的某个键值对
            storage.clear(); // 删除localStorage的所有内容
            console.log(storage);
        ```

    - localStorage 的键获取
        使用key()方法，向其中出入索引即可获取对应的键
        ```
        var storage = window.localStorage;
            storage.a = 1;
            storage.setItem("c", 3);
            for(var i = 0; i < storage.length; i ++) {
                var key=storage.key(i);
                console.log(key);
            }
        ```
* 特点

    - localStorage 只支持 string 类型的存储，不管写入的是整形还是数组或者对象，最终都是 string 类型的数据

    - 一般我们会将 JSON 存入 localStorage 中，但是在 localStorage 会自动将 localStorage 转换成为字符串形式
    这个时候我们可以使用 JSON.stringify() 这个方法，来将 JSON 转换成为 JSON 字符串
    读取之后要将JSON字符串转换成为JSON对象，使用JSON.parse()方法
    ```
    if (!window.localStorage) {
            alert("浏览器支持localstorage");
    } else {
        var storage = window.localStorage;
        var data = {
            name: 'xiecanyong',
            sex: 'man',
            hobby: 'program'
        };
        var dataArr = [
            {
                name: 'jjj',
                sex: 'man',
                hobby: 'program'
            }, {
                name: 'qqq',
                sex: 'man',
                hobby: 'program'
            }, {
                name: 'www',
                sex: 'man',
                hobby: 'program'
            }
        ];
        var d = JSON.stringify(data);
        var dA = JSON.stringify(dataArr);

        storage.setItem("data", d);
        storage.setItem("dataArr", dA);

        // 将JSON字符串转换成为JSON对象输出
        var json = storage.getItem("data");
        var jsonA = storage.getItem("dataArr");
        var jsonObj = JSON.parse(json);
        var jsonObjA = JSON.parse(jsonA);
        console.log(typeof jsonObj);
        console.log(typeof jsonObjA);

        me.state.data.push(jsonObj); // 将对象push到数组中方便使用
        this.setState({
            data: me.state.data
            dataA: jsonObjA // 数组类型直接遍历
        })
    }
    ```
