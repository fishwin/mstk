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





### 2. https 原理及握手过程



### 3. http keep-alive



### 4. http能不能一次连接多次请求，不等后端返回



### 5. http 建立链接过程



### 6. cookie和session



### 7. 浏览器输入url后都发生了什么



### 8. DNS在网络层用哪个协议，为什么。



### 9. HTTP协议的请求报文和响应报文格式



### 10. DNS解析过程



### 11. get和post请求的区别？



### 13. 常见的web漏洞有哪些.



### 14. CSRF与XSS



### 15. 接口幂等性



### 16. RESTFUL



### 17. RPC



### 18. C10K问题



### 19. 浏览器缓存



### 20. http2.0



### 21. **对称加密与非对称加密**



### 22. SQL注入



### 23. Http缓存过程原理



### 24. 使用http长链接的优缺点







