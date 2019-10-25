# Expires Cache-Control

## 强缓存 && 协商缓存

    Cache-Control: no-cache	必须先与代理服务器确认是否更改，
        然后在在决定使用缓存还是请求，类似于协商缓存（304）
        
    Cache-Control: no-store 才是真正的不缓存数据到本地
    
    Cache-Control: public 可以被所有用户缓存（多用户共享），包括终端和CDN等中间代理服务器
    
    Cache-Control: private 只能被终端浏览器缓存（而且是私有缓存），不允许中继缓存服务器进行缓存
    
    Cache-Control: must-revalidate如果缓存内容失效，请求必须发送服务器进行验证
    
    Cache-Control: max-age=s	缓存内容在s秒后失效，仅HTTP1.1可用


## 合理的缓存方案

    HTML：使用协商缓存。

    CSS & JS & 图片：使用强缓存，文件命名带上hash值。

## 参考链接
    
    https://juejin.im/post/5c136bd16fb9a049d37efc47