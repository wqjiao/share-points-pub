# store 实现本地存储

将数据存储至 localstorage

* 基本用法

```javascript
store(key, data); // 存储单个字符串数据
store({key: data, key2: data2}); // 存储多个字符串数据
store(key); // 获取指定`key`的字符串数据
store("?key"); // 确定`key`是否存在
store(); // 获取所有键/数据
 
store.set(key, data[, overwrite]); // === store(key, data);
store.set({key: data, key2: data2}); // === store({key: data, key2: data});
store.get(key[, alt]); // === store(key);
store.get("?key"); // 确定`key`是否存在
store.get("key1", "key2", "key3"); // 获取 `key1`,`key2`,`key3` 数据
store.remove(key); // ===store(key, false)
store.clear(); // 清除所有 key/data
store.keys(); // 返回所有的键值数组
store.forEach(callback); // 循环遍历，返回 false 结束
store.search(string); // 搜索法
 
store.has(key); //⇒ 确定是否返回 true/false
 
//⇒ 提供处理数据的回调数据
store('test', (key,val) => {
    console.log(val) // 处理在这里得到的数据
    return [3,4,5] // 返回数据并存储数据
});
 
store(['key', 'key2'], (key) => {
    // 获取多个密钥的数据处理，返回并保存
    console.log('key:', key)
    return '逐个更改数据'
});
```

* 使用 StorageEvent 响应存储更改

```javascript
if (window.addEventListener) {
    window.addEventListener("storage",handle_storage,false);
} else if (window.attachEvent) {
    window.attachEvent("onstorage",handle_storage);
}
function handle_storage(e) {
    if (!e) {
        e=window.event;
    }
    //showStorage();
}
```
