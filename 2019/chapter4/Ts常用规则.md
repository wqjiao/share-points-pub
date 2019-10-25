# Typescript 常用规则汇总

目前越来越多的中大型公司开始使用 [Typescript](https://ts.xcatliu.com/basics/type-assertion) 规范项目代码，便于持续性的项目迭代。`Typescript` 增加了代码的可读性和维护性，规范变量类型。但是缺点是项目中的框架或者插件等必须同样支持 `Typescript`，否则比较麻烦。

## 安装 Typescript

```
npm install -g typescript
tsc hello.ts // 生成一个 hello.js文件
```

TypeScript 中，使用 `:` 指定变量的类型，`:` 的前后有没有空格都可以
```ts
function sayHello(person: string) {
    return 'Hello, ' + person;
}

let user = 'Tom';
console.log(sayHello(user));
```

注意：`TypeScript` 编写的文件以 `.ts` 为后缀，用 `TypeScript` 编写 `React` 时，以 `.tsx` 为后缀。

## 基础

### 原始数据类型

* `boolean`

```ts
let isShow: boolean = true;
// let createdByNewBoolean: boolean = new Boolean(1); // error TS2322
let createdByNewBoolean: Boolean = new Boolean(1);
let createdByBoolean: boolean = Boolean(1);
```

`boolean` 是原始数据类型，而 `new Boolean()` 返回的是一个 `Boolean` 对象

* `number`

* `string`

* 空值 || `function`

`JavaScript` 没有空值（Void）的概念，在 `TypeScript` 中，可以用 `void` 表示没有任何返回值的函数：
```ts
function alertName(): void {
    alert('My name is Tom');
}
```

声明一个 `void` 类型的变量没有什么用，因为你只能将它赋值为 `undefined` 和 `null`:
```ts
let unusable: void = undefined;
```

* `null` || `undefined`

`undefined` 类型的变量只能被赋值为 `undefined`, `null` 类型的变量只能被赋值为 `null`。
```ts
let u: undefined = undefined;
let n: null = null;
```

与 `void` 的区别是，`undefined` 和 `null` 是所有类型的子类型。也就是说 `undefined` 类型的变量，可以赋值给 `number` 类型的变量：
```ts
// 这样不会报错
let num: number = undefined;
// 这样也不会报错
let u: undefined;
let num: number = u;
```

而 `void` 类型的变量不能赋值给 `number` 类型的变量：
```ts
let u: void;
let num: number = u; // error TS2322
```

* `Symbol`

### 任意值

允许该变量被赋值为任意类型
```ts
let anyThing: any = 'seven';
anyThing = 7;
```

### 类型推论

定义某个变量时，没有明确指定类型，`Typescript` 会进行 `类型推论`，推测出一个类型：
```ts
// 虽没有明确变量类型，但 `Typescript` 推测出是 `string` 类型
let str = 'seven';
str = 7; // error TS2322 

// 虽没有明确类型，但定义变量时没有赋值，`Typescript` 推测出是 `any` 类型
let anyThing;
anyThing = 'seven';
anyThing = 7;
```

### 联合类型

表示变量的取值可以是多种类型中的一种，联合类型使用 | 分隔每个类型。
```ts
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

### 对象的类型 -- 接口

```ts
interface Person {
    readonly id: number; // 只读
    name: string; // 必需变量
    age?: number; // 可选值
    [propName: string]: any; // 任意属性
}
```

### 数组的类型

在 `Typescript` 中，数组的类型有多种定义方式，比较灵活。

* [类型+方括号] 表示法

```ts
// 最简单的定义方式，数组中不能出现除 定义类型 以外的类型
let fibonacci: number[] = [1, 1, 2, 3, 5];
```

* 数组泛型

```ts
let fibonacci: Array<number> = [1, 1, 2, 3, 5]; // 数字类型
let fibonacci: Array<any> = [1, 1, '2', 3, 5]; // 任意类型
// 定义类型为输入的类型
function createArray<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}
createArray<string>(3, 'x'); // ['x', 'x', 'x']
```

* 用接口表示数组

```ts
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```

* `any` 在数组中的应用

使用 `any` 表示数组中允许出现的任意类型
```ts
let list: any[] = ['Xcat Liu', 25, { website: 'http://xcatliu.com' }];
```

* 类数组 -- 不是数组类型

事实上常见的类数组都有自己的接口定义，如 `IArguments`, `NodeList`, `HTMLCollection` 等:
```ts
function sum() {
    let args: IArguments = arguments;
}
```
注意： 具体可以参考内置对象

### 函数的类型

* 函数声明

```ts
function nums(x: number, y: number): number {
    return x + y;
}
```
注意：最后调用函数的时候，少于或者多于函数要求的变量都是不被允许的。

* 函数表达式

```ts
let nums = function (x: number, y: number): number {
    return x + y;
}
// 在 TypeScript 的类型定义中，=> 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型
let sum: (x: number, y: number) => number = function (x: number, y: number): number {
    return x + y;
}
```

* 接口定义函数的形状

```ts
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```

* 可选参数、参数默认值、剩余参数

    - 可选参数
    可选参数必须在必选参数之后，且可选参数后面不能再出现必选参数，否则 `Typescript` 会报错 error TS1016。

    - 参数默认值
    `Typescript` 会将添加了默认值的参数识别为可选参数。

    - 剩余参数
    在 `ES6` 中，可以使用 `...rest` 的方式获取函数中的剩余参数(rest 参数) ，且 `rest` 参数只能是最后一个参数 [ES6 rest参数](http://es6.ruanyifeng.com/#docs/function#rest%E5%8F%82%E6%95%B0)。`rest` 参数搭配的变量是一个数组，该变量将多余的参数放入数组中。

    ```ts
    function buildName(firstName: string, array: any[] = [], lastName?: string, ...items: any[]) {
        if (lastName) {
            return firstName + ' ' + lastName;
        } else {
            return firstName;
        }
    }
    let tomcat = buildName('Tom', 'Cat');
    let tom = buildName('Tom');
    ```

* 重载

实现一个函数，输入数字||字符串时做不同的操作。可以使用 `联合类型` 定义变量，但是缺点是，不能精确的表达：输入的类型跟输出的类型应该是精确对应的。

```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```

上例中，我们重复定义了多次函数 `reverse`，前几次都是函数定义，最后一次是函数实现。在编辑器的代码提示中，可以正确的看到前两个提示。
注意: `TypeScript` 会优先从最前面的函数定义开始匹配，所以多个函数定义如果有包含关系，需要优先把精确的定义写在前面。

### 类型断言

类型断言不是类型转换，断言成一个 `联合类型` 中已存在的类型，在需要断言的变量前加上 `<Type>` 即可。

```ts
function getLength(something: string | number): number {
    if ((<string>something).length) {
        return (<string>something).length;
    } else {
        return something.toString().length;
    }
}
```

如果以上代码中的不做类型断言，则会报错：
```
error TS2339: Property 'length' does not exist on type 'string: number'.
Property 'length' does not exist on type 'number'.
    if (something.length) {
error TS2339: Property 'length' does not exist on type 'string: number'.
Property 'length' does not exist on type 'number'.
    return something.length;
```

### 用 TypeScript 写 Node.js

`Node.js` 不是内置对象的一部分，如果想用 `TypeScript` 写 `Node.js`，则需要引入第三方声明文件：
`npm install @types/node --save-dev`

## 进阶

### 类型别名

使用 `type` 来定义类型的别名，通俗的说就是给类型起一个别名，一般用在联合类型
```ts
type str = string;
type strs = () => string;
let name: str | strs;
```

### 字符串字面量类型

使用 `type` 来定义一个字符串字面量类型，用来约束取值只能是某几个字符串中的一个。
```ts
type names = 'click' | 'change' | 'focus';
```

### 元组

```ts
let xcatliu: [string, number];

xcatliu = ['Xcat Liu', 25];
xcatliu.push('http://xcatliu.com/')
xcatliu.push(true); // error TS2345:Argument of type 'true' is not assignable to parameter of type 'string: number'.
```

## 参考网址

[TS](https://ts.xcatliu.com/engineering/lint)
