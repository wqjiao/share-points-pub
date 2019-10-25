# javascript

## 一、input text select() 方法

`select()` 方法用于选取文本域中的内容所有主流浏览器都支持 `select()` 方法 `textObject.select()`

选取文本域的内容：
    ```html
    <input type="text" id="copyText" value="需要复制的内容">
    <button type="button" onClick="handleClick()">复制</button>
    <script>
        function handleClick() {
            document.getElementById("copyText").select();
            document.execCommand('copy');
        }
    </script>
    ```

## 二、this 指向

### 非箭头函数 this

没有箭头函数之前，我们说this就是函数运行时所在的环境对象，但是在箭头函数中this就是定义时所在的对象，先说大家熟知的：函数运行时所在的环境对象。

* 1、作为函数调用，this指向全局对象

```javascript
var q = 'window'
var func = function {
    console.log(this.q)
}

func()   // window
```

* 2、作为对象的方法调用，该对象即为调用上下文，this指向该对象。

```javascript
var q = 'window'
var func = function() {
    console.log(this.q)
}

var obj = {
    q: 'obj',
    func: func,
    anotherObj: {
        q: 'anotherObj',
        func: func
    }
}

obj.func()   // obj
obj.anotherObj.func() // anotherObj
```

* 3、作为构造函数调用，构造函数试图初始化这个新创建的对象，并将这个对象作为其调用上下文，this 指向这个新创建的对象。

```javascript
var q = 'window'

function Func() {
    this.q = 'Func'
    console.log(this.q)
}

var obj = new Func  // Func

console.log(this.q)  // window
```

* 4、通过函数的call/apply方法间接调用, call/apply 方法的第一个参数是调用上下文，在函数体内，通过 this 获得对它的引用。

```javascript
var q = 'window';

function func() {
　console.log(this.q)
}

var obj = {
 q: 'obj'
}

func.apply()  // window
func.call() // window

func.apply(obj) // obj
func.call(obj) // obj
```

### 箭头函数 this

箭头函数中 this 对象就是定义时所在的作用域，也就是说箭头函数本身没有 this，内部的 this 就是外层代码块作用域中的 this。

* 1、独立函数

```javascript
var a = 0
var test = ()=> {
    var a = 1
    console.log(this.a)
}

test()  // 0
```

该箭头函数在全局环境中定义，即this指向window

* 2、对象的方法

```javascript
var a = 0
var obj = {
    a: 1,
    foo: ()=> {
        console.log(this.a)
    }
}

obj.foo()  // 0
```

即：
```javascript
var a = 0
var obj = new Object
obj.a = 1
obj.foo = ()=> {
    console.log(this.a)
}
obj.foo();
```
如上所示，foo在全局中定义,所以this指向window，那么如何使this指向obj?

根据上一篇介绍，当函数作为对象的方法调用时this指向该对象，可以这样改写：

```javascript
var a = 0
function foo(){
    var func = () => {
        console.log(this.a)
    }
    return func
}
var obj = {
    a : 1,
    foo:foo
}
obj.foo()()  // 1
```
func在foo调用时定义，此时的foo所在作用域为obj，因此this指向obj

* 3、构造函数,因箭头函数没有this,固不能用作构造函数，否则会报错

```javascript
var foo = ()=> {
    console.log(this)
}
var boo = new foo()  //foo is not a constructor
```

* 4、bind/call

```javascript
var a = 0

var func = ()=> {
    console.log(this.a)
}

var obj = {
    a: 1
}

func.call(obj)  // 0
```

如上：func定义在全局，因此打印0，同对象方法，我们可以通过如下改写，打印出1

```javascript
var a = 0

var func = function() {
    var boo = ()=> {
        console.log(this.a)
    }
    return boo
}

var obj = {
    a: 1
}

func.call(obj)() // 1
```

## 三、QRCode.js 生成二维码

* 直接引入 cdn

QRCode.js 是一个可以将网址生成二维码的 js 插件，它是借助HTML5 Canvas绘制而成。所以，今天的主角就是canvas啦–canvas的实际应用。

```html
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="ko" lang="ko">

<head>
    <title>Javascript 二维码生成库：QRCode</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no" />
    <script type="text/javascript" src="http://cdn.staticfile.org/jquery/2.1.1/jquery.min.js"></script>
    <script type="text/javascript" src="http://static.runoob.com/assets/qrcode/qrcode.min.js"></script>
</head>

<body>
    <input id="text" type="text" value="https://wqjiao.github.io/" style="width:80%" />
    <br />
    <div id="qrcode" style="width:100px; height:100px; margin-top:15px;"></div>

    <script type="text/javascript">
        var qrcode = new QRCode(document.getElementById("qrcode"), {
            width: 100,
            height: 100
        });

        function makeCode() {
            var elText = document.getElementById("text");

            if (!elText.value) {
                alert("Input a text");
                elText.focus();
                return;
            }

            qrcode.makeCode(elText.value);
        }

        makeCode();

        $("#text").
            on("blur", function () {
                makeCode();
            }).
            on("keydown", function (e) {
                if (e.keyCode == 13) {
                    makeCode();
                }
            });
    </script>
</body>

</html>
```

* React 中引入 `qrcode.react`

```js
import React from 'react';
import QRCode from 'qrcode.react';

class xx extends React.Component {
    render () {
        const {iosData} = this.props;
        return (
            <QRCode value={iosData} />
        )
    }
}
```

## 四、bind、apply、call 区别

* call()

Obj.function.call(thisArg, ...argArray);

    thisArg -- Context
    ...argArray -- 函数参数

```javascript
let mathObj = {
    pi: 3.14,
    area: function(r) {
        console.log(this); // this 指向上下文{pi: 3.14, area: ƒ, circumference: ƒ}
        return this.pi * r * r;
    },
    circumference: function(r) {
        return 2 * this.pi * r;
    },
    volume: function(r, h) {
        return this.pi * r * r * h;
    }
};

mathObj.area(2); // 12.56
// 但此时要求 pi 精确到小数点后 5 位，并立即执行该方法得出结果
mathObj.area.call({pi: 3.14159}, 2); // 12.56636
```

* apply()

Obj.function.apply(thisArg, argArray)

    thisArg -- Context
    argArray -- 参数数组

上述代码也可以使用 `apply()`，与 `call()` 类似，结果一致，只是参数的类型不一样

```javascript
let mathObj = {
    pi: 3.14,
    area: function(r) {
        console.log(this); // this 指向上下文{pi: 3.14, area: ƒ, circumference: ƒ}
        return this.pi * r * r;
    },
    circumference: function(r) {
        return 2 * this.pi * r;
    },
    volume: function(r, h) {
        return this.pi * r * r * h;
    }
};

mathObj.volume.call({pi: 3.14159}, 2, 6); // 参数作为函数参数被传递 75.39815999999999
mathObj.volume.apply({pi: 3.14159}, [2, 6]); // 函数参数作为数组传递 75.39815999999999
```

* bind()

bind 将一个全新的 this 注入到指定的函数上，改变 this 的指向， 使用 bind 时，函数不会像 call 或 apply 立即执行

```javascript
let mathObj = {
    pi: 3.14,
    area: function(r) {
        console.log(this); // this 指向上下文{pi: 3.14, area: ƒ, circumference: ƒ}
        return this.pi * r * r;
    },
    circumference: function(r) {
        return 2 * this.pi * r;
    },
    volume: function(r, h) {
        return this.pi * r * r * h;
    }
};

let newVolume = mathObj.volume.bind({pi: 3.14159});
newVolume(2, 6);
```

它允许我们将上下文注入一个函数，该函数返回一个具有更新上下文的新函数。这意味着这个变量将是用户提供的变量，这在处理 JavaScript 事件时非常有用。

```javascript
var button = document.getElementById("button"),
    text = document.getElementById("text");
button.onclick = function() {
    alert(this.id); // 弹出text
}.bind(text);

// 由于 IE6 - IE8 浏览器不支持，需要进行代码模拟
// 判断是否存在 bind 方法
if (!function() {}.bind) {
    Function.prototype.bind = function(context) {
        var self = this,
            args = Array.prototype.slice.call(arguments);
            
        return function() {
            return self.apply(context, args.slice(1));    
        }
    };
}
```

* 三者的区别

三种方式都是改变函数的 this 对象的指向；其中第一个参数都是 this 要指向的对象；都可以利用后续参数进行传参。但是 bind 返回对应函数，便于后续调用，而 apply、call 都是立即调用。

## 五、js 函数

之前看到过这样一段代码，函数变量中变量和函数被封装，这样可以避免全局变量的污染。jQuery 中的 Lodash 的库就是采用这种技术提供的 $、_等。

```javascript
let simpleLibrary = function() {
    let simpleLibrary = {
        a: 10,
        b: 2,
        add: function(a, b) {
            return a + b;
        },
        subtract: function(a, b) {
            return a - b;   
        }
    }
    return simpleLibrary;
}();

simpleLibrary; // {a: 10, b: 2, add: ƒ, subtract: ƒ}
simpleLibrary.add(100, 88); // 188
```

## 六、类似简单的生成器

```javascript
function generator(input) {
    let index = 0;
    return {
        next: function() {
            if (index < input.lenght) {
                return input[index -1];
            }
            return "";
        }
    }
}
```

## 七、ES10 探索

https://juejin.im/post/5c7c8e125188256365101c34

## 八、Object 原型

```js
var object = {};
object.__proto__ ===  Object.prototype;  // 为 true

var fn = function() {};
fn.__proto__ === Function.prototype;  // 为 true
fn.__proto__.__proto__ === Object.prototype; // 为 true

var array = [];
array.__proto__ === Array.prototype; // 为 true
array.__proto__.__proto__ === Object.prototype; // 为 true

Function.__proto__ ===Function.prototype; // 为 true
Array.__proto__ === Function.prototype; // 为 true
Object.__proto__ === Function.prototype; // 为 true

true.__proto__ === Boolean.prototype; // 为 true

Function.prototype.__proto__ ===  Object.prototype; // 为 true
```

## 九、获取某个元素的位置集合

* `getBoundingClientRect()`

`getBoundingClientRect` 用于获取某个元素相对于视窗的位置集合。集合中有 top, right, bottom, left 等属性。

```js
let client = e.target.getBoundingClientRect();

client.width // 宽度
client.height // 高度
client.top // 上边距(相对可视区域上方)
client.right // 右边距(相对可视区域右方)
client.bottom // 下边距(相对可视区域下方)
client.left // 左边距(相对可视区域左方)
client.x // x 轴坐标/left (相对可视区域左方)
client.y // y 轴坐标/top (相对可视区域上方)
```

* 获取浏览器网页的相关高度/宽度

    - body 对象的宽/高
    ```js
    document.body.clientWidth // body 对象宽度
    document.body.clientHeight // body 对象高度
    ```

    - 可见区域宽/高
    ```js
    document.documentElement.clientWidth // 可见区域宽度
    document.documentElement.clientHeight // 可见区域高度
    ```

    - 网页正文全文宽/高
    ```js
    document.body.scrollWidth
    document.body.scrollHeight
    ```

    - 网页上方滚动的上/左侧边距
    ```js
    document.body.scrollTop // 上方滚动距离(手机/平板)
    document.body.scrollLeft // 左侧滚动距离(手机/平板)
    document.documentElement.scrollTop // 上方滚动距离
    document.documentElement.scrollLeft // 左侧滚动距离
    window.pageXOffset // 上方滚动距离 window.scrollX 别名
    window.pageYOffset // 左侧滚动距离 window.scrollY 别名
    ```

    - 网页宽/高(包括边线的宽/高)
    ```js
    document.body.offsetWidth
    document.body.offsetHeight
    document.documentElement.offsetWidth
    document.documentElement.offsetHeight
    ```

    - 返回当前渲染窗口中和屏幕有关的属性 [window.screen](https://developer.mozilla.org/zh-CN/docs/Web/API/Screen) (包括边线和滚动条)

    ```js
    window.screen.width // 可用空间宽度
    window.screen.height // 可用空间高度
    window.screen.availWidth // 可用空间宽度
    window.screen.availHeight // 可用空间高度
    window.screen.availLeft // 可用空间左边距离屏幕(系统电脑)左边界的距离
    window.screen.availTop // 可用空间上边距离屏幕(系统电脑)上边界的距离
    window.screen.colorDepth
    window.screen.pixelDepth
    window.screen.orientation
    ```

    注意: `availLeft/availTop` 大多数情况下，返回 0。

    如果你在有两个屏幕的电脑上使用该属性，在右侧屏幕计算该属性值时，返回左侧屏幕的宽度（单位：像素），也即左侧屏幕左边界的 X 坐标。

    在 Windows 中，该属性值取决于哪个屏幕被设为主屏幕，返回相对于主屏幕左边界的 X 坐标。就是说，即使主屏幕不是左侧的屏幕，它的左边界的 X 坐标也是返回 0。如果副屏幕在主屏幕的左侧，则它拥有负的 X 坐标。

    [1] [2] - 左屏幕 availLeft 返回 0，右侧的屏幕返回左侧屏幕的宽度；

    [2] [1] - 左侧屏幕 availLeft 返回该屏幕的 -width，右侧屏幕返回 0；

    ```js
    let setY = window.screen.height - window.screen.availTop;
    let setX = window.screen.width - window.screen.availLeft;
    window.moveTo(setX, setY);
    ```

* 获取元素对应的边距属性

    - 获取对象相对于版面或由父坐标 `offsetParent` 属性指定的父坐标的属性
    ```js
    let _dom = document.getElementById('container');

    _dom.offsetHeight // 当前元素内容高度
    _dom.offsetLeft // 当前元素相对父元素的偏移距离(不包括当前元素内边距)
    _dom.offsetTop // 获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置   
    event.clientX // 相对文档的水平坐标   
    event.clientY // 相对文档的垂直坐标   
    event.offsetX // 相对容器的水平坐标   
    event.offsetY // 相对容器的垂直坐标
    ```

* 注意：`offsetTop/offsetLeft` 是相对定位父级的偏移量，如果需要可以使用 `getBoundingClientRect().top` 与 `scrollTop` 解决现有问题

## 十、JSON 与 Javascript 的区别

`JSON`(JavaScript Object Notation) 数据格式，用于数据交换，键值对表示
    `key` 必须使用双引号 `""` 包裹；
    `value` 如果是字符串，也必须是双引号 `""`，不能是 undefined || NaN

`Javascript` 键值对，`key` 不需要引号包裹

```js
var test = {
    one : 'Good',
    'two' : 'Man',
    'three' : undefined,
    'four' : [ 1, 2 ],
    'five' : function() {
        return 'test'
    } 
}

JSON.stringify(test); //"{"one":"Good","two":"Man","four":[1,2]}"
{name: 'wqjiao', sex: '女'} // javascript 对象
{"name": "wqjiao", "sex": "女"} // JSON格式的 javascript 对象
'{"name": "wqjiao", "sex": "女"}' // JSON格式的字符串
```

## 十一、Javascript 方法

* `charAt()` 方法返回指定位置的字符 stringObject.charAt(index) index 必需，当前字符串的下标

* `indexOf()` 方法返回调用 String 对象中第一次出现的指定值的索引，开始在 fromIndex 进行搜索。 stringObject.indexOf(searchValue, fromIndex) searchValue 必需，该字符串表示被查找的值；fromIndex 可选，开始查找的位置

* `substr()` 返回一个从指定位置开始的指定长度的子字符串 stringvar.substr(start [, length ]) start 必需，开始查找的位置；length 可选，返回的字符串长度

* `substring()` 返回位于 String 对象中指定位置的子字符串 strVariable.substring(start, end) start/end 开始查找--结束的下标位置

* `sort()` 排序 Array.sort((a,b) => a-b) 数字数组的升序排列

* `parseInt()` parseInt(string, radix) string -- 要解析的字符串；radix -- 字符串基数
    
    ```js
    // 将'123'看作 5 进制数，返回十进制数 38 => 1*5^2 + 2*5^1 + 3*5^0 = 38
    parseInt('123', 5);
    ```

    一些数中可能包含e字符（例如6.022e23），使用parseInt去截取包含e字符数值部分会造成难以预料的结果。例如：
    ```js
    parseInt("6.022e23", 10); // 返回 6
    parseInt(6.022e2, 10); // 返回 602
    ```

    所以 `parseInt()` 不能作为 `Math.floor()`(向下取整) 的替代品

    string 如果以 '0' 开头，基数将会是 8
    string 如果以 '0x' 或 '0X' 开头，基数将会是 16
    ```js
    parseInt('0x56'); // 5*16^1 + 6*16^0 = 86
    parseInt('056'); // 5*8^1 + 6*8^0 = 46
    ```

* `Math.floor()` 返回小于或等于一个给定数字的最大整数；向下取整

* `str.trim()` 过滤字符串前后空格 `' hel lo  '.trim()` ==> `hel lo`

## 十二、什么是 js 链表
[什么是链表](https://aotu.io/notes/2017/10/13/make-a-chain-class/index.html)
[掘金链表实例](https://juejin.im/entry/59cb70995188256aa423b680)

```js
/*定义List类*/
function List () {
    this.listSize = 0; // 初始化元素个数为0
    this.pos = 0; // 初始化位置为0
    this.dataStore = []; // 初始化空数组来保存列表元素
    this.clear = clear; // 清空列表所有元素
    this.find = find; // 寻找元素
    this.toString = toString; // 显示列表中的元素
    this.insert = insert; // 在现有元素后插入新元素
    this.append = append; // 在列表末尾添加新元素
    this.remove = remove; // 从列表中删除元素
    this.front = front; // 将列表的当前位置移动到第一个元素
    this.end = end; // 将列表的当前元素移动到最后一个元素
    this.prev = prev; // 将当前位置前移一位
    this.next = next; // 将当前位置后移一位
    this.length = length; // 列表中的元素总数
    this.currPos = currPos; // 返回列表的当前位置
    this.moveTo = moveTo; // 将当前位置移动到指定位置
    this.getElement = getElement; // 返回当前位置的元素
    this.contains = contains; // 判断给定值是否在列表中
}

```

```js
// 定义栈/堆栈
function Stack () {
    this.dataStore = [];    //初始化为空
    this.top = 0;           //记录栈顶位置
    this.pop = pop;         //出栈
    this.push = push;       //入栈
    this.peek = peek;       //查看栈顶元素
    this.length = length;   //查看栈内元素总数
    this.clear = clear;     //清空栈
}

//该方法将一个新元素入栈，放到数组中 top 所对应的位置上，并将 top 的值加 1，让其指向数组的下一个空位置
function push( element ) {
    this.dataStore[this.top++] = element;
}

//该方法与入栈相反，返回栈顶元素，并将 top 的值减 1
function pop() {
    return this.dataStore[--this.top];
}

//该方法返回的是栈顶元素，即 top - 1 个位置元素
function peek() {
    if( this.top > 0 ) return this.dataStore[this.top-1];
    else return 'Empty';
}

// 堆栈长度
function length() {
    return this.dataStore.length || this.top;
}

// 清空栈
function clear() {
    delete this.dataStore;
    this.dataStore = [];
    this.top = 0;
}

var stack = new Stack();
```

```js
//定义队列
function Queue(){
    this.dataStore = [];
    this.enqueue = enqueue;     //入队
    this.dequeue = dequeue;     //出队
    this.front = front;         //查看队首元素
    this.back = back;           //查看队尾元素
    this.toString = toString;   //显示队列所有元素
    this.clear = clear;         //清空当前队列
    this.empty = empty;         //判断当前队列是否为空
}
//向队列末尾添加一个元素，直接调用 push 方法即可

function enqueue ( element ) {
    this.dataStore.push( element );
}
//删除队列首的元素，可以利用 JS 数组中的 shift 方法

function dequeue () {
    if( this.empty() ) return 'This queue is empty';
    else this.dataStore.shift();
}
//我们通过判断 dataStore 的长度就可知道队列是否为空

function empty(){
    if( this.dataStore.length == 0 ) return true;
    else return false;
}
//查看队首元素，直接返回数组首个元素即可

function front(){
    if( this.empty() ) return 'This queue is empty';
    else return this.dataStore[0]；
}
//查看队首元素，直接返回数组最后一个元素即可

//读取队列尾的元素
function back () {
    if( this.empty() ) return 'This queue is empty';
    else return this.dataStore[ this.dataStore.length - 1 ];
}
//查看对了所有元素，我这里采用数组的 join 方法实现

function toString(){
    return this.dataStore.join('\n');
}
//清空当前队列，也很简单，我们直接将 dataStore 数值清空即可

function clear(){
    delete this.dataStore;
    this.dataStor = [];
}
var queue = new Queue();
```

## 随机颜色

```js
Array.from({length: 6},()=>Math.floor(Math.random()*16).toString(16)).join("")
```

## 七天内数组

```js
Array.from({length: 7},(_,days) => new Date(Date.now() - 86400000 * days))
```

## Object.keys/Object.values/Object.entries

* [Object.keys](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/keys)

`Object.keys()` 方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 `for...in` 循环遍历该对象时返回的顺序一致 （两者的主要区别是 一个 for-in 循环还会枚举其原型链上的属性）。

```js
let obj = {name: 'aaa', age: '20'}
Object.keys(obj); // ['name', 'age']
```

* [Object.values](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/values)

`Object.values()`方法返回一个给定对象自己的所有可枚举属性值的数组，值的顺序与使用 `for...in` 循环的顺序相同 ( 区别在于 for-in 循环枚举原型链中的属性 )。

```js
let obj = {name: 'aaa', age: '20'}
Object.keys(obj); // ['aaa', '20']
```

* [Object.entries](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/entries)

`Object.entries()`方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用`for...in` 循环遍历该对象时返回的顺序一致（区别在于 for-in 循环也枚举原型链中的属性）

```js
let obj = {name: 'aaa', age: '20'}
Object.keys(obj); // [['name', 'aaa'], ['age', 20']]
```
