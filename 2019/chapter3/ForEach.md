## async await

* `forEach`

```js
function test() {
    let arr = [3, 2, 1]
    arr.forEach(async item => {
        console.log('item', item)
        const res = await fetch(item);
        console.log(res);
    });
    console.log('end');
}

function fetch(x) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve(x);
        }, 500 * x);
    })
}

test();
```

相当于

```js
async function a(item) {
    const res = await fetch(item)
    console.log(res);
}

function test() {
    let arr = [3, 2, 1]
    for (const item of arr) {
       a(item)
    }
    console.log('end')
}
test()
```

希望执行结果与实际执行结果对比

```
3   -- end
2   -- 1
1   -- 2
end -- 3
```

* `Promise.all()`

```js
async function test() {
    let arr = [3, 2, 1]
    await Promise.all(
        arr.map(async item => {
            const res = await fetch(item)
            console.log(res)
        })
    )
    console.log('end')
}
test();
```

* `for...of`

```js
async function test() {
    let arr = [3, 2, 1];
    for (const item of arr) {
        const res = await fetch(item);
        console.log(res);
    }
    console.log('end');
}
```

`for...of` 是通过迭代器的方式去遍历，等价于：

```js
async function test() {
    let arr = [3, 2, 1];
    const iterator = arr[Symbol.iterator]();
    let res = iterator.next();
    while (!res.done) {
        const value = res.value;
        const res1 = await fetch(value);
        console.log(res1);
        res = iterator.next();
    }
    console.log('end');
}
```