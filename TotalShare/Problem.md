# 问题汇总

## this 指向

* 全局环境中的this

    - 浏览器环境
    无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象 window;

    - node 环境
    无论是否在严格模式下，在全局执行环境中（在任何函数体外部），this 都是空对象 {};

* 是否是 `new` 绑定

    - 构造函数返回值不是function 或 object，this 指向新对象
    ```js
    function Super(age) {
        this.age = age;
    }

    let instance = new Super('26');
    console.log(instance.age); // '26'
    ```

    - 构造函数返回值是 function 或 object，this 指向返回的对象
    ```js
    function Super(age) {
        this.age = age;
        let obj = {a: '2'};
        return obj;
    }

    let instance = new Super('26');
    console.log(instance.age); // undefined
    console.log(instance.a); // '2'
    ```

## 如何得到 (a == 1 && a == 2 && a == 3) 的结果为 true

### 隐式转换规则

* `Number.prototype.valueOf`
```js
var i = 1
Number.prototype.valueOf = function() {
    return i++
}
var a = new Number(1)
if (a == 1 && a == 2 && a == 3) {
    console.log('here', a)
}
```

* `valueOf`
```js
let a = {
    valueOf: (function() {
        let i = 1;
        //闭包的特性之一：i 不会被回收
        return function() {
            return i++;
        }
    })();
}
console.log(a == 1 && a == 2 && a == 3); // true
```

* [Symbol.toPrimitive]
```js
let a = {
    [Symbol.toPrimitive]: (function(hint) {
            let i = 1;
            //闭包的特性之一：i 不会被回收
            return function() {
                return i++;
            }
    })()
}
console.log(a == 1 && a == 2 && a == 3); // true
```

* 正则匹配
```js
let a = {
    reg: /\d/g,
    valueOf () {
        return this.reg.exec(123)[0];
    }
}
console.log(a == 1 && a == 2 && a == 3); // true
```

### 数据劫持

* `Object.defineProperty`
```js
let i = 1;
Object.defineProperty(window, 'a', {
    get: function() {
        return i++;
    }
});
console.log(a == 1 && a == 2 && a == 3); // true
```

* `Proxy`
```js
let a = new Proxy({}, {
    i: 1,
    get: function () {
        return () => this.i++;
    }
});
console.log(a == 1 && a == 2 && a == 3); // true
```

* 数组的 `toString` 接口默认调用数组的 `join` 方法，重写数组的 `join` 方法
```js
let a = [1, 2, 3];
a.join = a.shift;
console.log(a == 1 && a == 2 && a == 3); // true
```

## 防抖(debounce)函数的作用是什么？有哪些应用场景，请实现一个防抖函数。
防抖函数的作用
防抖函数的作用就是控制函数在一定时间内的执行次数。防抖意味着N秒内函数只会被执行一次，如果N秒内再次被触发，则重新计算延迟时间。
举例说明：小思最近在减肥，但是她非常贪吃。为此，与其男朋友约定好，如果10天不吃零食，就可以购买一个包(不要问为什么是包，因为包治百病)。但是如果中间吃了一次零食，那么就要重新计算时间，直到小思坚持10天没有吃零食，才能购买一个包。所以，管不住嘴的小思，没有机会买包(悲伤的故事)...这就是防抖。
不管吃没吃零食，每10天买一个包，中间想买包，忍着，等到第十天的时候再买，这种情况是节流。如何控制女朋友的消费，各位攻城狮们，get到了吗？防抖可比节流有效多了！

* 防抖应用场景

    - 搜索框输入查询，如果用户一直在输入中，没有必要不停地调用去请求服务端接口，等用户停止输入的时候，再调用，设置一个合适的时间间隔，有效减轻服务端压力。
    - 表单验证
    - 按钮提交事件。
    - 浏览器窗口缩放，resize事件等。

* 防抖函数实现

事件第一次触发时，timer 是 null，调用 later()，若 immediate 为true，那么立即调用 func.apply(this, params)；如果 immediate 为 false，那么过 wait 之后，调用 func.apply(this, params)
事件第二次触发时，如果 timer 已经重置为 null(即 setTimeout 的倒计时结束)，那么流程与第一次触发时一样，若 timer 不为 null(即 setTimeout 的倒计时未结束)，那么清空定时器，重新开始计时。

```js
function debounce(func, wait, immediate = true) {
    let timer;
    // 延迟执行函数
    const later = (context, args) => setTimeout(() => {
        timer = null;// 倒计时结束
        if (!immediate) {
            func.apply(context, args);
            //执行回调
            context = args = null;
        }
    }, wait);
    let debounced = function (...params) {
        let context = this;
        let args = params;
        if (!timer) {
            timer = later(context, args);
            if (immediate) {
                //立即执行
                func.apply(context, args);
            }
        } else {
            clearTimeout(timer);
            //函数在每个等待时延的结束被调用
            timer = later(context, args);
        }
    }
    debounced.cancel = function () {
        clearTimeout(timer);
        timer = null;
    };
    return debounced;
};
```

immediate 为 true 时，表示函数在每个等待时延的开始被调用。
immediate 为 false 时，表示函数在每个等待时延的结束被调用。
只要高频事件触发，那么回调函数至少被调用一次。

## Promise.all([])

## 深拷贝与浅拷贝
深拷贝 => 拷贝所有的属性,并且地址也与原来的不同,这样的话,你改变当前的属性也不会影响原来的
浅拷贝 => 就是直接赋值的这种,地址相同,当你改变现在的值,原来的值也会跟着改变

```
深拷贝的实现:
    a:JSON.parse(JSON.stringify(obj)) => 这种方式只能对属性不是function的有用
    b:Object.assgin({},target) => 这种方式第一层是深拷贝,第二层是浅拷贝
    c:jquery的JQ.extend
    d:采用递归来进行拷贝
    e:采用扩展运算符var a = {...obj};
```

## 闭包

闭包的定义其实很简单：函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。

```js
function A() {
    let a = 1
    window.B = function() {
        console.log(a)
    }
}
A()
B() // 1
```

很多人对于闭包的解释可能是函数嵌套了函数，然后返回一个函数。其实这个解释是不完整的，就比如我上面这个例子就可以反驳这个观点。

在 JS 中，闭包存在的意义就是让我们可以间接访问函数内部的变量。
