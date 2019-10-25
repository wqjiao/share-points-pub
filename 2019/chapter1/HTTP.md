# HTTP 及 HTTPs 协议

Tcp HTTP HTTP1 HTTP2

## HTTP 接口

### 请求方法

如果请求头中存在 X-HTTP-Method-Override 或参数中存在 _method（拥有更高权重），且值为GET, POST, PUT, DELETE, PATCH, OPTION, HEAD 之一，则视作相应的请求方式进行处理
GET, DELETE, HEAD 方法，参数风格为标准的 GET 风格的参数，如 url?a=1&b=2
POST, PUT, PATCH, OPTION 方法
默认情况下请求实体会被视作标准 json 字符串进行处理，当然，依旧推荐设置头信息的Content-Type 为 application/json
在一些特殊接口中（会在文档中说明），可能允许 Content-Type 为 application/x-www-form-urlencoded 或者 multipart/form-data ，此时请求实体会被视作标准 POST 风格的参数进行处理
关于方法语义的说明：

OPTIONS 用于获取资源支持的所有 HTTP 方法
HEAD 用于只获取请求某个资源返回的头信息
GET 用于从服务器获取某个资源的信息
完成请求后返回状态码 200 OK
完成请求后需要返回被请求的资源详细信息
POST 用于创建新资源
创建完成后返回状态码 201 Created
完成请求后需要返回被创建的资源详细信息
PUT 用于完整的替换资源或者创建指定身份的资源，比如创建 id 为 123 的某个资源
如果是创建了资源，则返回 201 Created
如果是替换了资源，则返回 200 OK
完成请求后需要返回被修改的资源详细信息
PATCH 用于局部更新资源
完成请求后返回状态码 200 OK
完成请求后需要返回被修改的资源详细信息
DELETE 用于删除某个资源
完成请求后返回状态码 204 No Content

* https://www.kancloud.cn/kancloud/http-api-guide/56258


### URL 通用语法

scheme>://<user>:<password>@<host>:<port>/<path>;<params>?<query>#<frag>

* scheme: 协议，常见的有 http（80），https（443），mailto，ftp（21），rtsp，rtspu，file。
* user：用户名。
* password： 密码。
* host：主机。
* port： 端口。
* params: 参数。通常为 key=value。
* query：查询参数或查询字符串。
* frag: 片段（在浏览器中会被解析为 window.location.hash）。

### ttp(s)中的语法

http://test.com:8080/user/index.html?id=1&nickName=test#/list

在浏览器中被解析后的格式为：

```
{
    protocol: 'http:', // 协议
    host: 'test.com:8080', // 主机名或域名，带端口号
    hostname: 'test.com', // 主机名或域名，不带端口号
    port: '', // 端口号, http默认80，https默认443
    path: '/user/index.html', // 路径
    query: '?id=1&nickName=test', // 查询字符串
    hash: '#list', //片段或者哈希
}
```

## HTTP 工作原理
http://www.ybao.org/book/http/2490.html

HTTP 协议是 Hyper Text Transfer Protocol(超文本传输协议)的缩写，是用于从万维网服务器传输超文本到本地浏览器的传送协议。
HTTP 是一个基于 TCP/IP 通信协议来传递数据(HTML 文件，图片文件，查询结果等)。

### 简介

HTTP 协议工作于客户端 -- 服务端架构之上，浏览器作为HTTP客户端通过URL向HTTP服务端即Web服务器发送所有请求。

Web 服务器有 Apache服务器、IIS服务器等
Web服务器根据接收到的请求后，向客户端发送响应信息。
HTTP默认端口号是 80，并隐式显示，可修改成需要的端口号。

HTTP 三点注意事项:

* HTTP 是无连接：无连接的含义是限制每次链接只处理一起请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可节省传输时间。
* HTTP是媒体独立的：这意味着，只要客户端和服务器知道如何处理的数据内容，任何类型的数据都可以通过HTTP发送，客户端以及服务器指定使用适合的MIME-type内容类型。
* HTTP协议是无状态协议：无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可以导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

以下是 HTTP 协议的大致通信流程：
```
Web Browser <-HTTP Protocol-> HTTP Server <--> CGI Program <--> Database
```
HTTP 是一个通信协议，HTTP 客户端发起请求并创建端口。HTTP 服务器在端口监听客户端的请求。HTTP 服务器在接收到请求后则返回状态和所请求的内容。

### 消息结构

HTTP 是基于客户端/服务端(C/S)的架构模型，通过一个可靠的链接来交换信息，是一个无状态的请求/响应协议。
一个HTTP "客户端"是一个应用程序(Web 浏览器或其他任何客户端)，通过连接到服务器达到向服务器发送一个或多个HTTP请求的目的。
一个HTTP "服务器"同样也是一个应用程序(通常是一个Web服务，如 Apache Web服务器或IIS服务器等)，通过接收客户端的请求并向客户端发送HTTP 响应数据。
HTTP 使用统一资源标识符来传输数据和建立连接。一旦建立连接后，数据消息就通过类似Internet邮件所使用的格式和多用途Internet邮件扩展来传送。

* 客户端请求消息

客户端发送一个 HTTP 请求到服务器的请求消息包括以下格式： 请求行、请求头部、空行和请求数据四部分组成

* 服务器响应消息

HTTP 响应也由四个部分组成，分别是： 状态行、消息报头、空行和响应正文。

### 常用方法

* GET 从服务器获取一份文档，不包含请求主体，属于一般性获取数据
* POST 向服务器发送需要处理的数据，包含请求主体
* PUT 将请求的请求主体部分储存在服务器上，包含请求主体
* DELETE 从服务器删除一份文档，不包含请求主体

### 不常用方法

* HEAD 只从服务器获取头文档的首部，不包含请求主体
* TRACE 对可能经过代理服务器传送到服务器上的报文进行追踪，不包含请求主体
* OPTIONS 决定可以在服务器上执行的方法

### 响应头信息

HTTP请求头提供了关于请求，响应或者其他的发送实体的信息。

* Allow	

服务器支持哪些请求方法（如GET、POST等）。

* Content-Encoding	

文档的编码（Encode）方法。只有在解码之后才可以得到Content-Type头指定的内容类型。利用gzip压缩文档能够显著地减少HTML文档的下载时间。Java的GZIPOutputStream可以很方便地进行gzip压缩，但只有Unix上的Netscape和Windows上的IE 4、IE 5才支持它。因此，Servlet应该通过查看Accept-Encoding头（即request.getHeader("Accept-Encoding")）检查浏览器是否支持gzip，为支持gzip的浏览器返回经gzip压缩的HTML页面，为其他浏览器返回普通页面。

* Content-Length

表示内容长度。只有当浏览器使用持久HTTP连接时才需要这个数据。如果你想要利用持久连接的优势，可以把输出文档写入 ByteArrayOutputStream，完成后查看其大小，然后把该值放入Content-Length头，最后通过byteArrayStream.writeTo(response.getOutputStream()发送内容。

* Content-Type	

表示后面的文档属于什么MIME类型。Servlet默认为text/plain，但通常需要显式地指定为text/html。由于经常要设置Content-Type，因此HttpServletResponse提供了一个专用的方法setContentType。

* Date	

当前的GMT时间。你可以用setDateHeader来设置这个头以避免转换时间格式的麻烦。

* Expires

应该在什么时候认为文档已经过期，从而不再缓存它？

* Last-Modified	

文档的最后改动时间。客户可以通过If-Modified-Since请求头提供一个日期，该请求将被视为一个条件GET，只有改动时间迟于指定时间的文档才会返回，否则返回一个304（Not Modified）状态。Last-Modified也可用setDateHeader方法来设置。

* Location

表示客户应当到哪里去提取文档。Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302。

* Refresh

表示浏览器应该在多少时间之后刷新文档，以秒计。除了刷新当前文档之外，你还可以通过setHeader("Refresh", "5; URL=http://host/path")让浏览器读取指定的页面。 
注意这种功能通常是通过设置HTML页面HEAD区的＜META HTTP-EQUIV="Refresh" CONTENT="5;URL=http://host/path"＞实现，这是因为，自动刷新或重定向对于那些不能使用CGI或Servlet的HTML编写者十分重要。但是，对于Servlet来说，直接设置Refresh头更加方便。 

注意Refresh的意义是"N秒之后刷新本页面或访问指定页面"，而不是"每隔N秒刷新本页面或访问指定页面"。因此，连续刷新要求每次都发送一个Refresh头，而发送204状态代码则可以阻止浏览器继续刷新，不管是使用Refresh头还是＜META HTTP-EQUIV="Refresh" ...＞。 

注意Refresh头不属于HTTP 1.1正式规范的一部分，而是一个扩展，但Netscape和IE都支持它。

* Server

服务器名字。Servlet一般不设置这个值，而是由Web服务器自己设置。

* Set-Cookie

设置和页面关联的Cookie。Servlet不应使用response.setHeader("Set-Cookie", ...)，而是应使用HttpServletResponse提供的专用方法addCookie。参见下文有关Cookie设置的讨论。

* WWW-Authenticate

客户应该在Authorization头中提供什么类型的授权信息？在包含401（Unauthorized）状态行的应答中这个头是必需的。例如，response.setHeader("WWW-Authenticate", "BASIC realm=＼"executives＼"")。 
注意Servlet一般不进行这方面的处理，而是让Web服务器的专门机制来控制受密码保护页面的访问（例如.htaccess）。
