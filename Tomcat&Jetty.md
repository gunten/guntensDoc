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

`Domain` 和 `Path` 标识定义了Cookie的*作用域：*即Cookie应该发送给哪些URL。

`Domain` 标识指定了哪些主机可以接受Cookie。如果不指定，默认为[当前文档的主机]（**不包含子域名**）。如果指定了`Domain`，则一般包含子域名。

- 如果设置 `Domain=mozilla.org`，则Cookie也包含在子域名中（如`developer.mozilla.org`）。

`Path` 标识指定了主机下的哪些路径可以接受Cookie（该URL路径必须存在于请求URL中）。以字符 `%x2F` ("/") 作为路径分隔符，子路径也会被匹配。

例如，设置 `Path=/docs`，则以下地址都会匹配：

- `/docs`
- `/docs/Web/`
- `/docs/Web/HTTP`

跨域说的是，我们访问两个不同的域名或路径时，希望带上同一个cookie，跨域的具体实现方式有很多..



#### Session 技术

**Session 可以理解为服务器端开辟的存储空间，里面保存了用户的状态**，用户信息以 Session 的形式存储在服务端。

当用户请求到来时，服务端可以把用户的请求和用户的 Session 对应起来。那么 Session 是怎么和请求对应起来的呢？答案是通过 Cookie，浏览器在 Cookie 中填充了一个 Session ID 之类的字段用来标识请求。

具体工作过程是这样的：服务器在创建 Session 的同时，会为该 Session 生成唯一的 Session ID，服务端通过set-cookie放在http的响应头里 ,然后浏览器写到cookie里，后续每次请求就会自动带上来了。发到客户端的只有 Session ID，这样相对安全，也节省了网络流量，因为不需要在 Cookie 中存储大量用户信息。



## Servlet规范和Servlet容器

<img src="Tomcat&amp;Jetty.assets/dfe304d3336f29d833b97f2cfe8d7801.jpg" alt="img" style="zoom: 50%;" />

Servlet 接口其实是 Servlet 容器跟具体业务类之间的接口。Servlet 接口和 Servlet 容器的出现，达到了 HTTP 服务器与业务类解耦的目的。

而 Servlet 接口和 Servlet 容器这一整套规范叫作 Servlet 规范。Tomcat 和 Jetty 都按照 Servlet 规范的要求实现了 Servlet 容器，同时它们也具有 HTTP 服务器的功能。



### Servlet 接口

 定义了下面五个方法：

```java
public interface Servlet {
    //在web.xml给 Servlet 配置参数
    void init(ServletConfig config) throws ServletException;
    
    ServletConfig getServletConfig();
    
    void service(ServletRequest req, ServletResponse res）throws ServletException, IOException;
    
    String getServletInfo();
    
    void destroy();
}
```

其中最重要是的 service 方法，具体业务类在这个方法里实现处理逻辑。这个方法有两个参数：ServletRequest 和 ServletResponse。ServletRequest 用来封装请求信息，ServletResponse 用来封装响应信息，**因此本质上这两个类是对通信协议的封装。**

有接口一般就有抽象类，抽象类用来实现接口和封装通用的逻辑，因此 Servlet 规范提供了 GenericServlet 抽象类，我们可以通过扩展它来实现 Servlet。虽然 Servlet 规范并不在乎通信协议是什么，但是大多数的 Servlet 都是在 HTTP 环境中处理的，因此 Servet 规范还提供了 HttpServlet 来继承 GenericServlet，并且加入了 HTTP 特性。这样我们通过继承 HttpServlet 类来实现自己的 Servlet，只需要重写两个方法：doGet 和 doPost。



### Servlet 容器

<img src="Tomcat&amp;Jetty.assets/b70723c89b4ed0bccaf073c84e08e115.jpg" alt="img" style="zoom:50%;" />



#### web应用

servlet规范下的web目录

> | -  MyWebApp
>       | -  WEB-INF/web.xml        -- 配置文件，用来配置Servlet等
>       | -  WEB-INF/lib/           -- 存放Web应用所需各种JAR包
>       | -  WEB-INF/classes/       -- 存放你的应用类，比如Servlet类
>       | -  META-INF/              -- 目录存放工程的一些信息

Servlet 容器通过读取配置文件，就能找到并加载 Servlet。

Servlet 规范里定义了 **ServletContext** 这个接口来对应一个 Web 应用。Web 应用部署好后，Servlet 容器在启动时会加载 Web 应用，并为每个 Web 应用创建唯一的 ServletContext 对象。你可以把 ServletContext 看成是一个全局对象，一个 Web 应用可能有多个 Servlet，这些 Servlet 可以通过全局的 ServletContext 来共享数据，这些数据包括 Web 应用的初始化参数、Web 应用目录下的文件资源等。由于 ServletContext 持有所有 Servlet 实例，你还可以通过它来实现 Servlet 请求的转发。



#### 扩展机制

引入了 Servlet 规范后，你不需要关心 Socket 网络通信、不需要关心 HTTP 协议，也不需要关心你的业务类是如何被实例化和调用的，但是如果有个性化需求，怎么办呢？

- Filter -> FilterChain。Filter 是干预过程的，它是过程的一部分，是基于过程行为的。
- Listener ->监听事件。 Listener 是基于状态的，任何行为改变同一个状态，触发的事件是一致的。



servlet、spring、springMVC启动时关系

> Tomcat&Jetty在启动时给每个Web应用创建一个全局的上下文环境，这个上下文就是ServletContext，其为后面的Spring容器提供宿主环境。
>
> Tomcat&Jetty在启动过程中触发容器初始化事件，Spring的ContextLoaderListener会监听到这个事件，它的contextInitialized方法会被调用，在这个方法中，Spring会初始化全局的Spring根容器，这个就是Spring的IoC容器，IoC容器初始化完毕后，Spring将其存储到ServletContext中，便于以后来获取。
>
> Tomcat&Jetty在启动过程中还会扫描Servlet，一个Web应用中的Servlet可以有多个，以SpringMVC中的DispatcherServlet为例，这个Servlet实际上是一个标准的前端控制器，用以转发、匹配、处理每个Servlet请求。
>
> Servlet一般会延迟加载，当第一个请求达到时，Tomcat&Jetty发现DispatcherServlet还没有被实例化，就调用DispatcherServlet的init方法，DispatcherServlet在初始化的时候会建立自己的容器，叫做SpringMVC 容器，用来持有Spring MVC相关的Bean。同时，Spring MVC还会通过ServletContext拿到Spring根容器，并将Spring根容器设为SpringMVC容器的父容器，请注意，Spring MVC容器可以访问父容器中的Bean，但是父容器不能访问子容器的Bean， 也就是说Spring根容器不能访问SpringMVC容器里的Bean。说的通俗点就是，在Controller里可以访问Service对象，但是在Service里不可以访问Controller对象。