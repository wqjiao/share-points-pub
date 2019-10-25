# JS简洁小技巧

js 技术的语法糖(`语法糖`是英国计算机科学家彼得·约翰·兰达（Peter J. Landin）发明的一个术语)
参考网址：
  https://juejin.im/entry/5bd8e8b75188254a267ef788

## 1.清空或截断数组

在不重新给数组赋值的情况下，清空或截断数组的最简单方法是更改​​其 length 属性值：

```
const arr = [11, 22, 33, 44, 55, 66];
// truncanting
arr.length = 3;
console.log(arr); //=> [11, 22, 33]
// clearing
arr.length = 0;
console.log(arr); //=> []
console.log(arr[2]); //=> undefined
```
## 2.使用对象解构(destructuring)模拟命名参数

当您需要将一组可选变量传递给某个函数时，你很可能已经在使用配置对象了，如下所示：

```
doSomething({ foo: 'Hello', bar: 'Hey!', baz: 42 });
function doSomething(config) {
const foo = config.foo !== undefined ? config.foo : 'Hi';
const bar = config.bar !== undefined ? config.bar : 'Yo!';
const baz = config.baz !== undefined ? config.baz : 13;
// ...
}
```

这是一个古老但有效的模式，它试图在 JavaScript 中模拟命名参数。 函数调用看起来很好。 另一方面，配置对象处理逻辑不必要地冗长。 使用ES2015 对象解构，您可以绕过这个缺点：
```
function doSomething({ foo = 'Hi', bar = 'Yo!', baz = 13 }) {
// ...
}
```

如果你需要使配置对象也可选，也很简单：
```
function doSomething({ foo = 'Hi', bar = 'Yo!', baz = 13 } = {}) {
// ...
}
```
注意： 函数调用时无数据，使用默认的数据，有数据则覆盖默认数据

## 3.使用对象解构来处理数组

可以使用对象解构将数组项分配给各个变量：
```
const csvFileLine = '1997,John Doe,US,john@doe.com,New York';
const { 2: country, 4: state } = csvFileLine.split(',');
console.log(country, state); // US/New York
```

## 4.switch 语句中使用范围值
```
function getWaterState(tempInCelsius) {
  let state;
  switch (true) {
	case (tempInCelsius < = 0):
      state = 'Solid';
      break;
     case (tempInCelsius > 0 && tempInCelsius < 100): 
       state = 'Liquid';
       break;
     default:
       state = 'Gas';
  }
  return state;
}
```

## 5.使用 async/await 来 await多个async函数

可以使用 Promise.all 来 await 多个 async（异步）函数。

```
await Promise.all([anAsyncCall(), thisIsAlsoAsync(), oneMore()])
```

* `Promise`

```javascript
function test(resolve, reject) {
    var timeOut = Math.random() * 2;
    log('set timeout to: ' + timeOut + ' seconds.');
    setTimeout(function () {
        if (timeOut < 1) {
            log('call resolve()...');
            resolve('200 OK');
        }
        else {
            log('call reject()...');
            reject('timeout in ' + timeOut + ' seconds.');
        }
    }, timeOut * 1000);
}
// 变量 p1 是一个 Promise 对象，它负责执行 test 函数。由于 test 函数在内部是异步执行的，当 test 函数执行成功时，执行 p2；否则当 test 函数执行失败时，执行 p3:
var p1 = new Promise(test);
var p2 = p1.then(function (result) {
    console.log('成功：' + result);
});
var p3 = p2.catch(function (reason) {
    console.log('失败：' + reason);
});

// Promise对象可以串联起来，所以上述代码可以简化为：
new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});
```

## 6.创建纯(pure)对象

创建一个 100％ 纯对象，它不会从 Object 继承任何属性或方法（例如，constructor，toString() 等）。

```
const pureObject = Object.create(null);
console.log(pureObject); //=> {}
console.log(pureObject.constructor); //=> undefined
console.log(pureObject.toString); //=> undefined
console.log(pureObject.hasOwnProperty); //=> undefined
```

## 7.格式化JSON代码

JSON.stringify 不仅可以简单地将对象转化为字符串。你也可以用它来格式化JSON输出：

```
const obj = { 
foo: { bar: [11, 22, 33, 44], baz: { bing: true, boom: 'Hello' } } 
};
// The third parameter is the number of spaces used to 
// beautify the JSON output.
JSON.stringify(obj, null, 4); 
// =>"{
// =>    "foo": {
// =>        "bar": [
// =>            11,
// =>            22,
// =>            33,
// =>            44
// =>        ],
// =>        "baz": {
// =>            "bing": true,
// =>            "boom": "Hello"
// =>        }
// =>    }
// =>}"
```

### JSON.parse(text[, reviver])

用于将一个 JSON 字符串转换为对象。

text:必需， 一个有效的 JSON 字符串。

reviver: 可选，一个转换结果的函数， 将为对象的每个成员调用此函数。

* 实现一个 `JSON.parse()`

	1. 使用 `eval`
	```js
	var json = '{arr: [{"a":"1", "b":2}, {"a":"3", "b":4}], name: "nihao"}';
	var obj = eval("(" + json + ")");  // obj 就是 json 反序列化之后得到的对象
	```
	
	注意：存在 XSS 漏洞，解决 -- 对参数 json 做校验，只有真正符合 JSON 格式，才能调用 eval。
	辅助工具： [正则可视化工具](https://regexper.com/)

	```js
	var rx_one = /^[\],:{}\s]*$/;
	var rx_two = /\\(?:["\\\/bfnrt]|u[0-9a-fA-F]{4})/g;
	var rx_three = /"[^"\\\n\r]*"|true|false|null|-?\d+(?:\.\d*)?(?:[eE][+\-]?\d+)?/g;
	var rx_four = /(?:^|:|,)(?:\s*\[)+/g;

	if (
		rx_one.test(
			json
				.replace(rx_two, "@")
				.replace(rx_three, "]")
				.replace(rx_four, "")
		)
	) {
		var obj = eval("(" +json + ")");
	}
	```

	- 使用 `Function`,与 `eval` 类似
	```js
	var json = '{"name":"小姐姐", "age":20}';
	var obj = (new Function('return ' + json))();
	```

### JSON.stringify(value[, replacer[, space]])

用于将 JavaScript 值转换为 JSON 字符串

value:必需， 要转换的 JavaScript 值（通常为对象或数组）。

replacer:可选。用于转换结果的函数或数组。
    如果 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。

    使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。

    如果 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。

space:可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 也可以使用非数字，如：\t。

* 实现一个 `JSON.stringfy()`

- 方法
```js
function jsonStringify(data) {
    let dataType = typeof data;
    if (dataType !== 'object') {
        let result = data;
        //data 可能是 string/number/null/undefined/boolean
        if (Number.isNaN(data) || data === Infinity) {
            //NaN 和 Infinity 序列化返回 "null"
            result = "null";
        } else if (dataType === 'function' || dataType === 'undefined' || dataType === 'symbol') {
            //function 、undefined 、symbol 序列化返回 undefined
            return undefined;
        } else if (dataType === 'string') {
            result = '"' + data + '"';
        }
        //boolean 返回 String()
        return String(result);
    } else if (dataType === 'object') {
        if (data === null) {
            return "null";
        } else if (data.toJSON && typeof data.toJSON === 'function') {
            return jsonStringify(data.toJSON());
        } else if (data instanceof Array) {
            let result = [];
            //如果是数组
            //toJSON 方法可以存在于原型链中
            data.forEach((item, index) => {
                if (typeof item === 'undefined' || typeof item === 'function' || typeof item === 'symbol') {
                    result[index] = "null";
                } else {
                    result[index] = jsonStringify(item);
                }
            });
            result = "[" + result + "]";
            return result.replace(/'/g, '"');

        } else {
            //普通对象
            /**
             * 循环引用抛错(暂未检测，循环引用时，堆栈溢出)
             * symbol key 忽略
             * undefined、函数、symbol 为属性值，被忽略
             */
            let result = [];
            Object.keys(data).forEach((item, index) => {
                if (typeof item !== 'symbol') {
                    //key 如果是symbol对象，忽略
                    if (data[item] !== undefined && typeof data[item] !== 'function'
                        && typeof data[item] !== 'symbol') {
                        //键值如果是 undefined、函数、symbol 为属性值，忽略
                        result.push('"' + item + '"' + ":" + jsonStringify(data[item]));
                    }
                }
            });
            return ("{" + result + "}").replace(/'/g, '"');
        }
    }
}
```

- 测试代码
```js
let sym = Symbol(10);
console.log(jsonStringify(sym) === JSON.stringify(sym));
let nul = null;
console.log(jsonStringify(nul) === JSON.stringify(nul));
let und = undefined;
console.log(jsonStringify(undefined) === JSON.stringify(undefined));
let boo = false;
console.log(jsonStringify(boo) === JSON.stringify(boo));
let nan = NaN;
console.log(jsonStringify(nan) === JSON.stringify(nan));
let inf = Infinity;
console.log(jsonStringify(Infinity) === JSON.stringify(Infinity));
let str = "hello";
console.log(jsonStringify(str) === JSON.stringify(str));
let reg = new RegExp("\w");
console.log(jsonStringify(reg) === JSON.stringify(reg));
let date = new Date();
console.log(jsonStringify(date) === JSON.stringify(date));
let obj = {
    name: '刘小夕',
    age: 22,
    hobbie: ['coding', 'writing'],
    date: new Date(),
    unq: Symbol(10),
    sayHello: function () {
        console.log("hello")
    },
    more: {
        brother: 'Star',
        age: 20,
        hobbie: [null],
        info: {
            money: undefined,
            job: null,
            others: []
        }
    }
}
console.log(jsonStringify(obj) === JSON.stringify(obj));


function SuperType(name, age) {
    this.name = name;
    this.age = age;
}
let per = new SuperType('小姐姐', 20);
console.log(jsonStringify(per) === JSON.stringify(per));

function SubType(info) {
    this.info = info;
}
SubType.prototype.toJSON = function () {
    return {
        name: '钱钱钱',
        mount: 'many',
        say: function () {
            console.log('我偏不说！');
        },
        more: null,
        reg: new RegExp("\w")
    }
}
let sub = new SubType('hi');
console.log(jsonStringify(sub) === JSON.stringify(sub));
let map = new Map();
map.set('name', '小姐姐');
console.log(jsonStringify(map) === JSON.stringify(map));
let set = new Set([1, 2, 3, 4, 5, 1, 2, 3]);
console.log(jsonStringify(set) === JSON.stringify(set));
```

## 8.从数组中删除重复元素(数组去重)

通过使用通过使用集合语法和 Spread(展开)运算符，您可以轻松地从数组中删除重复项：

```
const removeDuplicateItems = arr => [...new Set(arr)];
removeDuplicateItems([42, 'foo', 42, 'foo', true, true]);
//=> [42, "foo", true]
```

### ES6 提供了新的数据结构 Set。它类似于数组，但是成员的值都是唯一的，没有重复的值

### 用法： Array.from(arrayLike[, mapFn[, thisArg]])

作用： 方法从一个类似数组或可迭代对象中创建一个

新的数组实例。

返回值： 一个新的数组实例

#### arrayLike 想要转换成数组的伪数组对象或可迭代对象。

#### mapFn (可选参数) 如果指定了该参数，新数组中的每个元素会执行该回调函数。

#### thisArg (可选参数) 可选参数，执行回调函数 mapFn 时 this 对象。

## 9.平铺多维数组

使用 Spread(展开)，可以很容易去平铺嵌套多维数组：
```
const arr = [11, [22, 33], [44, 55], 66];
const flatArr = [].concat(...arr); //=> [11, 22, 33, 44, 55, 66]
```
可惜，上面的方法仅仅适用于二维数组。不过，通过递归，我们可以平铺任意维度的嵌套数组。

```
unction flattenArray(arr) {
const flattened = [].concat(...arr);
return flattened.some(item => Array.isArray(item)) ? 
flattenArray(flattened) : flattened;
}
const arr = [11, [22, 33], [44, [55, 66, [77, [88]], 99]]];
const flatArr = flattenArray(arr); 
//=> [11, 22, 33, 44, 55, 66, 77, 88, 99]
```

### 平铺数组 Array.flat(depth)

* 用法：var newArray = arr.flat(depth)

* 参数：depth指定嵌套数组中的结构深度，默认值为1。

* 返回值： 一个将子数组连接的新数组.

### hasOwnProperty 判断对象中是否有该属性

* 语法: object.hasOwnProperty(proName)
  
* 参数:
  ```
  object 必需。对象的实例。

  proName 必需。一个属性名称的字符串值。
  ```

* 例子： 
  	```
    var o = new Object(  );          // 创建对象

    o.x = 3.14;                      // 定义非继承的局部属性y

    o.hasOwnProperty("x");           // 返回 true: x 是O的局部属性

    o.hasOwnProperty("y");           // 返回 false: o 没有属性y

    o.hasOwnProperty("toString");    // 返回 false: toString属性是继承的
	```

* 注意：

	如果 object 具有带指定名称的属性，则 hasOwnProperty 方法返回 true，否则返回 false。

	此方法不会检查对象原型链中的属性；该属性必须是对象本身的一个成员。

	```
	let cache = {...},
		detail = {};
		
	for (var prop in cache) {
		if (cache.hasOwnProperty(prop)) {
			detail[prop] = cache[prop]
		}
	}
	```