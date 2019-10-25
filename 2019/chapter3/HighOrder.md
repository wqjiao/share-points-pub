# 高阶函数

## 条件
  - 接受一个或多个函数作为输入
  - 输出一个函数

## 理解 -- 函数作为返回值输出

* `Object.prototype.toString.call` 获取对应对象返回的字符串
```js
let isString = obj => Object.prototype.toString.call( obj ) === '[object String]';
let isArray = obj => Object.prototype.toString.call( obj ) === '[object Array]';
let isNumber = obj => Object.prototype.toString.call( obj ) === '[object Number]';
```

通过上面可以写成一个公共的方法:
```js
let isType = (type) => {
  return (obj) => {
    return Object.prototype.toString.call( obj ) === `[object ${type}]`;
  }
}

// 或者简写箭头函数
let isType = type => obj => {
  return Object.prototype.toString.call( obj ) === `[object ${type}]`;
}

isType('String')('123');    // true
isType('Array')([1, 2, 3]); // true Array.isArray()
isType('Number')(123);      // true
```

* 数字无限累加函数
```js
function add(a) {
    function sum(b) { // 使用闭包
      a = a + b; // 累加
      return sum;
    }
    sum.toString = function() { // 重写toString()方法
        return a;
    }
    return sum; // 返回一个函数
}

add(1); // 1
add(1)(2);  // 3
add(1)(2)(3)； // 6
add(1)(2)(3)(4)； // 10 
```

## 实例
* `Array.prototype.map` 遍历

  ```js
  // 普通遍历
  const arr1 = [1, 2, 3, 4];
  const arr2 = [];
  for (let i = 0; i < arr1.length; i++) {
    arr2.push( arr1[i] * 2);
  }

  // 高阶函数 map
  const arr1 = [1, 2, 3, 4];
  const arr2 = arr1.map(item => item * 2);

  console.log( arr2 ); // [2, 4, 6, 8]
  console.log( arr1 ); // [1, 2, 3, 4]
  ```

* `Array.prototype.filter` 过滤器
  
  ```js
  // 普通遍历
  const arr1 = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
  const arr2 = [];
  for (let i = 0; i < arr1.length; i++) {
    if (arr1.indexOf( arr1[i] ) === i) {
      arr2.push( arr1[i] );
    }
  }
  // 高阶函数 filter
  const arr1 = [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4];
  const arr2 = arr1.filter( (element, index, self) => {
      return self.indexOf( element ) === index;
  });

  console.log( arr2 ); // [1, 2, 3, 5, 4]
  console.log( arr1 ); // [1, 2, 1, 2, 3, 5, 4, 5, 3, 4, 4, 4, 4]
  ```

* `Array.prototype.reduce` 累加器

  ```js
  // 普通遍历
  const arr = [0, 1, 2, 3, 4];
  let sum = 0;
  for (let i = 0; i < arr.length; i++) {
    sum += arr[i];
  }

  // 高阶函数 reduce
  const arr = [0, 1, 2, 3, 4];
  const initalValue = 10; // initalValue 为初始值，默认 0；在执行过程中添加 initalValue，多一个执行步骤
  let sum = arr.reduce((accumulator, currentValue, currentIndex, array) => {
    return accumulator + currentValue;
  }, [initalValue]);

  console.log( sum ); // 10
  console.log( arr ); // [0, 1, 2, 3, 4]
  ```

* `Array.prototype.includs` 是否包含
