# 工具 && 技术

## 运营分析工具 -- 诸葛IO
    
* 作用：

    数据化分析工具，适用于iOS、Android应用及网站

* 语法：

    向 windows 中传入公共的方法
    ```
    window.zhuge = window.zhuge || [];
    window.zhuge.methods = "_init debug identify track trackLink trackForm page".split(" ");
    window.zhuge.factory = function(b) {
        return function() {
            var a = Array.prototype.slice.call(arguments);a.unshift(b);
            window.zhuge.push(a);
            return window.zhuge;
        }
    };
    for (var i = 0; i < window.zhuge.methods.length; i++) {
        var key = window.zhuge.methods[i];
        window.zhuge[key] = window.zhuge.factory(key);
    }
    window.zhuge.load = function(b, x) {
        if (!document.getElementById("zhuge-js")) {
            var a = document.createElement("script");
            var verDate = new Date();
            var verStr = verDate.getFullYear().toString()+ verDate.getMonth().toString() + verDate.getDate().toString();
            a.type = "text/javascript";
            a.id = "zhuge-js";
            a.async = !0;
            a.src = (location.protocol == 'http:' ? "http://sdk.zhugeio.com/zhuge.min.js?v=" : 'https://zgsdk.zhugeio.com/zhuge.min.js?v=') + verStr;
            a.onerror = function(){
                window.zhuge.identify = window.zhuge.track = function(ename, props, callback) {
                    if(callback && Object.prototype.toString.call(callback) === '[object Function]')callback();
                };
            };
            var c = document.getElementsByTagName("script")[0];
            c.parentNode.insertBefore(a, c);
            window.zhuge._init(b, x)
        }
    };
    window.zhuge.load('db630a48aa614ee784df54cc5d0cdabb', {debug: false});//配置应用的AppKey
    ```

    调用该 window 方法
    ```
    window.zhuge && window.zhuge.track('车系详情-参数配置', {
        '操作' : '点击参数配置',
    });
    ```

* 注意：

    用户手机或网站数据分析，分析用户的操作，应该相当于某些公司做的'数据埋点'。

## 服务器代理

* 正向代理

    A 通过 B 向 C 发送请求
    ```
    A -- 客户端（请求方）
    B -- 正向代理服务器（vpn厂商）
    C -- 目标服务器（接收方）
    ```

* 反向代理

    A 通过 B 向 空闲的 Cn 发荣请求
    ```
    A -- 客户端（请求方）
    B -- 反向代理服务器（Nginx。。。）
    Cn -- n个C中，空闲的Cn服务器（接收方）
    ```

* 安装 Nginx -- Windows

    - Nginx 网址： http://nginx.org/en/download.html
    - 将下载的 Nginx 解压至合适的盘（D）对应的目录下
    - 打开 cmd 窗口，进入到对应 nginx 目录下，执行命令 'start nginx.exe',启动安装 Nginx
    - 安装成功后，打开 任务管理器，查看是否启动 'nginx.exe' 进程
    - 在浏览器地址栏输入：127.0.0.1，会看到 nginx 欢迎界面

        如果未显示，查看 error 日志，查看 80 端口是否被占用了，
        
        或是cmd命令进入dos下执行：netstat -aon | findstr :80 查看 80 端口是否被占用，如果占用，那么你需要修改注册表，如下步骤：

            1、打开注册表：regedit
            
            2、找到：HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\HTTP
            
            3、找到一个 REG_DWORD 类型的项 Start，将其改为 0
            
            4、重启系统，System 进程不会占用 80 端口，再次执行 start nginx.exe 命令了。

    - 查看 nginx 配置文件 -- nginx.conf

        ```
        #user  nobody;
 
        #指定nginx进程数
        
        worker_processes  1;
        
        #全局错误日志及PID文件
        
        #error_log  logs/error.log;
        
        #error_log  logs/error.log  notice;
        
        #error_log  logs/error.log  info;
        
        #pid        logs/nginx.pid;
        
        events {
        
            # 连接数上限
        
            worker_connections  1024;
        
        }
        
        #设定http服务器，利用它的反向代理功能提供负载均衡支持
        
        http {
        
            #设定mime类型,类型由mime.type文件定义
        
            include       mime.types;
        
            default_type  application/octet-stream;
        
            #设定日志格式
        
            #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
        
            #                  '$status $body_bytes_sent "$http_referer" '
        
            #                  '"$http_user_agent" "$http_x_forwarded_for"';
        
            #使用哪种格式的日志
        
            #access_log  logs/access.log  main;
        
            #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，    
        
            sendfile        on;
        
            #tcp_nopush     on;
        
            #连接超时时间
        
            #keepalive_timeout  0;
        
            keepalive_timeout  65;
        
            #开启gzip压缩 ，压缩html
        
            #gzip  on;
        
            #设定负载均衡的服务器列表 支持多组的负载均衡,可以配置多个upstream  来服务于不同的Server.
        
            #nginx 的 upstream 支持 几 种方式的分配
        
            #1)、轮询（默认） 每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
        
            #2)、weight 指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。 跟上面样，指定了权重。
        
            #3)、ip_hash 每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题。 
        
            #4)、fair      
        
            #5)、url_hash #Urlhash
        
            upstream mysvr {
            
                #weigth参数表示权值，权值越高被分配到的几率越大   
            
                #1.down 表示单前的server暂时不参与负载
            
                #2.weight 默认为1.weight越大，负载的权重就越大。     
            
                #3.backup： 其它所有的非backup机器down或者忙的时候，请求backup机器。所以这台机器压力会最轻。  
            
                #server 192.168.1.116  down;
            
                #server 192.168.1.116  backup;
            
                server 192.168.1.121  weight=1;
            
                server 192.168.1.122  weight=2;
        
            }
            #配置代理服务器的地址，即Nginx安装的服务器地址、监听端口、默认地址
        
            server {
        
                #1.侦听80端口
        
                listen       80;
        
                #对于server_name,如果需要将多个域名的请求进行反向代理，可以配置多个server_name来满足要
                server_name  localhost;
                #charset koi8-r;
                #access_log  logs/host.access.log  main;
                location / {
        
                    # 默认主页目录在nginx安装目录的html子目录。
        
                    root   html;
        
                    index  index.html index.htm;           
        
                    proxy_pass http://mysvr; #跟载均衡服务器的upstream对应   
        
                }
        
                #error_page  404              /404.html;
                # redirect server error pages to the static page /50x.html
        
                ## 定义错误提示页面
        
                #error_page   500 502 503 504  /50x.html;
        
                #location = /50x.html {
        
                #    root   html;
        
                #}
        
                # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        
                #
        
                #location ~ \.php$ {
        
                #    proxy_pass   http://127.0.0.1;
        
                #}
        
        
                # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        
                #
        
                #location ~ \.php$ {
        
                #    root           html;
        
                #    fastcgi_pass   127.0.0.1:9000;
        
                #    fastcgi_index  index.php;
        
                #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        
                #    include        fastcgi_params;
        
                #}
        
                # deny access to .htaccess files, if Apache's document root
        
                # concurs with nginx's one
        
                #
        
                #location ~ /\.ht {
        
                #    deny  all;
        
                #}
        
            }
        
            # another virtual host using mix of IP-, name-, and port-based configuration
        
            #
        
            #server {
        
            #    listen       8000;
        
            #    listen       somename:8080;
        
            #    server_name  somename  alias  another.alias;
        
            #    location / {
        
            #        root   html;
        
            #        index  index.html index.htm;
        
            #    }
        
            #}
        
            # HTTPS server
        
            #
        
            #server {
        
            #    listen       443 ssl;
        
            #    server_name  localhost;
        
            #    ssl_certificate      cert.pem;
        
            #    ssl_certificate_key  cert.key;
        
            #    ssl_session_cache    shared:SSL:1m;
        
            #    ssl_session_timeout  5m;
        
        
        
            #    ssl_ciphers  HIGH:!aNULL:!MD5;
        
            #    ssl_prefer_server_ciphers  on;
        
        
        
            #    location / {
        
            #        root   html;
        
            #        index  index.html index.htm;
        
            #    }
        
            #}
        
        }

        ```

* 常用命令

    验证配置是否正确: nginx -t

    查看 Nginx 的版本号：nginx -V

    启动 Nginx：start nginx

    快速停止或关闭或退出 Nginx：nginx -s stop

    正常停止或关闭 Nginx：nginx -s quit

    配置文件修改重装载命令：nginx -s reload

* 常见问题

    - 1、端口号被占用，日志大致如下：
        ```
        2018/12/12 10:44:12 [emerg] 8800#5988: bind() to 0.0.0.0:80 failed 
        (10013: An attempt was made to access a socket in a way forbidden by its access permissions)
        ```

    - 2、Nginx 所在目录有中文名称，日志大致如下：
        ```
        2018/12/12 11:55:55 [emerg] 5664#8528: CreateFile() "D:\软件\nginx-1.7.8/conf/nginx.conf" failed 
        (1113: No mapping for the Unicode character exists in the target multi-byte code page)
        ```
    - 3、启用缓存时报错，日志大致如下：
        ```
        2018/12/12 17:26:50 [emerg] 17068#20356: shared zone "cache_one" has no equal addresses: 02CF0000 vs 02A20000
        2018/12/12 17:26:50 [alert] 11536#11228: worker process 17068 exited with code 1
        ```

    - 4、缺少nginx.pid文件，日志大致如下：
        ```
        nginx: [error] CreateFile() "E:\nginx\nginx-1.9.3/logs/nginx.pid" failed
        nginx: [error] CreateFile() "E:\nginx\nginx-1.9.3/logs/nginx.pid" failed
        nginx: [error] Open() "E:\nginx\nginx-1.9.3/logs/nginx.pid" failed
        ```

        解决方法:

            使用命令创建/logs/nginx.pid文件: nginx -c conf/nginx.conf

    - 5、bash: nginx: command not found

        有可能是你再linux命令行环境下运行了windows命令，

        如果你之前是允许 nginx -s reload 报错， 试下 ./nginx -s reload

        或者 用 windows 系统自带命令行工具运行

    - 6、windows 下 nginx 访问 web 目录提示 403 Forbidden

    　　在 windows 下 http 服务器 nginx 时，访问 web 目录提示 403 Forbidden，首先需要了解 nginx 出现 403 错误是什么意思：

    　　    403 Forbidden 表示你在请求一个资源文件但是 nginx 不允许你查看
            
            403 Forbidden 只是一个HTTP状态码，像404,200一样不是技术上的错误。

        找到 nginx.conf：

    　　    将user nobody 改为user root;重启ng，仍无效果。

        后发现nginx默认是不支持浏览目录的。

        找到 autoindex off 更改为 on。重启 nginx,正常访问。

        另外 Linux 下，nginx.conf 配置文件最上面的那个 “# user nobody” 要改成 “user ftpuser”，不然访问可能会出现 403 错误。

* 参考链接

    (Nginx 安装与配置)[https://blog.csdn.net/kingscoming/article/details/79042874]

    (Nginx 下载官网)[http://nginx.org/en/download.html]

## 命令行工具 Cmder

## 手机端 console

* 1.`html` 文件中直接引入库并初始化

```html
<script src="/vconsole.min.js"></script>
<script>
    new VConsole(); // 初始化 VConsole
</script>
```

* 2.安装 `vconsole` 依赖包

npm install vconsole

```js
import VConsole from 'vconsole';

// 开发环境时,使用 Vconsole
if (process.env.NODE_ENV !== 'production') {
    new VConsole();
}
```

## Sentry 网页报错日志

* 1.`html` 文件中直接引入并初始化

```html
<script src="https://cdn.ravenjs.com/3.26.2/raven.min.js" crossorigin="anonymous"></script>
<script>
    Raven.config('https://xxx.com/').install()
</script>
```

* 2.引入 `raven-js` 依赖包

npm install raven-js

```js
import Raven from 'raven-js';

// 生产环境时,使用 Sentry (报错日志)
if (process.env.NODE_ENV === 'production') {
    Raven.config('https://xxx.com/').install()
}
```
