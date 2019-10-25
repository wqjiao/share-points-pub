# Promise

* 1、基本用法

    ```js
    const primise = new Promise((resolve, reject) => {
        axios({
            method: 'POST',
            url: '****',
            dataType: 'json',
            data: {}
        }).then((response) => {
            if (response) {
                // Promise 已成功
                resolve(response.data);
            } else {
                // Promise 已失败
                reject('接口获取失败！');
            }
        });
    });

    primise.then(
        // 完成情况
        data => {
            console.log('success:', data);
        },
        // 拒绝情况--------------------
        err => {
            console.log('error:', err);
        }
    )
    ```

* 2、三种状态

    pending(进行中)、fulfilled(已成功)、rejected(已失败);
    
* 3、两种结果

    resolve --> fulfilled 成功
    reject --> rejected 失败/拒绝

* 4、then 链式操作

    ```js
    const getRandom = () => +(Math.random() * 1000).toFixed(0);
    new Promise((resolve, reject) => {
        let timeout = getRandom();

        console.log(`time=${timeout}`);
        setTimeout(() => {
            if (timeout > 200) {
                resolve(taskID);
            } else {
                reject('failed')
            }
        }, timeout);
    }).then(
        data => {
            console.log('resolved:', data);
            return data;
        },
        // 指定 reject 的回调 -- catch
        err => consolse.log('rejected:', err);
    ).then(data => {
        console.log('resolved:', data);
    });
    ```

* 5、catch 捕捉 err

    - `catch` 操作相当于 `then` 中的第二个参数，用来指定 `reject` 的回调
    - 在执行 `resolve` 的回调(`then` 的第一个参数)时，若抛出异常/代码出错，那么并不会报错卡死js，而是进到 `catch` 方法中。

    ```js
    const getRandom = () => +(Math.random() * 1000).toFixed(0);
    new Promise((resolve, reject) => {
        let timeout = getRandom();

        console.log(`time=${timeout}`);
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
        throw '手动抛出异常';
        throw new Error('手动抛出异常');
    }).catch(err => {
        console.log('rejected:', err);
    });
    ```

* 6、其他知识点

    - `throw` 语句的作用是手动中断程序执行，抛出一个错误
    - `catch` 与 `try ... catch` 有相同的功能，见 [代码对比](/2018/chapter4/Compare.md) 中第 22 条。

## promise.all([]) 内部是如何执行的

Promise.all([]) 遵循所有 promise 都执行成功，才能执行回调函数。传入数组，返回值也为数组。

Promise.all 属于 `并行执行` promise

```javascript
// 获取随机数，toFixed为四舍五入保留小数，0为保留整数，范围～1000
const getRandom = () => +(Math.random() * 1000).toFixed(0);

const asyncTask = (taskID) => new Promise((resolve) => {
    // 随机获取一次0~1000的随机数
    let timeout = getRandom();
    // 打印出传递进来的ID号 taskID=1 start.
    console.log(`taskID=${taskID} start.`);
    // 设置计时时间，function()等价于 () => {...}
    setTimeout(function () {
        // 打印出执行的taskID,和timeout
        console.log(`taskID=${taskID} finished in time=${timeout}.`);
        // 异步成功执行
        resolve(taskID)
    }, timeout);
});

Promise.all([asyncTask(1), asyncTask(2), asyncTask(3)])
.then(resultList => {
    console.log('results:', resultList); // results: (3) [1, 2, 3]
});
```

自定义封装 `Promise.all([])` 方法
```js
function promiseAll(promises) {
    return new Promise(function (resolve, reject) {
        // 先判断 promises 是否为数组
        judgeType(promises);

        let resolvedCounter = 0,
            promiseNum = promises.length,
            resolvedValues = new Array(promiseNum);

        for (var i = 0; i < promiseNum; i++) {
            (function (i) {
                Promise.resolve(promises[i]).then(function (value) {
                    resolvedCounter++;
                    resolvedValues[i] = value;

                    if (resolvedCounter == promiseNum) {
                        return resolve(resolvedValues);
                    }
                }, function (reason) {
                    return reject(reason);
                });
            })(i);
        }
    });
}
// 类型的判断
function judgeType(array) {
    if (array instanceof Array) {
        array.forEach(item => {
            if (!(item instanceof Promise)) {
                throw '该参数的每一项必须是Promise的实例';
            }
        });
    } else {
        throw '必须是数组哦';
    }
}
```

## Promise.race([])

Promise.race([]) 遵循着谁执行快，谁输出的原则

```javascript
// 获取随机数，toFixed为四舍五入保留小数，0为保留整数，范围～1000
const getRandom = () => +(Math.random() * 1000).toFixed(0);

const asyncTask = (taskID) => new Promise((resolve) => {
    // 随机获取一次0~1000的随机数
    let timeout = getRandom();
    // 打印出传递进来的ID号 taskID=1 start.
    console.log(`taskID=${taskID} start.`);
    // 设置计时时间，function()等价于 () => {...}
    setTimeout(function () {
        // 打印出执行的taskID,和timeout
        console.log(`taskID=${taskID} finished in time=${timeout}.`);
        // 异步成功执行
        resolve(taskID)
    }, timeout);
});

Promise.race([asyncTask(1), asyncTask(2), asyncTask(3)])
.then(resultList => {
    console.log('results:', resultList); // results: 2
});
```

## 顺序执行 Promise

```javascript
function getA() {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve(2);
        }, 1000);
    });
}

function getB() {
    return new Promise(function (resolve, reject) {
        setTimeout(function () {
            resolve(3);
        }, 1000);
    });
}

function addAB(a, b) {
    return a + b
}
```

* 方法一 连续使用 then 链式操作

```javascript
function getResult() {
    var obj = {};
    Promise.resolve().then(function () {
        return getA()
    })
        .then(function (a) {
            obj.a = a;
        })
        .then(function () {
            return getB()
        })
        .then(function (b) {
            obj.b = b;
            return obj;
        })
        .then(function (obj) {
            return addAB(obj['a'], obj['b'])
        })
        .then(data => {
            console.log(data)
        })
        .catch(e => console.log(e));

}
getResult();
```

* 方法二 使用 promise 构建队列

```javascript
function getResult() {
    var res = [];
    // 构建队列
    function queue(arr) {
        var sequence = Promise.resolve();
        arr.forEach(function (item) {
            sequence = sequence.then(item).then(data => {
                res.push(data);
                return res
            })
        })
        return sequence
    }

    // 执行队列
    queue([getA, getB]).then(data => {
        return addAB(data[0], data[1])
    })
        .then(data => {
            console.log(data)
        })
        .catch(e => console.log(e));

}

getResult();
```

* 方法三 使用async、await实现类似同步编程

```javascript
function getResult() {
    async function queue(arr) {
        let res = []
        for (let fn of arr) {
            var data = await fn();
            res.push(data);
        }
        return await res
    }

    queue([getA, getB])
        .then(data => {
            return addAB(data[0], data[1])
        }).then(data => console.log(data))

}

getResult();
```

## new Promise()

```js
new Promise(resolve => {
    dispatch({
        type: 'orderApplyStepFour/deleteFile',
        payload: {
            fileId: file.uid,
        },
        callback: res => {
            if (res.code === 200) {
                resolve(true);
            } else {
                resolve(false);
            }
        },
    });
});
```
