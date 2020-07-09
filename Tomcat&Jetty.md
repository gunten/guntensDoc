# Tomcat & Jetty

而 Tomcat 和 Jetty 就是一个 Servlet 容器。为了方便使用，它们也具有 HTTP 服务器的功能，因此 **Tomcat 或者 Jetty 就是一个“HTTP 服务器 + Servlet 容器”，我们也叫它们 Web 容器。**





## HTTP协议

### HTTP 请求响应实例

<img src="Tomcat&amp;Jetty.assets/f58bf57649ec9eb35eb24e0679bb2514.png" alt="img" style="zoom:67%;" />

你可以看到，HTTP 请求数据由三部分组成，分别是**请求行、请求报头、请求正文**。当这个 HTTP 请求数据到达 Tomcat 后，Tomcat 会把 HTTP 请求数据字节流解析成一个 Request 对象，这个 Request 对象封装了 HTTP 所有的请求信息。接着 Tomcat 把这个 Request 对象交给 Web 应用去处理，处理完后得到一个 Response 对象，Tomcat 会把这个 Response 对象转成 HTTP 格式的响应数据并发送给浏览器。

我们再来看看 HTTP 响应的格式，HTTP 的响应也是由三部分组成，分别是**状态行、响应报头、报文主体**。同样，我还以极客时间登陆请求的响应为例。

<img src="Tomcat&amp;Jetty.assets/84f4fe4c411dfb9fd83a1d53cf2915b7.png" alt="img" style="zoom:67%;" />



### Cookie 和 Session

HTTP 协议有个特点是无状态，请求与请求之间是没有关系的。这样会出现一个很尴尬的问题：Web 应用不知道你是谁。因此 HTTP 协议需要一种技术让请求与请求之间建立起联系，并且服务器需要知道这个请求来自哪个用户，于是 Cookie 技术出现了。



#### Cookie 技术

**Cookie 是 HTTP 报文的一个请求头**，Web 应用可以将用户的标识信息或者其他一些信息（用户名等）存储在 Cookie 中。用户经过验证之后，每次 HTTP 请求报文中都包含 Cookie，这样服务器读取这个 Cookie 请求头就知道用户是谁了。**Cookie 本质上就是一份存储在用户本地的文件，里面包含了每次请求中都需要传递的信息。** 由于是以明文的方式存储，这样就造成了非常大的安全隐患

cookie有两个重要属性：

- domain字段 ：表示浏览器访问这个域名时才带上这个cookie
- path字段：表示访问的URL是这个path或者子路径时才带上这个cookie

跨域说的是，我们访问两个不同的域名或路径时，希望带上同一个cookie，跨域的具体实现方式有很多..



#### Session 技术

**Session 可以理解为服务器端开辟的存储空间，里面保存了用户的状态**，用户信息以 Session 的形式存储在服务端。

当用户请求到来时，服务端可以把用户的请求和用户的 Session 对应起来。那么 Session 是怎么和请求对应起来的呢？答案是通过 Cookie，浏览器在 Cookie 中填充了一个 Session ID 之类的字段用来标识请求。

具体工作过程是这样的：服务器在创建 Session 的同时，会为该 Session 生成唯一的 Session ID，服务端通过set-cookie放在http的响应头里 ,然后浏览器写到cookie里，后续每次请求就会自动带上来了。发到客户端的只有 Session ID，这样相对安全，也节省了网络流量，因为不需要在 Cookie 中存储大量用户信息。