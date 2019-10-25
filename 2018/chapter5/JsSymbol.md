---
title: Js 对象及数据类型
date: 2019-01-02
categories: web 前端
tags:
- Javascript
- Symbol
---
总结 Js 对象及数据类型：1、js 判断对象是否为空的 5 种方式；2、js遍历对象的  4 种方式；3、js 遍历数组的 5 种方式；4、原始数据类型及ES6 Symbol 数据类型...
<!-- more -->
## 一、js 判断对象是否为空

### 1、将 json 对象转化为 json 字符串，再判断该字符串是否为 "{}"

    var data = {};
    var b = (JSON.stringify(data) == "{}");
    alert(b); // true

注意： 忽略转换 undefined、function、Symbol 属性

    JSON.stringify({[Symbol("key")]: "foo"}); // {}

### 2、for in 循环判断

    var obj = {};
    var b = function() {
        for(var key in obj) {
            return false;
        }
        return true;
    }
    alert(b()); // true

### 3、jquery 的 isEmptyObject()

此方法是 jquery 将 2 方法 (for in) 进行封装，使用时需要依赖 jquery

    var data = {};
    var b = $.isEmptyObject(data);
    alert(b); // true

### 4、Object.getOwnPropertyNames()

获取到对象中的属性名，存到一个数组中，返回数组对象，我们可以通过判断数组的 length 来判断此对象是否为空

    var data = {};
    var arr = Object.getOwnPropertyNames(data);
    alert(arr.length === 0); // true

注意：此方法不兼容 IE8

### 5、使用 ES6 的 Object.keys()

与 4 方法类似，是 ES6 的新方法, 返回值也是对象中属性名组成的数组

    var data = {};
    var arr = Object.keys(data);
    alert(arr.length === 0); // true

## 二、js 遍历对象

### 1、for in

循环遍历对象自身的和继承的可枚举属性(不含 Symbol 属性)

    var obj = {'0': 'a', '1': 'b', '2': 'c'};

    for (var i in obj) {
        console.log(i, obj[i]);
    }

### 2、Object.getOwnPropertyNames()

返回一个数组,包含对象自身的所有属性(不含 Symbol 属性,但是包括不可枚举属性).

    var obj = {'0': 'a', '1': 'b', '2': 'c'};

    Object.getOwnPropertyNames(obj).forEach((key) => {
        console.log(key, obj[key]);
    });

### 3、Object.keys()

返回一个数组,包括对象自身的(不含继承的)所有可枚举属性(不含 Symbol 属性)

    var obj = {'0': 'a', '1': 'b', '2': 'c'};

    Object.keys(obj).forEach((key) => {
        console.log(key, obj[key]);
    });

### 4、Reflect.ownKeys()

返回一个数组,包含对象自身的所有属性,不管属性名是 Symbol 或字符串,也不管是否可枚举

    var obj = {'0': 'a', '1': 'b', '2': 'c'};

    Reflect.ownKeys(obj).forEach(function(key) {
        console.log(key, obj[key]);
    });

## 三、js 数组遍历

### 1、for

    var arr = [1, 2, 3];

    for (var i = 0; i < arr.length; i ++) {
        console.log(i, arr[i]);
    }

### 2、forEach

    var arr = [1, 2, 3];

    arr.forEach(function(val, index) {
        console.log(val, index);
    });

### 3、for in

    var arr = [1, 2, 3];

    for (var i in arr) {
        console.log(i, arr[i]);
    }

### 4、for of

不仅支持数组，还支持大多数类数组对象

也支持字符串遍历，它将字符串视为一系列的 Unicode 字符来进行遍历

    var arr = [1, 2, 3];

    for (var value of arr) {
        console.log(value);
    }

### 5、数组/对象数组中获取 id

```javascript
let arr = [{
    id: 0,
    text: 'a'
}, {
    id: 1,
    text: 'b'
}];

// map()/filer()
let str = arr.map( (item) => {
    return [item.id]; // 字符串、数组、对象
});
```

## 四、js 删除数组中某一项或几项

### splice
替换/删除/添加数，该方法会改变原始数组

splice(index, len, [item]);

index: 数组开始下标
len: 替换/删除的长度
item: 替换的值，删除操作的话 item 为空

* 删除 -- item 不设置

```javascript
let arr = ['a','b','c','d'];
// 删除起始下标为1，长度为1的一个值，len设置的1，如果为0，则数组不变
arr.splice(1, 1); // ['a','c','d'] 
// 删除起始下标为 1，长度为 2 的一个值，len 设置的 2
arr.splice(1, 2); // ['a','d']
```

* 替换 -- item 为替换的值

```javascript
let arr = ['a','b','c','d'];
// 替换起始下标为 1，长度为 1 的一个值为 ‘ttt’，len 设置的 1
arr.splice(1, 1, 'ttt'); // ['a','ttt','c','d']
// 替换起始下标为1，长度为2的两个值为‘ttt’，len设置的1
arr.splice(1, 2, 'ttt'); // ['a','ttt','d']         
```

* 添加 -- len 设置为 0，item 为添加的值

```javascript
let arr = ['a','b','c','d'];
// 表示在下标为 1 处添加一项 ‘ttt’
arr.splice(1, 0, 'ttt'); // ['a','ttt','b','c','d']
```

### slice

* 数组截断

```js
let array = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
array = array.slice(0, 4); // [0, 1, 2, 3]
```

* 获取数组中的最后一项

`slice()` 可以接受负数,表示它将接受数组末尾的值
```js
let arr = [1, 5, 8, 9,56];
console.log(arr.slice(-1)); // [56]
console.log(arr.slice(-2)); // [9, 56]
```
    
### delete
delete 删除掉数组中的元素后，会把该下标出的值置为undefined,数组的长度不会变，优势：原数组的索引也保持不变，此时再遍历数组元素，会跳过其中 undefined 的元素

```javascript
let arr = ['a','b','c','d'];

for (index in arr) {
    document.write('arr[' + index + ']='+arr[index]);
}

// 中间出现两个逗号，数组长度不变，有一项为 undefined
delete arr[1]  //['a', ,'c','d']
```

## 五、js 字符串截取

string str = "123abc456";
int i = 3;

### 1、取字符串的前 i 个字符

str=str.Substring(0,i); // or  str=str.Remove(i,str.Length-i); 

### 2、去掉字符串的前i个字符：
   
str=str.Remove(0,i);  // or str=str.Substring(i); 

### 3、从右边开始取i个字符：
  
str=str.Substring(str.Length-i); // or str=str.Remove(0,str.Length-i);

### 4、从右边开始去掉i个字符：
   
str=str.Substring(0,str.Length-i); // or str=str.Remove(str.Length-i,i);

### 5、判断字符串中是否有"abc" 有则去掉之

```javascript
string str = "123abc456";
string a="abc";
Regex r = new  Regex(a); 
Match m = r.Match(str);

if (m.Success) {
    //绿色部分与紫色部分取一种即可。
    str=str.Replace(a,"");
    Response.Write(str);   
    string str1, str2;
    str1=str.Substring(0, m.Index);
    str2=str.Substring(m.Index + a.Length, str.Length - a.Length - m.Index);
    Response.Write(str1 + str2); 
}
```

### 6、如果字符串中有"abc"则替换成"ABC"

   ·str=str.Replace("abc","ABC");

### 7、截取指定字符区间

string str="adcdef";

int indexStart = str.IndexOf("d");

int endIndex =str.IndexOf("e");

string toStr = str.SubString(indexStart, endIndex - indexStart);

### 8、c# 截取字符串最后一个字符的问题!

str1.Substring(str1.LastIndexOf(",")+1)

## 六、判断字符串中是否包含某个子字符串

* ES6 `includes`

```javascript
var string = "foo",
    substring = "oo";
string.includes(substring);
```

* ES5 and older `indexof`

```javascript
var string = "foo",
    substring = "oo";
string.indexOf(substring) !== -1;
```

* search

```javascript
var string = "foo",
    expr = /oo/;
string.search(expr);
```

* lodash includes

```javascript
var string = "foo",
    substring = "oo";
_.includes(string, substring);
```

* RegExp

```javascript
var string = "foo",
    expr = /oo/;  // no quotes here
expr.test(string);
```

* Match

```javascript
var string = "foo",
    expr = /oo/;
string.match(expr);
```

* 参考链接

[stackoverflow](https://stackoverflow.com/questions/1789945/how-to-check-whether-a-string-contains-a-substring-in-javascript/1789952#1789952)

## 七、数据类型

### 1、原始数据类型

    null 空值
    
    undefined 变量未初始化

    Number 数字类型

    String 字符串

    boolean 布尔型

    Object 对象

### 2、ES6 Symbol

* 概述

    Symbol 数据类型是一个原始数据类型

    Symbol 是一种特殊的、不可变的数据类型，可以作为对象属性的标识符使用
    
    Symbol 对象是一个 symbol primitive data type 的隐式对象包装器

* 语法格式

    Symbol([description]) // description 是可选的 字符串

    Symbol 的描述，可用于调试(便于区分 Symbol 值)但不能访问 Symbol 本身

    - Number

        const b = Number(10); // 10
        const b = 10; // 简写

    - Symbol

        const name = Symbol();
        const name1 = Symbol('sym1');
        console.log(name, name1) // Symbol() Symbol(sym1)

* Symbol 转码 需要 babel 及 babel-polyfill

babel默认只转换新的JavaScript句法（syntax），而不转换新的API，比如 Iterator、Generator、Set、Maps、Proxy、Reflect、Symbol、Promise 等全局对象，以及一些定义在全局对象上的方法（比如Object.assign）都不会转码。如果想让这个方法运行，必须使用 babel-polyfill，为当前环境提供一个垫片。

* Symbol 特点

    - 1、Symbol 不能使用 new

    ``` const name = new Symbol(); ```

    - 2、每个从 Symbol() 返回的 symbol 值都是唯一的

    一个 symbol 值能作为对象属性的标识符

    Symbol("foo") 不会强制字符串 “foo” 成为一个 symbol类型
    
    它每次都会创建一个新的 symbol类型：

    ```javascript
    Symbol("foo") === Symbol("foo"); // false
    Symbol.for('foo') === Symbol.for('foo'); // true
    ```

    Symbol.for() 与 Symbol() 这两种写法，都会生成新的 Symbol
    
    它们的区别是，前者会被登记在全局环境中供搜索，后者不会
    
    Symbol.for() 不会每次调用就返回一个新的 Symbol 类型的值，而是会先检查给定的 key 是否已经存在，如果不存在才会新建一个值，比如，如果你调用 Symbol.for("cat") 30 次，每次都会返回同一个 Symbol 值，但是调用 Symbol("cat")30 次，会返回 30 个不同的 Symbol 值

    - 3、可以使用 `Object.defineProperty()` 和 `Object.defineProperties()` 方法

    在一个对象上定义新的属性

    ```javascript
    // 设置对象属性只读。
    Object.defineProperty(obj, name, {writable: false});

    Object.defineProperty(props, 'key', {
        get: warnAboutAccessingKey,
        configurable: true
    });
    ```

    - 4、全局共享

    Symbol 在 js 文件中定义的 Symbol，并不能在其他文件直接共享

    要创建跨文件可用的 symbol，甚至跨域（每个都有它自己的全局作用域），
    使用 Symbol.for() 方法和  Symbol.keyFor() 方法从全局的 symbol 注册表中设置和取得。

    Symbol.for() // 只有一个参数

    Symbol.keyFor() // 只有一个参数，返回的是key

    ```javascript
    let name = Symbol.for('000');
    let name1 = Symbol.for('111');
    let name2 = Symbol.for('222');

    console.log(Symbol.keyFor(name)); // 000
    console.log(Symbol.keyFor(name1)); // 111
    console.log(Symbol.keyFor(name2)); // 222
    ```

    - 5、Symbol 不支持类型强制转换

    ```javascript
    let a = 1;
    console.log(typeof a); // number
    console.log(a + ' haha') // '1haha'

    Symbol 不支持强制转换

    let a = Symbol('a');
    console.log(typeof a);
    console.log(a + ' haha'); // Cannot convert a Symbol value to a string
    ```

    - 6、Symbol 检索

    Symbol 作为属性名，该属性不会出现在 `for...in`、`for...of` 循环中
    
    也不会被 `Object.keys()`、`Object.getOwnPropertyNames()`、`JSON.stringify()` 返回
    
    但是，它也不是私有属性，有一个 ES6 新增的方法 `Object.getOwnPropertySymbols` 方法
    
    可以获取指定对象的所有 Symbol 属性名

    ```javascript
    let a = Symbol('a');
    let b = Symbol('b');
    let obj = { [a]: '123', [b]: 45 }

    for (let value of Object.getOwnPropertySymbols(obj)) {
        console.log(obj[value]); // "123" 45
    }
    ```

    - 7、Object.getOwnPropertySymbols

    使用 Object.getOwnPropertyNames 方法得不到Symbol属性名，需要使用 `Object.getOwnPropertySymbols` 方法

    ```javascript
    const obj = {};

    let foo = Symbol("foo");

    Object.defineProperty(obj, foo, {
        value: "foobar",
    });

    for (let i in obj) {
        console.log(i); // 无输出
    }

    Object.getOwnPropertyNames(obj); // []

    Object.getOwnPropertySymbols(obj); // [Symbol(foo)]
    ```

    - 8、Reflect.ownKeys

    还有一个上面提到的 Reflect.ownKeys 方法可以返回所有类型的键名,包括常规键名和 Symbol 键名

    ```javascript
        let obj = { [Symbol('my_key')]: 1, enum: 2, nonEnum: 3 };

        Reflect.ownKeys(obj); // ["enum", "nonEnum", Symbol(my_key)]
    ```

    - 9、typeof 运算符

    ```javascript
    typeof Symbol('foo') === 'symbol'
    ```

    - 10、在所有使用可计算属性名的地方，都能使用 Symbol 类型。比如在对象中的key

    ```javascript
    const name = Symbol('name');
    const obj = { [name]: "haha" }
    console.log(obj[name]); // haha
    ```

    - 11、Symbol 包装器对象作为属性的键

    ```javascript
    var sym = Symbol('foo');
    var obj = {[sym]: 1};
    obj[sym]; // 1
    obj[Object(sym)]; // still 1
    ```

* 实例： 消除魔术字符串

    - 消除魔术字符串，消除强耦合

    ```javascript
    function getArea(shape, options) {
        let area = 0;

        switch (shape) {
            case 'Triangle': // 魔术字符串
            area = .5 * options.width * options.height;
            break;
            /* ... more code ... */
        }

        return area;
    }

    getArea('Triangle', { width: 100, height: 100 }); // 魔术字符串
    ```

    - 定义一个变量

    ```javascript
    const shapeType = {
        triangle: 'Triangle'
    };

    function getArea(shape, options) {
        let area = 0;
        switch (shape) {
            case shapeType.triangle:
            area = .5 * options.width * options.height;
            break;
        }
        return area;
    }

    getArea(shapeType.triangle, { width: 100, height: 100 });
    ```

    - Symbol 值

    如果仔细分析，可以发现 shapeType.triangle 等于哪个值并不重要
    
    只要确保不会跟其他 shapeType 属性的值冲突即可
    
    因此，这里就很适合改用 Symbol 值

    ```javascript
    const shapeType = {
        triangle: Symbol()
    };
    ```

### 3、参考链接

* [developer - Symbol](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
* [Es6 基本用法](http://es6.ruanyifeng.com/?search=new+Set&x=0&y=0#docs/class)
