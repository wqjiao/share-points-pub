---
title: 前端代码对比
date: 2018-12-08
categories: web 前端
tags: Javascript
---
前端代码对比: 包括 宽松相等 & 严格相等、React 中 click 事件对比、事件流、函数A和函数B，实现B继承A、浏览器窗口大小改变、指定页面返回最顶部的多种方法等等...
<!-- more -->

## 1、宽松相等 & 严格相等

* 宽松相等

    ```
    const a = { value : 0 };
    a.valueOf = function() {
        return this.value += 1;
    };

    console.log(a == 1 && a == 2 && a == 3); // true
    ```
    
注意：
    宽松相等 == 会先将左右两两边的值转化成相同的原始类型，然后再去比较他们是否相等。
    在转化之后( == 一边或两边都需要转化)，最后的相等匹配会像 === 符号一样去执行判断。
    宽松相等是可逆的，对于任何值 A 与 B，通常 A == B 与 B == A 具有相同的表现(除了转换的顺序不同)。

    ToPrimitive(input, PreferredType?)
        
可选参数 PreferredType 可以指定最终转化的类型，它可以是 Number 类型或 String 类型，
这依赖于 ToPrimitive() 方法执行的结果返回的是 Number 类型或 String 类型

* 严格相等

    ```
    var value = 0; //window.value
    Object.defineProperty(window, 'a', {
        get: function() {
            return this.value += 1;
        }
    });

    console.log(a===1 && a===2 && a===3) // true
    ```
    
* 类型固定时，宽松相等 与 严格相等

    ```
    var value = 0;
    const a = {
        get: function() {
            return this.value += 1;
        }
    }

    console.log((0, a.get)() == 1 && (0, a.get)() == 2 && (0, a.get)() == 3); // true
    console.log((0, a.get)() === 1 && (0, a.get)() === 2 && (0, a.get)() === 3); // true
    ```

* Object.defineProperty

    - 语法
    
        Object.defineProperty(obj, prop, descriptor)
    
    - 参数
    
        obj 用于定义属性的对象。
    
        prop Symbol 要定义或修改的名称或属性。
    
        descriptor 正在定义或修改属性的描述符。
    
    - 返回值
    
        传递给函数的对象

* 参考链接
    - [gist.github](https://gist.github.com/anubhavsrivastava)
    - [掘金](https://juejin.im/post/5bfcc632f265da61493353cc)
    - [web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)

## 2、React 中 click 事件对比

类的方法默认不会自动绑定 this，在调用时如果忘记绑定 this，那么 this 的值将会是 undefined。
通常如果不是直接调用，应该为方法绑定 this。绑定方式有以下几种：

* 1、在 onClick 时使用匿名(箭头)函数绑定
    ```
    <button onClick={() => this.handleClick()}>按钮</button>

    <!-- 传参 -- 该方法 点击 时才会执行 -->
    <button onClick={() => this.handleClick(data)}>按钮</button>
    ```

* 2、在 onClick 时使用 bind 绑定 this

Function.prototype.bind 来为事件处理函数传递参数
    ```
    handleClick() {
        alert('我点击了按钮');
    }
    ...
    <button onClick={this.handleClick.bind(this)}>按钮</button>

    <!-- 传参 -- 该方法 点击 时才会执行 -->
    <button onClick={this.handleClick.bind(this)}>按钮</button>
    ```

* 3、在构造函数中使用 bind 绑定上下文

    ```
    constructor(props) {
        super(props);
        this.handleClick = this.handleClick.bind(this);
    }
    ...
    handleClick() {
        alert('我点击了按钮');
    }
    ...
    <button onClick={this.handleClick}>按钮</button>

    <!-- 传参 -- 该方法在 render 时会直接被执行 -->
    <button onClick={this.handleClick(data)}>按钮</button>
    ```

* 4、使用属性初始化器语法(箭头函数)绑定 onClick

    ```
    handleClick = () => {
        alert('我点击了按钮');
    }
    ...
    <button onClick={this.handleClick}>按钮</button>

    <!-- 传参 -- 该方法在 render 时会直接被执行 -->
    <button onClick={this.handleClick(data)}>按钮</button>
    ```

* 总结

    - 方式1 和 方式2：
        
        在 onClick 时绑定this，影响性能，且当方法作为属性传递给子组件的时候会引起重渲问题

        优点：写法比较简单，当组件中没有 state 的时候就不需要添加类构造函数来绑定 this，`传参 -- 该方法 点击 时才会执行`

        缺点：每一次调用的时候都会生成一个新的方法实例，因此对性能有影响，且当这个函数作为属性值传入低阶组件的时候，这些组件可能会进行额外的重新渲染，因为每一次都是新的方法实例作为的新的属性传递。

    - 方式3：
        
        在类构造函数中绑定 this，调用的时候不需要再绑定，官方推荐的绑定方式，性能最好的方式

        优点：只会生成一个方法实例，并且绑定一次之后如果多次用到这个方法也不需要再绑定。

        缺点：即使不用到 state，也需要添加类构造函数来绑定 this，代码量多。

    - 方式4：
        
        利用属性初始化语法，将方法初始化为箭头函数，因此在创建函数的时候就绑定了 this，最好的方式

        优点：创建方法就绑定 this，不需要在类构造函数中绑定，调用的时候不需要再作绑定，结合了前三种方式的优点。
        
        缺点：需要用 babel 转译

    - 方式3 和 方式4：
        
        `共同缺点：传参 -- 该方法在 render 时会直接被执行`

    - 传参
    
        向方法中传参时，使用 方法1 箭头函数(arrow functions) 和 方法2 bind(Function.prototype.bind)

        事件对象 e 要放在最后 handleClick(data, e)，作为第二个参数传递

        通过箭头函数的方式，事件对象必须显式的进行传递，但是通过 bind 的方式，事件对象以及更多的参数将会被隐式的进行传递

## 3、事件流 -- 捕获事件流、冒泡事件流

* 事件流分为两种，捕获事件流和冒泡事件流。
    - 捕获事件流从根节点开始执行，一直往子节点查找执行，直到查找执行到目标节点。
    - 冒泡事件流从目标节点开始执行，一直往父节点冒泡查找执行，直到查到到根节点。
    
* 事件流分为三个阶段，一个是捕获节点，一个是处于目标节点阶段，一个是冒泡阶段。

## 4、函数A和函数B，实现B继承A

* 方法
    ```
    // 方式1
    function B() {}
    function A() {}
    B.prototype = new A();

    // 方式2
    function A() {}
    function B() {
        A.call(this);
    }

    // 方式3
    function B() {}
    function A() {}
    B.prototype = new A();

    function B() {
        A.call(this);
    }
    ```
* 特点

    方式1：简单易懂，但是无法实现多继承，父类新增原型方法/原型属性，子类都能访问到

    方式2：可以实现多继承，但是只能继承父类的实例属性和方法，不能继承原型属性/方法

    方式3：可以继承实例属性/方法，也可以继承原型属性/方法，但是示例了两个A的构造函数


* 参考链接

    [掘金](https://juejin.im/post/5befeb5051882511a8527dbe)

## 5、浏览器窗口大小改变

* window.onresize

    直接给window的onresize属性绑定事件，只能有一个。也就是说后面的会覆盖前面

        window.onresize = function() {
            console.log('操作1');
        }
        window.onresize = function() {
            console.log('操作2');
        }

当浏览器窗口大小发生改变时，只会输出'操作2'

* $(window).resize

    - 作用

        当调整浏览器窗口的大小时，发生 resize 事件。可以写多个 $(window).resize 方法

        resize() 方法触发 resize 事件，或规定当发生 resize 事件时运行的函数。

        ```
        $(window).resize(function(){
            console.log("操作3")
        });
        $(window).resize(function(){
            console.log("操作4")
        });
        ```

        当浏览器窗口大小发生改变时，会输出'操作3' '操作4'

        浏览器窗口大小改变时，这段代码会执行多次，有时我们可能想要处理比较复杂的逻辑，会对性能影响较大，这样就比较容易造成浏览器假死。 
        
        如何实现不管窗口如何改变，只在停止改变之后才执行代码呢？

        ```
        var resizeTimer = null;
        $(window).bind('resize', function (){
            if (resizeTimer) clearTimeout(resizeTimer);
            resizeTimer = setTimeout(function(){
                console.log("窗口发生改变了哟！");
            } , 100);
        });
        ```
        通过增加定时器的方式来让代码延迟执行，这样每次窗口改变的时候，我们都清除事件，只有当他停下来之后，才会继续执行。

    - 使用
        
        触发 resize 事件 $(selector).resize()
        
        将函数绑定到 resize 事件 $(selector).resize(function)

## 6、指定页面返回最顶部

* 1、jquery

    ```
    $('html, body').animate({scrollTop: 0}, 1000);
    ```

* 2、使用a标签锚点定位
    ```
    <a href="#toTop" target="_self">返回顶部</a>
    ```

* 3、使用JS scrollTo 函数
    - javascript scroll 函数（scrollBy scrollTo）是用来滚动页面到指定位置，格式定义如下：

        ```
        ■scrollBy(xnum,ynum)
        ■scrollTo(xpos,ypos)
        ```

    - 其中：

        ■xnum，ynum 分别指水平、垂直滚动多少像素，正值表示向右或向下滚动，负值表示向左或向上滚动
        
        ■xpos，ypos 分别指水平和垂直坐标

    - 示例：

        ```
        <a href="javascript: scrollTo(0, 0);">返回顶部</a>
        ```
        ```
        <div id="divId">
            有对应id的话, 滚动到相应位置，没有的话，滚动到头部
            有对应id的话, 滚动到相应位置，没有的话，滚动到头部
            有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部有对应id的话, 滚动到相应位置，没有的话，滚动到头部
        </div>
        <button id="test" style="position:fixed;right:0;bottom:0">回到顶部</button>
        <script>
            let test = document.getElementById('test');
            let divId = document.getElementById('divId');
            test.onclick = function(){
                console.log('divId.offsetTop', divId.offsetTop)
                scrollTo(0, divId.offsetTop);
            }
        </script>
        ```

* 4、scrollBy 慢速滚动返回顶部

    本方式使用上面提到的 scrollBy 函数，每次只滚动定量像素，整体看起来有点滚动效果，代码如下：

    - 代码如下:
        ```
        var sdelay = 0;
        function returnTop() {
            window.scrollBy(0, -100); // Only for y vertical-axis
            if(document.body.scrollTop > 0) { 
                sdelay = setTimeout('returnTop()', 50);
            }
        }

        ||

        var top = document.body.scrollTop || document.documentElement.scrollTop
            scrollBy(0,-top);
        }
        ```
    - scrollBy 函数第二个参数我设了-100，越大（比如-10）滚动越慢，越小滚动越快，启动滚动只需在页面底部加个链接：
        ```
        <a href="javascript:returnTop();">返回顶部</a>
        ```
* 5、scrollTop

　　scrollTop 属性表示被隐藏在内容区域上方的像素数。元素未滚动时，scrollTop 的值为0，
    如果元素被垂直滚动了，scrollTop 的值大于0，且表示元素上方不可见内容的像素宽度

　　由于 scrollTop 是可写的，可以利用 scrollTop 来实现回到顶部的功能
    ```
    document.body.scrollTop = document.documentElement.scrollTop = 0;
    ```

* 6、scrollIntoView()

　　Element.scrollIntoView方法滚动当前元素，进入浏览器的可见区域　

　　该方法可以接受一个布尔值作为参数。
    如果为true，表示元素的顶部与当前区域的可见部分的顶部对齐（前提是当前区域可滚动）；
    如果为false，表示元素的底部与当前区域的可见部分的尾部对齐（前提是当前区域可滚动）。
    如果没有提供该参数，默认为true

　　使用该方法的原理与使用锚点的原理类似，在页面最上方设置目标元素，当页面滚动时，
    目标元素被滚动到页面区域以外，点击回到顶部按钮，使目标元素重新回到原来位置，则达到预期效果
    ```
    document.getElementById('target').scrollIntoView();
    ```
    
## 7、jQuery.extend( [deep ], target, object1 [, objectN ] );

* 描述： 将两个或更多对象的内容合并到第一个对象

    [deep ] 取值true/false,表示是否为深拷贝，默认为false(浅拷贝)

    target {} 目标对象
    
    object1 第一个被合并的对象
    
    objectN 第N个被合并的对象

    ```
    let state = {
        id: 1111,
        list: [
            {
                id: 0,
                name: 'name0'
            },
            {
                id: 1,
                name: 'name1'
            }
        ]
    }
    let data = {
        list: [
            {
                id: 0,
                name: 'name2'
            }
        ]
    }
    <!-- 结果进行了合并 -->
    $.extend(true, {}, state, data); // {id: 1111, list: [{id: 0, name: 'name2'},{id: 1, name: 'name1'}]}
    ```

## 8、文字与背景反色

* 问题
    有一个进度条，进度条中间有一串文字，当我的进度条覆盖了文字之后，文字要与进度条反色，怎么实现？

* 解决方案
    - 1、使用js,在进度条宽度变化的时候，计算盖过每一个文字的50%，如果超过，设置文字相反颜色；
    - 2、使用css中的mix-blend-mode属性
    - 3、使用html,两个相同位置但是颜色相反的dom结构在重叠在一起，顶层覆盖底层，最顶层的进度条取overflow为hidden，其宽度就为进度

## 9、为什么需要复制数组，才能使用 .map() 方法？

### .map()方法

* 语法
    ```
    var new_array = arr.map(callback(currentValue[, index [, array]]) {
        //返回new_array的元素
    } [, thisArg ])
    ```

* 参数
    
    callback
    产生新数组元素的函数，带有三个参数：
        
        currentValue 当前元素在数组中处理。
        index 可选的 数组中正在处理的当前元素的索引。
        array 可选的 该阵列map被召唤。
        thisArg 可选的 this 执行时要使用的值 callback。

* 返回值

    一个新数组，每个元素都是回调函数的结果。

* 描述
    
    map 按顺序为数组中的每个元素调用一次提供的 callback 函数，并从结果中构造一个新数组。仅对已分配值的数组的索引（包括 [undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)）调用。它不会被调用缺少数组的元素（即，从未设置过的索引，已删除的索引或从未赋值的索引）。

    由于map构建了一个新数组，因此在不使用返回数组时使用它是一种反模式; 使用[forEach](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/forEach)或[for-of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of)代替。标志你不应该使用map：A）你没有使用它返回的数组，和/或B）你没有从回调中返回一个值。
    
    callback 使用三个参数调用：元素的值，元素的索引和遍历的Array对象。
    
    如果thisArg提供了参数map，则它将用作回调的this值。否则，该值[undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)将用作其this值。this最终可观察到的值callback是根据[确定this函数所见的通常规则确定的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/this)。
    
    map不会改变callback调用它的数组（虽然，如果调用，可能会这样做）。
    
    处理的元素范围map在第一次调用之前设置callback。在调用map开始后附加到数组的元素将不会被访问callback。如果更改了数组的现有元素，则传递给它们的值callback将是map访问它们时的值。map 在访问开始之后和访问之前删除的元素  不会被访问。
    
    由于规范中定义的算法，如果调用的映射数组是稀疏的，则生成的数组也将是稀疏的，保持相同的索引为空。

### 使用 Array()，定义一个数量固定条目未定的数组
    Array(2); // [empty × 2]

### 使用 .map() 方法遍历数量固定条目未定的新数组
    Array(2).map( () => "foo");  // [empty × 2]

### 复制该数组后，使用 .map() 方法
    [...Array(2)].map( () => "foo");  // ["foo", "foo"]

### Answers
* 1、使用 Array(arrayLength) 创建数组：一个新的 js 数组，其 length 属性设置为该数字

    注意：这意味着 arrayLength 空插槽的数组，而不是具有实际未定义值的插槽
    
    该数组实际上不包含任何值，甚至不包含undefined值 - 它只是一个 length 属性。

    当您将具有 length 属性的项扩展到数组中时，例如[...{ length: 4 }]，扩展语法访问每个索引并在新数组中设置该索引处的值
    ```
    const arr1 = [];
    arr1.length = 4;
    // 该数组中不包含任何的index索引值
    console.log('1' in arr1);

    const arr2 = [...arr1];
    console.log(arr2);
    console.log('2' in arr2);
    ```
    并且 .map 只映射属性实际存在于要映射的数组中的属性/值。

    使用数组构造函数令人困惑。我建议使用 Array.from，从头开始创建数组时 - 你可以传递一个带有 length 属性的对象，以及一个映射函数
    ```
    const arr = Array.from(
        { length: 2 },
        () => 'foo'
    );
    console.log(arr);
    ```

* 2、数组元素未分配。仅对已分配值的数组索引（包括undefined）调用 callback
    ```
    var array1 = Array(2);
    array1[0] = undefined;

    // pass a function to map
    const map1 = array1.map(x => x * 2);

    console.log(array1); // [undefined, empty]
    console.log(map1); // [NaN, empty]
    ```

* 参考链接
    - [stackoverflow](https://stackoverflow.com/questions/53491943/why-do-i-need-to-copy-an-array-to-use-a-method-on-it)
    - [web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map)


## 10、查看端口号是否被占用

    netstat -ano 列出所有端口的情况

    netstat -aon|findstr "8081" 查看被占用端口对应的PID 1844

    tasklist|findstr "1844" 查看是哪个进程或者程序占用了 1844 端口 -- TeamViewer
    
    taskkill /f /t /im Tencentdl.exe 结束该进程

## 11、四则运算符

### Number 与 String 转换

* Number 转换成 String

    '' + 1 => '1'
    1.toString()

    性能上来说：('' +) > String() > .toString() > new String()

* String 转换成 Number

    + '1'; // + 连接操作符
    ~~'15'; // 15 ~~ 双非运算符，返回一个整数(~按位非运算)
    Number('1');
    parseFloat('1');
    parseInt('1');

另外，可以使用 `+` 运算实现 `boolean` 类型转换成 `number`:
    
    +true; // 1
    +false; // 0

* 浮点数 Float 转换成整型 Int

最常用的是 parseInt()，其实 parseInt() 是用于将字符串转换成数字，而不是浮点数和整型之间的转换，可以使用 Math.ceil()、 Math.floor() 或者 Math.round()。

    Math.ceil(num)  数值上取整
    Math.floor(num) 数值下取整
    Math.round(num) 数值四舍五入

### 字符串链接

如果要连接多个字符串，应该少使用 +=，如：

    x += a;
    x += b;
    x += c;

    应该写成 x+= a + b + c;

而如果是收集字符串，比如多次对同一个字符串进行 += 操作的话，最好使用一个缓存，使用 JavaScript 数组来收集，最后使用 join 方法连接起来。

### 0 和 1 转换成 Boolean

falsy: 0 '' null undefined NaN false

truthy: 1 '***' [] {} true

```js
const isTrue  = !0;
const isFalse = !1;
const alsoFalse = !!0;
console.log(isTrue); // Result: true
console.log(typeof true); // Result: "boolean"          
```

### 指数运算符 **

三种方法：
```js
// Math.pow 方法
Math.pow(2, 3); // 8
// 指数运算符 **
console.log(2 ** 3); // 8 --更快
// 按位左移操作符
console.log(2 << (3 - 1)); // 8
```

## 删除最后一个数字

```js
let str = '1553';
Number(str.substring(0, str.length - 1)); // 155
console.log(1553 / 10   | 0); // 155
```

## 12、es6 class 的 new 实例和 es5 的 new 实例有什么区别
摘自 阮一峰翻译的 《ECMAScript 6 入门》中 “Class 的基本用法”

* ES5 语法 与 ES6 语法比较

ES5 写法：
```javascript
function Point(x, y) {
    this.x = x;
    this.y = y;
}

Point.prototype.toString = function () {
    return '(' + this.x + ', ' + this.y + ')';
};

// 使用 new
var p = new Point(1, 2); // {x: 1, y: 2}
p.toString(); // (1, 2)
```

ES6 写法：
```javascript
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}

typeof Point // "function"
Point === Point.prototype.constructor // true

// 使用 new
let point = new Point(1, 2);
point.toString() // (1, 2)

point.hasOwnProperty('x') // true
point.hasOwnProperty('y') // true
point.hasOwnProperty('toString') // false
point.__proto__.hasOwnProperty('toString') // true

// 以下三种结果一致
Point.__proto__.__proto__
Point.prototype.__proto__
point.__proto__.__proto__
/*
    constructor: ƒ Object()
    hasOwnProperty: ƒ hasOwnProperty()
    isPrototypeOf: ƒ isPrototypeOf()
    propertyIsEnumerable: ƒ propertyIsEnumerable()
    toLocaleString: ƒ toLocaleString()
    toString: ƒ toString()
    valueOf: ƒ valueOf()
    __defineGetter__: ƒ __defineGetter__()
    __defineSetter__: ƒ __defineSetter__()
    __lookupGetter__: ƒ __lookupGetter__()
    __lookupSetter__: ƒ __lookupSetter__()
    get __proto__: ƒ __proto__()
    set __proto__: ƒ __proto__()
*/
```
上面代码表明，类的数据类型就是函数，类本身就指向构造函数。
`x`和`y`都是实例对象`point`自身的属性（因为定义在`this`变量上），所以`hasOwnProperty`方法返回`true`，而`toString`是原型对象的属性（因为定义在`Point`类上），所以`hasOwnProperty`方法返回`false`。这些都与 ES5 的行为保持一致。

* ES6 语法 => 对应 ES5 语法

```javascript
class Point {
  constructor() {}
  toString() {}
  toValue() {}
}

// 等同于
function Point() {}
Point.prototype = {
  constructor() {},
  toString() {},
  toValue() {},
};
```

需要注意的是`constructor`方法是类的默认方法，通过`new`命令生成对象实例时，自动调用该方法。一个类必须有`constructor`方法，如果没有显式定义，一个空的`constructor`方法会被默认添加。

* 在类的实例上面调用方法，其实就是调用原型上的方法

```javascript
class B {}
let b = new B();

b.constructor === B.prototype.constructor // true
```

上面代码中，`b`是`B`类的实例，它的`constructor`方法就是`B`类原型的`constructor`方法

* Object.assign(Point.prototype, {})

由于类的方法都定义在`prototype`对象上面，所以类的新方法可以添加在`prototype`对象上面。`Object.assign`方法可以很方便地一次向类添加多个方法。

```javascript
class Point {
    constructor() {}
}

Object.assign(Point.prototype, {
    toString(){},
    toValue(){}
});
```

`prototype`对象的`constructor`属性，直接指向“类”的本身，这与 ES5 的行为是一致的。

```javascript
Point.prototype.constructor === Point // true
```

* 类的内部所有定义的方法，都是不可枚举的（non-enumerable）

```javascript
class Point {
  constructor(x, y) {}
  toString() {}
}

Object.keys(Point.prototype); // []
Object.getOwnPropertyNames(Point.prototype); // ["constructor","toString"]
```

上面代码中，`toString`方法是`Point`类内部定义的方法，它是不可枚举的。这一点与 ES5 的行为不一致。

```javascript
var Point = function (x, y) { };

Point.prototype.toString = function() {};

Object.keys(Point.prototype); // ["toString"]
Object.getOwnPropertyNames(Point.prototype); // ["constructor","toString"]
```

上面代码采用 ES5 的写法，`toString`方法就是可枚举的。

* 类的属性名，可以采用表达式

```javascript
let methodName = 'getArea';

class Square {
    constructor(length) {}
    [methodName]() {}
}
```

* ES5 Class 与 ES6 生成类的实例对象一样，使用`new`命令

ES5 写法：
```javascript
function Point(x, y) {
    this.x = x;
    this.y = y;
}

Point.prototype.toString = function () {
    return '(' + this.x + ', ' + this.y + ')';
};

// 使用 new
var p = new Point(1, 2); // {x: 1, y: 2}
p.toString(); // (1, 2)
```

ES6 写法：
```javascript
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}

// 使用 new
let point = new Point(1, 2);
point.toString() // (1, 2)
```

## 13、__proto__ 与 prototype 的区别

* 使用 ES6 中的 Class 生成类的实例

```javascript
class Point {
    constructor(x, y) {
        this.x = x;
        this.y = y;
    }

    toString() {
        return '(' + this.x + ', ' + this.y + ')';
    }
}

let point = new Point(1, 2);
// 以下三种结果一致
Point.__proto__.__proto__
Point.prototype.__proto__
point.__proto__.__proto__
/*
    constructor: ƒ Object()
    hasOwnProperty: ƒ hasOwnProperty()
    isPrototypeOf: ƒ isPrototypeOf()
    propertyIsEnumerable: ƒ propertyIsEnumerable()
    toLocaleString: ƒ toLocaleString()
    toString: ƒ toString()
    valueOf: ƒ valueOf()
    __defineGetter__: ƒ __defineGetter__()
    __defineSetter__: ƒ __defineSetter__()
    __lookupGetter__: ƒ __lookupGetter__()
    __lookupSetter__: ƒ __lookupSetter__()
    get __proto__: ƒ __proto__()
    set __proto__: ƒ __proto__()
*/
```

* 函数皆对象，函数与对象的`__proto__`一样

```javascript
var func = function () {};
var obj ={};
console.log(func.__proto__.__proto__)
console.log(obj.__proto__)
```

js里所有的对象都有proto属性(对象，函数)，指向构造该对象的构造函数的原型。
只有函数function才具有prototype属性。这个属性是一个指针，指向一个对象，这个对象的用途就是包含所有实例共享的属性和方法（我们把这个对象叫做原型对象）。原型对象也有一个属性，叫做constructor，这个属性包含了一个指针，指回原构造函数。

### constructor 构造函数

`constructor` 是一种用于创建和初始化class创建的对象的特殊方法。

## 14、定时器

定时器： setTimeout、setInterval

如果针对的是不断运行的代码，不应该使用 setTimeout，而应该是用 setInterval，因为 setTimeout 每一次都会初始化一个定时器，而 setInterval 只会在开始的时候初始化一个定时器。

## 15、声明多个变量

在 js 代码中会出现声明多个变量的情况，此时可以使用一个 let/var 语句来同时声明多个变量，以减少整个脚本的执行时间，且代码格式也相对规范。

```javascript
let a = 1;
let b = 2;
let c = 3;

// 可以将以上代码替换成
let a = 1,
    b = 2,
    c = 3;
```

## 16、插入迭代器

使用迭代器合并多条代码
```javascript
let a = arr[i];

i++;

// 上述代码合并成
let a = arr[i++];
```

## 17、同一个数据被重复调用时，可预先使用变量进行保存

```javascript
let queryOptions = me.props.queryOptions;

a = queryOptions.a;
b = queryOptions.b;
```

## 18、switch 相较于 if 快

通过将 case 语句按照最可能到最不可能的顺序进行组织
```javascript
let result = '';

switch(a) {
    case 1:
        result = '1';
        break;
    case 2:
        result = '2';
        break;
    default:
        result = '3';
        break;
}

return result;
```

## 19、何时用单引号，何时用双引号

虽然在 JavaScript 当中，双引号和单引号都可以表示字符串, 为了避免混乱，我们建议在HTML中使用双引号，在JavaScript中使用单引号，但为了兼容各个浏览器，也为了解析时不会出错，定义JSON对象时，最好使用双引号。

## 20、String.split('') && Array.join('')

`String.split('')` 与 `Array.join('')` 是相反的两种方法

* `String.split('')` 分割字符串

stringObject.split(separator, howmany)
separator -- 必需，表示从该参数指定的地方进行分割，如果提供空字符串作为该参数，则stringObject中的每个字符之间都会被分割。
howmany -- 可选，该参数指定返回的数组的最大长度，但不会多于该参数指定的数组长度。

```js
var str = "1,2,3"; // 需要注意字符串中间的空格
str.split(','); // [1, 2, 3]
str.split(',', 5); // [1, 2, 3]
str.split(',', 2); // [1, 2]
```

* `Array.join('')` 元素通过指定分隔符分割成字符串

Array.join(separator)
separator -- 可选，分隔符，表示使用该分隔符进行分割，不提供该参数时，默认使用 `,` 进行分割

```js
var arr = [1, 2, 3];
arr.join(); // "1,2,3"
```

## 21、target 与 currentTarget 区别

currentTarget 始终是指向时间绑定的元素(监听事件者);
target 指向用户触发的元素(真正发出者)。

## 22、Promise.prototype​.catch 与 try ... catch

### Promise.prototype​.catch

`Promise​.prototype​.catch()` 与 `Promise​.prototype​.then(onReolved, onRejected)` 中的第二个参数 `onRejected`

若果 `then` 中抛出异常，在异常代码后的程序将不会再执行，直接进入 `catch` 中处理，避免了程序因为异常问题崩溃。

```js
const getRandom = () => +(Math.random() * 1000).toFixed(0);
new Promise((resolve, reject) => {
    let timeout = getRandom();
    
    setTimeout(() => {
        if (timeout > 200) {
            resolve('success');
        } else {
            reject('fail')
        }
    }, timeout);
}).then((data) => {
    // 接口执行成功 -- resolved
    console.log('resolved:', data);
    // 未定义参数 someArg -- catch error
    console.log('resolved:', someArg);
    // throw '异常';
    // throw new Error('异常');
    console.log('异常之后的代码')
}).catch(err => {
    console.log('rejected:', err);
});
```

### try...catch

`try...catch` 是 `Javascript` 的异常处理机制。将可能出现错误的代码放在 `try` 中执行，避免抛出异常时，程序崩溃。

* 定义
    `try...catch` 语句将能引发错误的代码放在 `try` 块中，并且对应一个响应，然后有异常被抛出。

* 三种形式的 `try` 声明

    ```
    try ... catch
    try ... finally
    try ... catch ... finally
    ```

    - `catch` 在 `try` 中抛出异常或者数据发生错误时，执行;
    - `finally` 在 `try` `catch` 执行完后执行，不管前面的代码是否有异常抛出，都会执行该层代码;
    - 如果在 `try` 中有异常抛出，则 `try` 中抛出异常后面的代码将不会再执行，直接到 catch 中进行错误分析，最后执行 `finally`。

    ```js
    try {
        // throw 语句的作用是手动中断程序执行，抛出一个错误
        throw '异常';
        throw new Error('异常');
    } catch (e) {
        console.log('catch:', e);
        console.log('catch:', e.message);
    } finally {
        console.log('finally');
    }
    ```

## 23、ajax、fetch、axios

## 24、交换参数值方法

```js
let a = 2,
b = 99;

// 方法1 -- 解构
[a, b] = [b, a];

// 方法2 -- 空变量交换
let temp = a;
    a = b;
    b = temp;

// 方法3 -- 类型通用
b = [a, a = b][0];

// 方法4 -- 数字加减法(数字，数字字符串，布尔值)
a = a + b;
b = a - b;
a = a - b;

// a -> 99; b-> 2s
```

## 25、- + ~~

* 一元负号 `-`

一元负号运算符位于操作数前面，并转换操作数的符号
```js
-'55' -> -55
-(-'55') -> 55
```
注意：一元负号会将数值变成负数，如果不想变成负数，需要在前面再添加一个 `-`(注意运算符优先级)。

* 一元正号 `+`

一元正号运算符位于其操作数前面，计算其操作数的数值，如果操作数不是一个数值，会尝试将其转换成一个数值。 尽管一元负号也能转换非数值类型，但是一元正号是转换其他对象到数值的最快方法，也是最推荐的做法，因为它不会对数值执行任何多余操作。它可以将字符串转换成整数和浮点数形式，也可以转换非字符串值 true，false 和 null。小数和十六进制格式字符串也可以转换成数值。负数形式字符串也可以转换成数值（对于十六进制不适用）。如果它不能解析一个值，则计算结果为 NaN。
```js
+'55' -> 55
```

* 双非运算符 `~~`

`~~` 返回一个整数(~按位非运算)
```js
~~'15' -> 15
```
