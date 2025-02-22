# HTTP协议

## 基础概念

- URI

![批注 2020-03-07 204209](/assets/批注%202020-03-07%20204209.png)

- 请求报文

![202037204550](/assets/202037204550.png)

- 响应报文

![202037204611](/assets/202037204611.png)

## HTTP方法

- GET

获取资源

- HEAD

与GET类似，但不返回报文的实体主体

- POST

主要用来传输数据

- PUT

上传文件

- PATCH

对资源进行部分修改

- DELETE

删除文件

- OPTIONS

查询指定的 URL 能够支持的方法

- CONNECT

要求在与代理服务器通信时建立隧道

- TRACE

服务器会将通信路径返回给客户端

## 状态码

分类  | 分类描述
--- | -----------------------
1** | 信息，服务器收到请求，需要请求者继续执行操作
2** | 成功，操作被成功接收并处理
3** | 重定向，需要进一步的操作以完成请求
4** | 客户端错误，请求包含语法错误或无法完成请求
5** | 服务器错误，服务器在处理请求的过程中发生了错误

### 1XX

100 Continue ：表明到目前为止都很正常，客户端可以继续发送请求或者忽略这个响应

### 2XX

- 200 OK
- 204 No Content ：请求已经成功处理，但是返回的响应报文不包含实体的主体部分。一般在只需要从客户端往服务器发送信息，而不需要返回数据时使用
- 206 Partial Content ：表示客户端进行了范围请求，响应报文包含由 Content-Range 指定范围的实体内容

### 3XX

- 301 Moved Permanently ：永久性重定向
- 302 Found ：临时性重定向
- 303 See Other ：和 302 有着相同的功能，但是 303 明确要求客户端应该采用 GET 方法获取资源
- 304 Not Modified ：如果请求报文首部包含一些条件，例如：If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since，如果不满足条件，则服务器会返回 304 状态码
- 307 Temporary Redirect ：临时重定向，与 302 的含义类似，但是 307 要求浏览器不会把重定向请求的 POST 方法改成 GET 方法

### 4XX

- 400 Bad Request:语法错误
- 401 Unauthorized:需要认证
- 403 Forbidden:请求被拒绝
- 404 Not Found

### 5XX

- 500 Internal Server Error ：服务器正在执行请求时发生错误
- 503 Service Unavailable ：服务器暂时处于超负载或正在进行停机维护，现在无法处理请求

## 具体应用

### 连接管理

- 短连接

每进行一次 HTTP 通信就要新建一个 TCP 连接

- 长连接

从 HTTP/1.1 开始默认是长连接的，如果要断开连接，需要由客户端或者服务器端提出断开，使用 Connection : close
在 HTTP/1.1 之前默认是短连接的，如果需要使用长连接，则使用 Connection : Keep-Alive

- 流水线

流水线是在同一条长连接上连续发出请求，而不用等待响应返回，这样可以减少延迟

### Cookie

Cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器之后向同一服务器再次发起请求时被携带上，用于告知服务端两个请求是否来自同一客户端

#### 用途

- 会话状态管理
- 个性化设置
- 浏览器行为跟踪

#### 创建过程

服务的响应头Set-Cookie头部：

```html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry
```

客户端之后对同一服务器发送请求时，都会在请求头Cookie头部带上这个Cookie

```html
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

#### 分类

- 会话期Cookie：浏览器关闭之后它会被自动删除，没有指定过期时间就是会话期Cookie
- 持久性 Cookie：指定过期时间（Expires）或有效期（max-age）之后就成为了持久性的 Cookie

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2020 07:28:00 GMT;
```

#### 作用域

Domain 标识Cookie在哪些域名下有效，如果不指定，默认是当前文档的主机

如果指定了Domain，则一般包括子域名，如baidu.com，包含map.baidu.com

#### JS访问

JavaScript可以通过document.cookie来创建cookie或者访问非HttpOnly的Cookie

#### HttpOnly

标记为 HttpOnly 的 Cookie 不能被 JavaScript 脚本调用

```html
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2020 07:28:00 GMT; Secure; HttpOnly
```

#### Secure

标记为 Secure 的 Cookie 只能通过被 HTTPS 协议加密过的请求发送给服务端

#### Session

Session是通过在服务端生成一个key，使用这个key为索引在服务器端存放用户信息，后将这个key作为cookie返回给客户端，让客户端使用这个key来操作

应该注意 Session ID 的安全性问题，不能让它被恶意攻击者轻易获取，那么就不能产生一个容易被猜到的 Session ID 值。此外，还需要经常重新生成 Session ID。在对安全性要求极高的场景下，还需要使用二重验证的方式

## 特点

- 基于TCP/IP的高级协议
- 默认端口号:80
- 基于请求/响应模型的:一次请求对应一次响应
- 无状态的：每次请求之间相互独立，不能交互数据

#### 浏览器禁用cookie

当浏览器无法使用Cookie，只能使用session，此外，session id也不能通过cookie来传递，而是需要通过URL传参的方式来传递，如wap时代的sid

#### Cookie与Session

比较类别 | Session | Cookie
---- | ------- | ------
存储方式 | 服务端     | 客户端
大小限制 | 无       | 有
安全   | 较安全     | 较不安全

## 缓存

### 优点

- 缓解服务器压力
- 提升客户端速度

### 实现方法

- 代理服务器缓存
- 客户端缓存

### Cache-Control

- 禁止进行缓存

```html
Cache-Control: no-store
```

- 强制确认缓存

只有当缓存资源有效时，才能使用这个响应

```html
Cache-Control: no-cache
```

- 私有缓存

只能单独给用户使用，一般用在浏览器

```html
Cache-Control: private
```

- 公共缓存

可以被多个用户使用，一般存储在代理服务器中

```html
Cache-Control: public
```

- 缓存过期

出现在响应报文，超过这个时间 缓存就被认为过期

```html
Cache-Control: max-age=31536000
```

Expires 首部字段也可以用于告知缓存服务器该资源什么时候会过期

```html
Expires: Wed, 04 Jul 2012 08:26:05 GMT
```

### 缓存验证

ETag 是资源的唯一标识

```html
If-None-Match: "82e22293907ce725faf67773957acd12"
```

如果服务器接收到ETage后，判断资源没有发生改变，会返回一个304

Last-Modified 首部字段也可以用于缓存验证，如果响应首部字段里含有这个信息，客户端可以在后续的请求中带上 If-Modified-Since 来验证缓存。服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200 OK。如果请求的资源从那时起未经修改，那么返回一个不带有实体主体的 304 Not Modified 响应报文

### 内容协商

#### 服务端驱动

客户端设置Accept、Accept-Charset、Accept-Encoding、Accept-Language等首部，服务端根据这些首部返回特定资源

#### 代理驱动

服务器返回 300 Multiple Choices 或者 406 Not Acceptable，客户端从中选出最合适的那个资源

#### vary

一个客户端发送了一个包含 Accept-Language 首部字段的请求之后，源服务器返回的响应包含 Vary: Accept-Language 内容，缓存服务器对这个响应进行缓存之后，在客户端下一次访问同一个 URL 资源，并且 Accept-Language 与缓存中的对应的值相同时才会返回该缓存

### 内容编码

内容编码有：gzip、compress、deflate、identity

浏览器发送 Accept-Encoding 首部，其中包含有它所支持的压缩算法，以及各自的优先级。服务器则从中选择一种，使用该算法对响应的消息主体进行压缩，并且发送 Content-Encoding 首部来告知浏览器它选择了哪一种算法

### 范围请求

- Range

请求报文中添加 Range 首部字段指定请求的范围

```html
Range: bytes=0-1023
```

成功的话服务器返回的响应包含 206 Partial Content

请求的范围越界的情况下，服务器会返回 416 Requested Range Not Satisfiable 状态码

不支持范围请求的情况下，服务器会返回 200 OK 状态码

- Accept-Range

用于告知客户端是否能处理范围请求，可以处理使用 bytes，否则使用 none

### 分块传输

Chunked Transfer Encoding，可以把数据分割成多块，让浏览器逐步显示页面

### 多部分对象集合

一份报文主体内可含有多种类型的实体同时发送，每个部分之间用 boundary 字段定义的分隔符进行分隔

如

```html
Content-Type: multipart/form-data; boundary=AaB03x

--AaB03x
Content-Disposition: form-data; name="submit-name"

Larry
--AaB03x
Content-Disposition: form-data; name="files"; filename="file1.txt"
Content-Type: text/plain

... contents of file1.txt ...
--AaB03x--
```

### 虚拟主机

HTTP/1.1 使用虚拟主机技术，使得一台服务器拥有多个域名，并且在逻辑上可以看成多个服务器

### 通信数据转发

#### 代理

- 目的
  - 缓存
  - 负载均衡
  - 网络访问控制
  - 访问日志记录
- 正向代理

用户可以察觉正向代理的存在

![2020389347](/assets/2020389347.png)

- 反向代理

反向代理一般位于内部网络中，用户察觉不到

![20203893445](/assets/20203893445.png)

#### 网关

网关服务器会将 HTTP 转化为其它协议进行通信，从而请求其它非 HTTP 服务器的服务

#### 隧道

使用 SSL 等加密手段，在客户端和服务器之间建立一条安全的通信线路

## 重定向原理

当服务端对客户端进行重定向时，会设置一个Location响应头，并将状态码设置为302

客户端（浏览器）接收到这样的响应之后，就会跳转到Location里面的网址

## HTTPS

### HTTP的问题

- 明文通信
- 无法确认通信方
- 无法验证报文完整性

### 原理

1. 客户端向服务端发送HTTPS请求
2. 服务端收到HTTPS请求返回公钥证书
3. 客户端收到服务端的公钥证书，验证是否有效（验证颁发机构、过期时间等等）
4. 如果有效，生成一个随机数用公钥加密，然后发送给服务端
5. 服务端使用私钥将该随机数解密，然后用该随机数作为密钥加密一串字符给客户端
6. 如果客户端解密这串字符成功，这串字符将作为接下来客户端与服务端通信的密钥

这个过程的关键在于密钥传递使用了非对称加密，数据传输采用了对称加密

所以这就保证了对称加密的密钥不会通过网络直接传输，之所以数据传输采用了对称加密，主要是因为非对称加密性能很低

### 证书

通过使用 证书 来对通信方进行认证

数字证书认证机构（CA，Certificate Authority）是客户端与服务器双方都可信赖的第三方机构

服务器的运营人员向 CA 提出公开密钥的申请，CA 在判明提出申请者的身份之后，会对已申请的公开密钥做数字签名，然后分配这个已签名的公开密钥，并将该公开密钥放入公开密钥证书后绑定在一起

### 完整性保护

SSL 提供报文摘要功能来进行完整性保护

### HTTPS的缺点

- 加解密有性能损失
- 证书授权需要高额费用

### nginx配置证书

```
 server {
     ....  
     ssl on;
     ssl_certificate fullchain.pem;
     ssl_certificate_key privkey.pem;
 }
```

## ### HTTP/2.0

### HTTP/1.x缺陷

- 使用多个连接提升性能
- 没有压缩请求与响应
- 不支持资源优先级

### 二进制分帧

![20203894642](/assets/20203894642.png)

只会有一个TCP连接，一个连接会有任意数量的双向数据流

一个数据流会有一个一个唯一的标识符，一个数据流可以承载一来一回双向信息

消息是请求消息或者响应消息

帧是最小的通信单位，不同数据流的帧可以交错发送，然后根据唯一标识符来重新组装

![20203895038](/assets/20203895038.png)

### 服务端推送

HTTP/2.0 在客户端请求一个资源时，服务端会把相关的资源一起发送给客户端

![20203895124](/assets/20203895124.png)

### 首部压缩

HTTP/2.0 要求客户端和服务器同时维护和更新一个包含之前见过的首部字段表，从而避免了重复传输

![2020389543](/assets/2020389543.png)

不仅如此，HTTP/2.0 也使用 Huffman 编码对首部字段进行压缩

## GET与POST

GET 用于获取资源，而 POST 用于传输实体主体。

### 参数

GET是通过URL携带参数的，而 POST 的参数存储在实体主体中

### 安全

GET语义来说是安全的，因为GET操作只是获取资源

而POST的语义是不安全的，因为POST是上传数据

### 幂等性

幂等方法不应该具有副作用，所有的安全方法也都是幂等的

在正确实现的条件下，GET，HEAD，PUT 和 DELETE 等方法都是幂等的

### 可缓存

一般来说GET和HEAD是可缓存的，PUT和DELETE不可缓存，POST在大多数情况下不可缓存

## CDN

>CDN加速意思就是在用户和我们的服务器之间加一个缓存机制,动态获取IP地址根据地理位置，让用户到最近的服务器访问

![屏幕截图 2020-09-27 113806](/assets/屏幕截图%202020-09-27%20113806.png)

### 原理

1) 用户向浏览器提供要访问的域名；

2) 浏览器调用域名解析库对域名进行解析，由于CDN对域名解析过程进行了调整，所以解析函数库一般得到的是该域名对应的
CNAME记录，为了得到实际IP地址，浏览器需要再次对获得的CNAME域名进行解析以得到实际的IP地址；在此过程中，使用的全局负载均衡DNS解析，如根据地理位置信息解析对应的IP地址，使得用户能就近访问；

3) 此次解析得到CDN缓存服务器的IP地址，浏览器在得到实际的IP地址以后，向缓存服务器发出访问请求；

4) 缓存服务器根据浏览器提供的要访问的域名，通过Cache内部专用DNS解析得到此域名的实际IP地址，再由缓存服务器向此实际IP地址提交访问请求；

5) 缓存服务器从实际IP地址得得到内容以后，一方面在本地进行保存，以备以后使用，二方面把获取的数据返回给客户端，完成数据服务过程；

6) 客户端得到由缓存服务器返回的数据以后显示出来并完成整个浏览的数据请求过程。

### CDN 动态加速

通过动态的链路探测来寻找回源最好的一条路径

![屏幕截图 2020-09-27 114519](/assets/屏幕截图%202020-09-27%20114519.png)

## 跨域问题

同源策略限制了从同一个源加载的文档或脚本如何与来自另一个源的资源进行交互，所以通常情况下一个源无法通过ajax与另外一个源进行交互

![批注 2020-02-29 141744](/assets/批注%202020-02-29%20141744.png)

### 解决方案

- JSONP（缺陷很多）

服务端将返回数据封装成js函数调用并返回，客户端js通过动态加载script标签加载服务器的js数据，加载完成后执行封装的js函数获取数据

所以jsonp这种请求方式与ajax有着本质的不同

- 被调服务端设置响应头允许跨域

```java
response.setHeader("Access-Control-Allow-Origin","*");
```

- 后端请求转发

前端所在的服务端调用被调服务端，将结果返回给前端

- nginx反向代理

```
server {
    listen 80;
    server_name api.domain;
    location /api1 {
        proxy_pass http://outter_server;
    }
}
```

- 使用应用网关

使可以通过一个统一入口访问各个项目