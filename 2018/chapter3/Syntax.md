# ES6部分语法

## 1、展开语法（Spread syntax)：3个点
* 作用
    
    可以在函数调用/数组构造时, 将数组表达式或者string在语法层面展开；
    
    还可以在构造字面量对象时, 将对象表达式按key-value的方式展开。

    可以对数组去重 [...new Set(array)]
    
    (注: 字面量一般指 [1, 2, 3] 或者 {name: "mdn"} 这种简洁的构造方式)
    - 例如：
        ```
        function sum(x, y, z) {
            return x + y + z;
        }
        const numbers = [1, 2, 3];
        console.log(sum(...numbers)); // expected output: 6
        console.log(sum.apply(null, numbers)); // expected output: 6

        this.state = {
            arr: [
                {
                    id: 1,
                    text: '测试1'
                }
            ]
        }
        this.setState({
            arr: [
                ..._this.state.arr,
                {
                    id: _this.state.arr.length + 1,
                    text: '测试' + _this.state.arr.length + 1
                }
            ]
        })
        =>
        this.setState({
            arr: this.state.arr.concat({
                id: _this.state.arr.length + 1,
                text: '测试' + _this.state.arr.length + 1
            })
        })        
    ```

## 2、var、let、const使用

    var 不存在块级作用域的概念，在特定场景下会形成作用于提前，导致变量undefined

    let 则存在块级作用域的概念
    
    const  阻止的是对于变量绑定的修改，而不阻止对成员值的修改

## 3、Set
* 作用

    它类似于数组，但是成员的值都是唯一的，没有重复的值。

    数组去重 [...new Set(array)]

    Set 本身是一个构造函数，用来生成 Set 数据结构。
    ```
    const s = new Set();
    [2, 3, 5, 4, 5, 2, 2].forEach(x => {
    s.add(x)
    console.log(s); // 2;2,3;2,3,5;2,3,5,4;2,3,5,4;...
    });
    for (let i of s) {
        console.log(i);
    }
    // 2，3，5，4
    ```

* 注意： 上面代码通过add方法向 Set 结构加入成员，结果表明 Set 结构不会添加重复的值。

    Set 函数可以接受一个数组（或者具有 iterable 接口的其他数据结构）作为参数，用来初始化

* 参考网址
    https://github.com/ruanyf/es6tutorial/blob/3c44084f4b2e318fcbec77b7191b1f2412726c47/docs/set-map.md

## 4、模板字符串
    ```
    // bad
    const foo = 'this is a' + example;

    // good
    const foo = `this is a ${example}`;
    ```

    标签模板
    ```
    let url = oneLine `
        www.taobao.com/example/index.html
        ?foo=${foo}
        &bar=${bar}
    `;
    ```

## 条件语句优化 Map
    
根据颜色找出对应的水果

* bad
    ```
    function test(color) {
        switch (color) {
            case 'red':
                return ['apple', 'strawberry'];
            case 'yellow':
                return ['banana', 'pineapple'];
            case 'purple':
                return ['grape', 'plum'];
            default:
                return [];
        }
    }

    test('yellow');
    ```

* good
    ```
    const fruitColor = {
        red: ['apple', 'strawberry'],
        yellow: ['banana', 'pineapple'],
        purple: ['grape', 'plum']
    };

    function test(color) {
        return fruitColor[color] || [];
    }
    ```

* better
    ```
    const fruitColor = new Map()
        .set('red', ['apple', 'strawberry'])
        .set('yellow', ['banana', 'pineapple'])
        .set('purple', ['grape', 'plum']);

    function test(color) {
        return fruitColor.get(color) || [];
    }
    ```

## 箭头函数
this向外围作用域/window，使用方便 ，代码简洁，但是应避免以下几种情况下使用：
* 定义对象中的方法
    ```
    let foo = {
        value: 1,
        getValue: () => console.log(this.value)
    }

    foo.getValue();  // undefined
    ```

    替换成
    ```
    let foo = {
        value: 1,
        getValue(){
            console.log(this.value)
        }
    }

    foo.getValue();  // 1
    ```

    因为箭头函数没有它自己的this值，箭头函数内的this值继承自外围作用域。

    对象方法内的this指向调用这个方法的对象，如果使用箭头函数，this和对象方法在调用的时候所处环境的this值一致。因为 test.sum()是在全局环境下进行调用，此时this指向全局。

    解决方法也很简单，使用函数表达式或者方法简写（ES6 中已经支持）来定义方法，这样能确保 this 是在运行时是由包含它的上下文决定的。

* 定义原型方法
    ```
    function Person(name) {
        this.name = name;
    }

    Person.prototype.sayName = () => {
        console.log(this === window); // => true
        return this.name;
    };

    const cat = new Person('Mew');
    cat.sayName(); // => undefined
    ```
    将箭头函数替换成：
    ```
    Person.prototype.sayName = function() {
        console.log(this === Person); // => true
        return this.name;
    };
    ```

    主要原因还是箭头函数中this的指向问题

* 定义事件回调函数
    ```
    const button = document.getElementById('myButton');
    button.addEventListener('click', () => {
        console.log(this === window); // => true
        this.innerHTML = 'Clicked button';
    });
    ```

    替换成：
    ```
    const button = document.getElementById('myButton');
    button.addEventListener('click', function() {
        console.log(this === button); // => true
        this.innerHTML = 'Clicked button';
    });
    ```

* 定义构造函数

理论上来说也是不能这么做的，因为箭头函数在创建时this对象就绑定了，更不会指向对象实例。

    ```
    const Message = (text) => {
        this.text = text;
    };
    const helloMessage = new Message('Hello World!');
    // Throws "TypeError: Message is not a constructor"
    ```

    替换成：
    ```
    const Message = function(text) {
        this.text = text;
    };
    const helloMessage = new Message('Hello World!');
    ```

* 带有逻辑判断的简短函数

    代码可读性比较差
    ```
    const multiply = (a, b) => b === undefined ? b => a * b : a * b;
    const double = multiply(2);
    double(3); // => 6
    multiply(2, 3); // => 6
    ```

    替换成：
    ```
    function multiply(a, b){
        if (b === undefined) {
            return function(b) {
                return a * b;
            }
        }
        return a * b;
    }
    const double = multiply(2);
    double(3); // => 6
    multiply(2, 3); // => 6
    ```

## for of

* for...of 循环可以使用的范围包括
    ```
    数组
    Set
    Map
    类数组对象，如 arguments 对象、DOM NodeList 对象
    Generator 对象
    字符串
    ```

* 优势

    ES2015 引入了 for..of 循环，它结合了 forEach 的简洁性和中断循环的能力：

    ```
    for (const v of ['a', 'b', 'c']) {
        console.log(v);
    }
    // a b c

    for (const [i, v] of ['a', 'b', 'c'].entries()) {
        console.log(i, v);
    }
    // 0 "a"
    // 1 "b"
    // 2 "c"
    ```

* 遍历 Map
    ```
    let map = new Map(arr);

    // 遍历 key 值
    for (let key of map.keys()) {
        console.log(key);
    }

    // 遍历 value 值
    for (let value of map.values()) {
        console.log(value);
    }

    // 遍历 key 和 value 值(一)
    for (let item of map.entries()) {
        console.log(item[0], item[1]);
    }

    // 遍历 key 和 value 值(二)
    for (let [key, value] of data) {
        console.log(key)
    }
    ```

##  React 与 Redux
    ```
    // good
    class MyReactComponent extends React.Component {}

    export default connect(mapStateToProps, mapDispatchToProps)(MyReactComponent);

    // better
    @connect(mapStateToProps, mapDispatchToProps)
    export default class MyReactComponent extends React.Component {};

    ```

## 参考链接

* [Es6](http://es6.ruanyifeng.com)
* [掘金](https://juejin.im/post/5bfe05505188252098022400)