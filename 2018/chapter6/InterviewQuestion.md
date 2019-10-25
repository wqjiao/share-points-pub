# 面试题

## 给定一个非空整数数组，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素

* 初步解法

这道题第一眼看过去，思路挺简单的，我们只需要维护一个对象来记录每一个元素出现的次数，使用元素的值作为key,元素出现的次数作为value。之后再遍历这个对象，找到value为1的key。对应的key就是那个元素

```javascript
function singleNumber(nums) {
    const obj = {};
    for (let i = 0; i < nums.length; i++) {
        obj[nums[i]] = obj[nums[i]] ? obj[nums[i]] + 1 : 1;
    }
    for (let key in obj) {
        if (obj[key] === 1) {
            return Number(key); // 由于 key 是 string ,因此我们这里需要转化下
        }
    }
}

console.log(singleNumber([2, 2, 1, 4, 4, 5, 5, 1, 8])); // 8
```

注意：上述解法创建了一个新的对象来储存结果，具有线性复杂度，考虑是否可以不使用额外空间的解法。

* 最终解法

题目是只有一个元素出现了一次，其余均出现了两次，通过过滤两个相同得数字的方法，实现要求。

异或操作：

异或运算是对于二进制数字而言的，比如说一个有两个二进制 a、b，如果 a、b 两个值不相同，则异或结果为 1。如果 a、b 两个值相同，异或结果为 0。
而 javascript 的按位异或(即 ^ 操作)操作，则会对两个数字相应的每一对比特位执行异或操作。
比如说 1 ^ 2，本质上其实是 1 和 2 的每一对比特位执行异或操作，等价于下面

```
00000000000000000000000000000001 // 数字1对应的二进制
^ 00000000000000000000000000000010 // 数字2对应的二进制
= 00000000000000000000000000000011 // 数字3对应的二进制
```

因此 1^2 的结果就为 3 啦。
那么如果两个相同的数字进行异或操作，结果就可想而知，答案为 0 啦。
如果是 0 和任何一个数字异或呢？结果是数字本身。

```javascript
/**
* 只存在一次的数字
* @param {number[]} nums
* @return {number}
*/
function singleNumber(nums) {
    for (let i = 1; i < nums.length; i ++) {
        nums[0] ^= nums[i];
    }
    return nums[0];
};

console.log(singleNumber([2, 2, 1, 4, 4, 5, 5, 1, 8]));

```

* 参考链接

[leetcode-en](https://leetcode.com/explore/interview/card/top-interview-questions-easy/1/array/25/)
[leetcode-cn](https://leetcode-cn.com/explore/interview/card/top-interview-questions-easy/1/array/25/)

## 回流 && 重绘

* 回流

当 render tree 的一部分或全部的元素因改变了自身的宽高，布局，显示或隐藏，或者元素内部的文字结构发生变化，导致需要重新构建页面的时候，回流就产生了。

- 1、页面渲染初始化

每个页面至少需要一次回流，就是在页面第一次加载的时候。

- 2、添加或者删除可见的DOM元素

当DOM树的结构变化时，例如节点的增减、移动等，也会触发回流。浏览器引擎布局的过程，类似于树的前序遍历，是一个从上到下从左到右的过程。通常在这个过程中，当前元素不会再影响其前面已经遍历过的元素。

- 3、DOM元素的几何属性变化

当DOM元素的几何属性变化时，渲染树中的相关节点就会失效，浏览器会根据DOM元素的变化重建构建渲染树中失效的节点。当前元素的回流通常会带来一系列的反应，甚至触发整个文档的回流和重绘，性能代价是高昂的。

- 4、内容改变

文本改变或者图片大小改变而引起的计算值宽度和高度改变。

- 5、浏览器窗口尺寸改变

resize事件发生时。

- 6、获取某些属性

虽然浏览器引擎可能会针对回流做了优化，比如Opera，它会等到有足够数量的变化发生，或者等到一定的时间，或者等一个线程结束，再一起处理，这样就只发生一次回流。但除了render树的直接变化，当获取一些属性时，浏览器为取得正确的值也会触发回流。这些属性包括：offsetTop/tLeft/Width/Height、scrollTop/Left/Width/Height、 clientTop/Left/Width/Height、width、height，请求了getComputedStyle(), 或者 IE的 currentStyle也会引起回流。

* 重绘

当一个元素自身的宽高，布局，及显示或隐藏没有改变，而只是改变了元素的外观风格的时候，就会产生重绘。例如你改变了元素的background-color

* 结论

回流必定触发重绘，而重绘不一定触发回流。

## 浏览器页面渲染过程

浏览器请求、加载、渲染一个页面经历五个过程：

```
DNS 查询
TCP 连接
HTTP 请求即响应
服务器响应
客户端渲染

处理 HTML 标记并构建 DOM 树。
处理 CSS 标记并构建 CSSOM 树。
将 DOM 与 CSSOM 合并成一个渲染树。
根据渲染树来布局，以计算每个节点的几何信息。
将各个节点绘制到屏幕上。
```

## Vue 双向绑定原理

## Web 开发如何提高页面性能优化

## JSONP 的工作原理

## vue-router 的实现原理

## React 与 Vue 的区别

## redux 数据流的流程

简单的阐述：首先由 view dispatch 拦截 action，然后执行对应 reducer 并更新到 store 中，最终 views 会根据 store 数据的改变执行界面的刷新渲染操作。

## 用过哪些设计模式

[js设计模式汇总](https://zhuanlan.zhihu.com/p/41423006)

* 1.工厂模式：工厂类可以根据条件生成不同的子类实例，这些子类有一个公共的抽象父类并且实现了相同的方法，但是这些方法针对不同的数据进行了不同的操作（多态方法）。当得到子类的实例后，开发人员可以调用基类中的方法而不必考虑到底返回的是哪一个子类的实例。

* 2.代理模式：给一个对象提供一个代理对象，并由代理对象控制原对象的引用。实际开发中，按照使用目的的不同，代理可以分为：远程代理、虚拟代理、保护代理、Cache代理、防火墙代理、同步化代理、智能引用代理。

* 3.适配器模式：把一个类的接口变换成客户端所期待的另一种接口，从而使原本因接口不匹配而无法在一起使用的类能够一起工作。

* 4.模板方法模式：提供一个抽象类，将部分逻辑以具体方法或构造器的形式实现，然后声明一些抽象方法来迫使子类实现剩余的逻辑。不同的子类可以以不同的方式实现这些抽象方法（多态实现），从而实现不同的业务逻辑。

## js 事件冒泡和事件代理

## js 面向对象中继承实现

## 数组排序的方法

[例子](https://www.jianshu.com/p/257319b309f3)

* sort() -- arrayObject.sort(sortby)

sort() 方法是在原数组上进行排序的，并不会返回新数组
arrayObject 参数是需要做排序的数组
sortby 参数是排序的顺序，必须是函数，默认按照数组中 `字符编码` 顺序进行排序的

* resres() -- 颠倒数组中元素的顺序

* 冒泡排序

冒泡排序就是遍历数组里面的元素，一次比较两个元素，如果它们的顺序错误，就把它们交换过来，直到没有需要交换的两个元素为止。它是一种稳定排序算法。

```js
function bubbleSort(arr) {
    for(var i = 0; i < arr.length; i++) {
        for(var j = 0; j < arr.length; j++) {
            if(arr[i] < arr[j]) {
                var temp = arr[j];
                arr[j] = arr[i];
                arr[i] = temp;
            }
        }
    }
    return arr;
}
var arr = [524, 684, 5, 69, 15];
bubbleSort(arr);    // 结果为[5, 15, 69, 524, 684]
```

* 快速排序

快速排序是对冒泡排序的一种改进。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

```js
function quickSort(arr) {
    if(arr.length <= 1) {return arr;}
    var pivotIndex = Math.floor(arr.length / 2);
    var pivot = arr.splice(pivotIndex, 1)[0];
    // splice()返回的是被删除元素组成的数组
    var lef = [];
    var rig = [];
    for(var i = 0; i < arr.length; i++) {
        if(arr[i] < pivot) {
            lef.push(arr[i]);
        } else {
            rig.push(arr[i]);
        }
    }
    return quickSort(lef).concat(pivot, quickSort(rig));
    // 递归
}
var arr = [524, 684, 5, 69, 15];
quickSort(arr);    // 结果为[5, 15, 69, 524, 684]
```

* 插入排序

* 希尔排序

* 选择排序

* 归并排序

* 堆排序

## web 攻击技术
    XSS、SQL注入攻击、CSRF
