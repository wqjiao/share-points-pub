# socket.io

socket.io 是一个可以在浏览器和服务器之间实现实时，双向和基于事件的通信。包括：

- Nodejs 服务器： [源码](https://github.com/socketio/socket.io) | [API](https://socket.io/docs/server-api/)
- 浏览器的 javascript 客户端(也可以从 Nodejs 运行): [源码](https://github.com/socketio/socket.io-client) | [API](https://socket.io/docs/client-api/)

* 可靠性

代理和负载均衡器。
个人防火墙和防病毒软件。

* 自动重新连接支持
* 断线检测
* 二进制支持
* 多路复用支持
* Rooms 执行
* 简单 API 实例
npm install --save socket.io

```javascript
// socket 通信地址
let socket_url = 'http://localhost:8080';
let socket = io(socket_url);

// socket 连接后 用户登录
socket.on('connection', function() {
    // emit 发送数据 {} -- 向服务器发送消息
    socket.emit('login', {
        socket_uid: userInfo.uid
    });

    // on 请求数据 {} -- top_navigation 监听名
    socket.on('top_navigation', function(data) {
        console.log('top_navigation', data)
    });

    // socket 关闭连接
    socket.on('disconnect', function() {
        console.log('user disconnected');
    });
});
```

当第一次连接时，事件触发顺序为：connecting -> connect
当失去连接时，事件触发顺序为：disconnect -> reconnecting（可能进行多次）-> connecting -> reconnect -> connect。

* 参考网址

[socket-docs](https://socket.io/docs/)
