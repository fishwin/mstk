### 1. http 常见错误码

<img src="../images/Lark20201016145929.png"  />

+ 1xx

  ​	1xx类状态码属于**提示消息**，是协议处理的一种**中间状态**，实际用到的比较少。

+ 2xx

  ​	2xx类状态码表示服务器**成功处理**了客户端的请求

  + 200 ok

    表示一切正常，如果是非HEAD请求，服务器返回的响应头中都会有body数据

  + 204 no content

    与200基本基本相同，但**响应头没有body数据**

  + 206 partial content

    一般应用于**http分块下载或断点续传**，表示响应返回的body中并不是全部数据，而是一部分。

+ 3xx

  ​	3xx表示重定向

  + 301 moved permanently

    **永久重定向**，表示请求的资源不存在了，需要使用另一个url进行访问

  + 302 found

    **临时重定向**，表示请求的资源还在，但是暂时需要另一个url来访问

  + 304 not modified

    不具有跳转的含义，表示资源未修改，重定向已存在的缓存文件，也称**缓存重定向**，用于缓存控制。

+ 4xx

  ​	4xx表示客户端发送的报文有误，服务端无法处理

  + 400 bad request

    表示客户端请求的报文有错误,是一个笼统的错误

  + 401 Unauthorized

    请求未经授权

  + 403 forbidden

    服务器禁止客户端访问相应的资源

  + 404 not found

    请求的资源不存在

+ 5xx

  ​	5xx表示服务端错误

  + 500 internal server error

    服务器内部错误，和400一样是一个笼统的错误

  + 501 not implemented

    表示客户端的请求服务端还不支持

  + 502 bad gateway

    一般是服务端进程异常或挂掉

  + 503 service unavailable

    表示服务器当前繁忙，无法响应

+ 参考

  https://www.cnblogs.com/xiaolincoding/p/12442435.html



### 2. 301与302有何区别，应用上有什么异同？



### 3. http请求与响应协议格式

+ HTTP请求（Request）格式

  请求行

  请求头

  空行

  请求体

  + get请求示例

  ```http
  GET请求：
  GET /562f25980001b1b106000338.jpg HTTP/1.1
  Host    img.mukewang.com
  User-Agent  Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.106 Safari/537.36
  Accept  image/webp,image/*,*/*;q=0.8
  Referer http://www.imooc.com/
  Accept-Encoding gzip, deflate, sdch
  Accept-Language zh-CN,zh;q=0.8
   
   
  ```

  + post请求示例

    ```http
    POST请求：
    POST / HTTP1.1
    Host:www.wrox.com
    User-Agent:Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; SV1; .NET CLR 2.0.50727; .NET CLR 3.0.04506.648; .NET CLR 3.5.21022)
    Content-Type:application/x-www-form-urlencoded
    Content-Length:40
    Connection: Keep-Alive
     
    name=Professional%20Ajax&publisher=Wiley
    ```

+ HTTP响应（Response）格式

  响应行

  响应头

  空行

  响应体

  + 示例

    ```http
    HTTP/1.1 200 OK
    Date: Fri, 22 May 2009 06:07:21 GMT
    Content-Type: text/html; charset=UTF-8
     
    <html>
          <head></head>
          <body>
                <!--body goes here-->
          </body>
    </html>
    ```



### 4. 常见http header

+ 常见的http请求头

  | 协议头          | 说明                                                         | 示例                                              |
  | --------------- | ------------------------------------------------------------ | ------------------------------------------------- |
  | Accept          | 可接受的响应内容类型（Content-Types）                        | Accept:text/plain                                 |
  | Accept-Charset  | 可接受的字符集                                               | Accept-Charset:utf-8                              |
  | Accept-Encoding | 可接受的响应内容编码方式                                     | Accept-Encoding: gzip, deflate                    |
  | Accept-Language | 可接受的响应内容的语言列表                                   | Accept-Language: en-US                            |
  | Authorization   | 认证信息                                                     | Authorization: Basic OSdjJGRpbjpvcGVuIANlc2SdDE== |
  | Cache-Control   | 是否使用缓存机制                                             | Cache-Control: no-cache                           |
  | Connection      | 客户端（浏览器）想要优先使用的连接类型                       | Connection: keep-alive Connection: Upgrade        |
  | Cookie          | 由之前服务器设置的一个Cookie                                 | Cookie: $Version=1; Skin=new;                     |
  | Content-Length  | 以8进制表示的请求体的长度                                    | Content-Length: 348                               |
  | Content-Type    | 请求体的MIME类型，用于POST和PUT                              | Content-Type: application/x-www-form-urlencoded   |
  | Date            | 日期时间                                                     | Date: Dec, 26 Dec 2015 17:30:00 GMT               |
  | Host            | 服务器域名                                                   | Host: www.itbilu.com:80 Host: www.itbilu.com      |
  | Origin          | 发起一个针对[跨域资源共享](http://itbilu.com/javascript/js/VkiXuUcC.html)的请求 | Origin: http://www.itbilu.com                     |
  | Referer         | 表示浏览器所访问的前一个页面                                 | Referer: http://itbilu.com/nodejs                 |
  | User-Agent      | 浏览器的身份标识字符串                                       | User-Agent: Mozilla/……                            |
  | Upgrade         | 要求服务器升级到一个高版本协议。                             | Upgrade: HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11    |

+ 常见的http响应头

  | 响应头                      | 说明                                                         | 示例                                               |
  | --------------------------- | ------------------------------------------------------------ | -------------------------------------------------- |
  | Access-Control-Allow-Origin | 指定哪些网站可以跨域源资源共享                               | Access-Control-Allow-Origin: *                     |
  | Age                         | 响应对象在代理缓存中存在的时间，以秒为单位                   | Age: 12                                            |
  | Allow                       | 对于特定资源的有效动作;                                      | Allow: GET, HEAD                                   |
  | Cache-Control               | 缓存相关                                                     | Cache-Control: max-age=3600                        |
  | Connection                  | 针对该连接所预期的选项                                       | Connection: close                                  |
  | Content-Encoding            | 响应资源所使用的编码类型                                     | Content-Encoding: gzip                             |
  | Content-Language            | 响就内容所使用的语言                                         | Content-Language: zh-cn                            |
  | Content-Length              | 响应消息体的长度，用8进制字节表示                            | Content-Length: 348                                |
  | Content-Type                | 当前内容的`MIME`类型                                         | Content-Type: text/html; charset=utf-8             |
  | Date                        | 此条消息被发送时的日期和时间                                 | Date: Tue, 15 Nov 1994 08:12:31 GMT                |
  | Last-Modified               | 所请求的对象的最后修改日期                                   | Last-Modified: Dec, 26 Dec 2015 17:30:00 GMT       |
  | Location                    | 用于在进行重定向，或在创建了某个新资源时使用。               | Location: http://www.itbilu.com/nodejs             |
  | Server                      | 服务器的名称                                                 | Server: nginx/1.6.3                                |
  | **Set-Cookie**              | 设置cookie                                                   | Set-Cookie: UserID=itbilu; Max-Age=3600; Version=1 |
  | Status                      | 通用网关接口的响应头字段，用来说明当前HTTP连接的响应状态。   | Status: 200 OK                                     |
  | Transfer-Encoding           | 表示实体传输给用户的编码形式。包括：`chunked`、`compress`、 `deflate`、`gzip`、`identity`。 | Transfer-Encoding: chunked                         |
  | Upgrade                     | 要求客户端升级到另一个高版本协议。                           | Upgrade: HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11     |
  | WWW-Authenticate            | 表示在请求获取这个实体时应当使用的认证模式。                 | WWW-Authenticate: Basic                            |

  

### 2. https 原理及握手过程

+ 原理

  <img src="../images/https.png" style="zoom:80%;" />

  https本质是在原http与tcp之间加入了SSL/TLS协议

  https = http + ssl/tls

+ https七次握手过程

  1. 客户端向服务端发送SYN消息，并进入SYN_SENT状态
  2. 服务端收到SYN报文后，回复SYN+ACK报文，并进入SYN_RECV状态
  3. 客户端回复ACK，双方进入ESTABLISHED状态，建立TCP连接
  4. 客户端向服务端发送Client Hello消息，并携带客户端支持的协议版本号、加密算法、**随机数**（32位）等信息。
  5. 服务端收到之后，向客户端发送Server Hello消息，并携带**证书**（包含公钥等信息）、服务端生成的**随机数**、会话ID、协议版本等信息。
  6. 客户端收到服务端消息后，验证证书，向服务端发送**Client key exchange**消息，客户端再生成一个随机数，并使用服务端传过来的证书公钥对其进行加密作为**预主密钥**（pre master key），并通知服务端之后加密传输，并发送**finished消息**。
  7. 服务端收到消息之后，回复**finished消息**，并通知客户端之后数据加密传输

  ![](../images/tls.png)

  注意：主密钥由双方产生的随机数和预主密钥生成

  ```c
  master_secret = PRF(pre_master_secret,"master secret",ClientHello.random+ServerHello.random)
  ```

+ 参考

  https://draveness.me/whys-the-design-https-latency/

  https://razeencheng.com/post/ssl-handshake-detail

  https://segmentfault.com/a/1190000021494676

### 3. http keep-alive



### 4. http能不能一次连接多次请求，不等后端返回



### 5. http 建立链接过程



### 6. cookie和session



### 7. 浏览器输入url后都发生了什么



### 8. DNS在网络层用哪个协议，为什么。



### 10. DNS解析过程



### 11. get和post请求的区别？



### 13. 常见的web漏洞有哪些.



### 14. CSRF与XSS



### 15. 接口幂等性



### 16. RESTFUL



### 17. RPC



### 18. C10K问题



### 19. 浏览器缓存



### 20. http2.0与http3.0

+ http1.0

  + **连接无法复用**：每次发起一个请求都需要建立新的tcp链接，完成请求后断开链接
  + 由于tcp慢启动机制，大文件请求效率低
  + **队头阻塞**：只有当前请求返回后，才能发送一个请求，如果当前请求一直阻塞的话，后面的请求都会陷入阻塞
  + **无状态**：导致每次都需要传递重复的header，协议开销大
  + **明文传输**

+ http1.1

  + **连接复用**：Connection:keep-alive 可以实现连接的复用，不必每次请求都建立一个新连接

  + **pipeline**：可多次发送多个请求，不需要等待当前请求返回，即可发送下一个请求，但响应必须按序返回，所以还是没有解决队头阻塞的问题

    ```text
    请求1 --> 请求2 --> 请求3 > 响应1 --> 响应2 --> 响应3
    ```

  + **明文传输**

  + **无状态**：导致每次都需要传递重复的header，协议开销大

+ http2.0

  + **二进制分帧**：将请求和响应的数据分割为更小的帧，并用二进制进行编码，解析效率更高。**多个帧可以乱序发送，根据帧首部的流标识可以重新组装**
  + **多路复用**：多个请求公用一个tcp连接，可**并行交错**的发送多个请求/响应（基于二进制分帧，可乱序发送，并可根据帧首部标识进行重新组装），它们之间互不影响。
  + **头部压缩**：采用HPACK算法，不发送重复的header，减少协议开销
  + **server push**：服务端可主动推送消息给客户端

+ http3.0

  + 基于QUIC协议（QUIC协议基于UDP实现）
    + 实现了类似TCP的流量控制、传输可靠性的功能
    + 快速握手：基于udp，可实现0-rtt或1-rtt建立连接
    + 集成tls
    + 多路复用：彻底解决队头阻塞问题

+ http1.1中的connection:keep-alive复用tcp连接与http2.0中的多路复用有什么区别

  http1.1中的pipeline可同时发送多个请求，但响应必须按序返回，当一个请求阻塞后，后面的请求也会阻塞，无法解决队头阻塞的问题。http2.0多路复用，由于数据被分为多个帧，多个请求与响应可以并行交错的进行，相互之间不会影响，在一定程度上解决了队头阻塞的问题（并没有彻底解决）。

+ 为什么说http2.0没有彻底解决队头阻塞的问题

  由于多个请求复用一个tcp连接，当网络不稳定出现**丢包**时，tcp的**重传**机制会导致数据包重传并等待确认，这时，这个链接上的数据包都需要等待，就会阻塞这个链接上的所有请求。这时，http2的性能可能不如http1，因为http1建立多个tcp链接，相互之间不会产生影响。

+ 参考

  https://www.infoq.cn/article/kU4OkqR8vH123a8dLCCJ

  https://my729.github.io/blog/internetwork/http%E7%89%88%E6%9C%AC%E5%8C%BA%E5%88%AB.html#http2-0%E7%89%B9%E6%80%A7

  

### 21. **对称加密与非对称加密**



### 22. SQL注入



### 23. Http缓存过程原理



### 24. 使用http长链接的优缺点







