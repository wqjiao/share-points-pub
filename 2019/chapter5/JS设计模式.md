# JS 设计模式

## 1.实现一个观察者模式

观察者模式定义了对象间的一种一对多的依赖关系，当一个对象的状态发生改变时，所有依赖于它的对象都将得到通知，并自动更新。观察者模式属于行为型模式，行为型模式关注的是对象之间的通讯，观察者模式就是观察者和被观察者之间的通讯。
观察者（Observer）直接订阅（Subscribe）主题（Subject），而当主题被激活的时候，会触发（Fire Event）观察者里的事件。

```js
// 有一家猎人工会，其中每个猎人都具有发布任务(publish)，订阅任务(subscribe)的功能
// 他们都有一个订阅列表来记录谁订阅了自己
// 定义一个猎人类
// 包括姓名，级别，订阅列表
function Hunter(name, level) {
    this.name = name
    this.level = level
    this.list = []
}

Hunter.prototype.publish = function (money) {
    console.log(this.level + '猎人' + this.name + '寻求帮助')
    this.list.forEach(function (item, index) {
        item.event(money);
    });
}

// 订阅
Hunter.prototype.subscribe = function (targrt, fn) {
    console.log(this.level + '猎人' + this.name + '订阅了' + targrt.name)
    targrt.list.push({
        name: this.name,
        event: fn
    });
}

// 退订
Hunter.prototype.unsubscribe = function (targrt) {
    targrt.list.forEach((item, index) => {
        if (this.name === item.name) {
            targrt.list.splice(index, 1);
            console.log(this.level + '猎人' + this.name + '退订了' + targrt.name)
        }
    });
 }

// 猎人工会走来了几个猎人
let hunterMing = new Hunter('小明', '黄金');
let hunterJin = new Hunter('小金', '白银');
let hunterZhang = new Hunter('小张', '黄金');
let hunterPeter = new Hunter('Peter', '青铜');

// Peter等级较低，可能需要帮助，所以小明，小金，小张都订阅了Peter
hunterMing.subscribe(hunterPeter, money => {
    console.log('小明表示：' + (money > 200 ? '' : '暂时很忙，不能') + '给予帮助');
});
hunterJin.subscribe(hunterPeter, () => {
    console.log('小金表示：给予帮助');
});
hunterZhang.subscribe(hunterPeter, () => {
    console.log('小张表示：给予帮助');
});

// Peter遇到困难，赏金198寻求帮助
hunterPeter.publish(198);

// 猎人们(观察者)关联他们感兴趣的猎人(目标对象)，如Peter，当Peter有困难时，会自动通知给他们（观察者）
```

## 2.实现一个工厂模式
工厂模式类似于现实生活中的工厂可以产生大量相似的商品，去做同样的事情，实现同样的效果;这时候需要使用工厂模式。

简单的工厂模式可以理解为解决多个相似的问题;这也是她的优点;比如如下代码：

```js
function CreatePerson(name,age,sex) {
    var obj = {};
    obj.name = name;
    obj.age = age;
    obj.sex = sex;
    obj.sayName = function(){
        return this.name;
    }
    return obj;
}
var p1 = new CreatePerson("longen",'28','男');
var p2 = new CreatePerson("tugenhua",'27','女');
console.log(p1.name); // longen
console.log(p1.age);  // 28
console.log(p1.sex);  // 男
console.log(p1.sayName()); // longen

console.log(p2.name);  // tugenhua
console.log(p2.age);   // 27
console.log(p2.sex);   // 女
console.log(p2.sayName()); // tugenhua

// 返回都是object 无法识别对象的类型 不知道他们是哪个对象的实列
console.log(typeof p1);  // object
console.log(typeof p2);  // object
console.log(p1 instanceof Object); // true
```

如上代码：函数CreatePerson能接受三个参数name,age,sex等参数，可以无数次调用这个函数，每次返回都会包含三个属性和一个方法的对象。

工厂模式是为了解决多个类似对象声明的问题;也就是为了解决实列化对象产生重复的问题。

优点：能解决多个相似的问题。

缺点：不能知道对象识别的问题(对象的类型不知道)。
