# 前言    
**会话与状态管理**   
- Http协议是一个无状态的协议，WEB服务器本身不能识别出哪些请求是同一个浏览器发出的，浏览器每次请求都是完全孤立的。
- 即使HTTP1.1支持持续连接，但当用户有一段时间没有提交请求，连接也会关闭。
- 怎么才能实现网上商店中的购物车呢：某个用户从网站的登录页面登入后，在进入购物页面购物时，负责处理购物请求的服务器程序必须知道处理上一次请求的程序所得到的用户信息。
- 作为你WEB服务器，必须能够采用一种机制来唯一的标识一个用户，同时记录该用户的状态。
  


**会话与会话状态**  
- WEB应用中的会话是指一个客户端浏览器与WEB服务器之间连续发生的一系列请求和响应过程。
- WEB应用的会话状态是指WEB服务器与浏览器在会话过程中产生的状态信息，借助会话状态，WEB服务器能够把属于同一会话中的一系列的请求和响应关联起来。

**如何实现有状态的会话**   
- WEB服务端程序要能从大量的请求消息中区分出哪些请求消息属于同一个会话，即能识别出来自同一个浏览器的访问请求，这需要浏览器对其发出的每个请求消息都进行标识：属于同一个会话中的请求消息都附带同样的标识号，而属于不同会话的请求消息- 总是附带不同的标识号，这个标识号称之为会话ID（SessionID）.
- 在Servlet 规范中，常用以下两种机制完成会话跟踪
    - Cookie
    - Session


# Cookie 
Cookie简介
1. 完成会话跟踪的一种机制：采用的是客户端保持HTTP状态信息的方案
2. Cookie 是在浏览器访问WEB服务器的某个资源时，由WEB服务器在HTTP响应消息头中附带传送给浏览器的一个小文本文件。
3. 一旦WEB浏览器保存了某个Cookie,那么它在以后每次访问该WEB服务器时，都会在请求头中将这个 Cookie 回传给服务器。


**底层的实现原理**   

1. EB服务器通过在HTTP响应消息中增加Set-Cookie响应头字段将Cookie 信息发送给浏览器.
2. 浏览器则通过在HTTP消息请求中增加Cookie 请求头字段,将Cookie 回传给服务器

**在Servlet 程序中使用 Cookie**  
- Servlet API 提供了一个Cookie 类来封装Cookie 信息，它包含有生成Cookie 信息和提取Cookie 信息的各个属性的方法。
- Cookie 类的方法：
    - 构造方法:public Cookie(String name,String value)
    - getName()方法
    - setValue()与getValue()方法
    - setMaxAge()与getMaxAge()方法
    - setPath与getPath方法

- HttpServletResponse 接口中定义了一个addCookie方法，它用于在发送给浏览器的HTTP响应消息中增加一个Set-Cookie响应头字段。
- HttpServletRequest 接口中定义了一个getCookies 方法，它用于从HTTP请求消息的Cookie请求头字段中读取所有的Cookie项。


**Cookie 的发送**   
1.创建Cookie 对象
```
Cookie cookie = new Cookie("name", value);
```
2.设置最大时效

setMaxAge:设置Cookie 的最大时效，以秒为单位，

- 若为0，表示立即删除该Cookie.
- 若为负数，表示不存储该Cookie.
- 若为正数，表示该Cookie 的存储时间.
```
cookie.setMaxAge(0);
``` 

3.将Cookie 放入到Http响应报头
```
response.addCookie(cookie);
``` 

- 如果创建了一个cookie,并将它发送到浏览器，默认情况下它是一个会话级别的cookie,存储在浏览器的内存中，用户退出浏览器之后被删除，若希望浏览器将该cookie 存储在磁盘上，则需要使用maxAge，并给出一个以秒为单位的时间。将最大时效设为0则是命令浏览器删除该Cookie.
- 发送cookie 需要使用HttpServletResponse的addCookie 方法，将cookie 插入到一个Set-Cookie HTTP响应头中。由于这个方法并不修改任何之前指定的Set-Cookie报头，而是创建新的报头，因此将这个方法称为是addCookie,而非setCookie.   
- 

**会话Cookie 和持久化Cookie 的区别**

- 会话Cookie:如果不设置过期时间，则表示这个cookie 生命周期为浏览器会话期间，只要关闭浏览器窗口，cookie 就消失了。这种生命期为浏览器会话期的cookie ,会话cookie 一般不保存在硬盘上而是保存在内存里。
- 持久化cookie:设置了过期时间，浏览器把cookie保存在硬盘上，关闭后再次打开浏览器，这些cookie 依然有效直到超过设定的过期时间。
- 存储在硬盘上的cookie可以在不同的浏览器进程间共享，比如两个IE窗口，而对于保存在内存的cookie，不同的浏览器有不同的处理方式。

**cookie 的读取**     
1. 调用request.getCookies     
要获取浏览器发送来的cookie,这个调用返回Cookie 对象的数组，对应由HTTP请求中Cookie 报头输入的值。
```
Cookie[] cookies = request.getCookies();   
```
2.对数组进行循环，调用每个cookie的getName方法，直到找到感兴趣的cookie为止
```
if(cookies != null && cookies.length > 0){   
  for(Cookie cookie:cookies){   
     out.print(cookie.getName()+":"+cookie.getValue());   
     out.print("<br>");   
  }   
}
```
**Cookie 的作用范围**  

可以作用于当前目录和当前目录的子目录，但不能作用于当前目录的上一级目录.
可以通过setPath()方法设置Cookie的作用范围:cookie.setPath("/")，其中"/"代表站点的根目录。    


# Session   

**Session 机制**

- Session 机制采用的是在服务器端保持Http状态信息的方案。
- 服务器使用一种类似于散列表的结构（也可能就是使用散列表）来保存信息。
- 当程序需要为某个客户端的请求创建一个session 时，服务器首先检查这个客户端的请求里是否包含了session 标识（即sessionID）,如果已经包含一个sessionID则说明以前已经为此客户创建过session,服务器就按照session id 把这个session 检索出来使用(如果检索不到，可能会新建一个,这种情况可能会出现在服务端已经删除了该用户对应的session对象，但用户人为地在请求的URL后面附加上一个JSESSION参数)。如果客户请求不包含sessionId,则为此客户创建一个session 并且生成一个与此session相关联的sessionId,这个session id 将在本次响应中返回给客户端保存。


**保存session id 的几种方式**

- 保存session id 的方式可以采用cookie,这样在交互过程中浏览器可以自动的按照规则把这个标识发送给服务器.
- 由于 cookie 可以被人为的禁用，必须有其它的机制以便在cookie被禁用时仍然能够把session ID 传递回服务器，经常采用的一种技术叫做URL重写，就是把session id附加在URL路径后面，附加的方式也有两种，一种是作为URL路径的附加信息，另一种是作为查询字符串附加在URL后面。网络在整个交互过程中始终保持状态，就必须在每个客户端可能请求的路径后面都包含这个sessionID.   


**Sesion cookie**  
- session 通过SessionID来区分不同的客户,session 是以cookie或URL重写为基础的，默认使用cookie 来实现，系统会创造一个名为JSESSIONID的输出cookie,这称之为session cookie,以区别persistent cookies(也就是我们通常所说的cookie),session cookie 是存储于浏览器内存中的，并不是写到硬盘上的，通常看不到JESSIONID，但是当把浏览器的cookie禁止后，web服务器会采用URL重写的方式传递SessionID,这时地址栏能看到
- session cookie 针对某一次会话而言，会话结束session cookie也就随着消失了,而persistent cookie 是存在于客户端硬盘上的一段文本。
- 关闭浏览器，只会是浏览器端内存里的session cookie 消失，但不会使保存在服务器端的session 对象消失，同样也不会使已经保存到硬盘上的持久化cookie 消失    

**HttpSession 的生命周期**   
**一、什么时候创建HttpSession 对象**    
- 对于JSP：不是浏览器访问服务端的任何一个JSP,服务器都会立即创建一个 HttpSession 对象.
    - 若当前的JSP是客户端访问的当前WEB应用的第一个资源，且JSP的page指定session 属性为false，则浏览器不会为jsp 创建一个session对象.
    - 若当前JSP不是客户端访问的当亲WEB应用的第一个资源，且其它页面已经创建了一个HttpSession,那么服务器不会为当前JSP页面创建一个HttpSession对象,而会把和当前会话关联的那个HttpSession 对象返回给当前的JSP页面.
- 对于Servlet: 若Servlet 是客户端访问的第一个WEB应用的资源，则只有调用了request.getSession()或request.getSession(true)才会创建HttpServlet对象.    

 session="false" 表示当前页面禁用session隐含变量,但可以使用其他的显式的HttpSession对象.request.getSession()
 
- 在Servlet 中如何获取HttpSession对象
    - request.getSession(Boolean b):    
若b 为false.若没有和当前JSP页面关联的HttpSession对象,则返回null;若有，则返回HttpSession.
若b 为true.一定返回一个HttpSession 对象,若没有和当前JSP页面关联的HttpSession 对象,则服务器创建一个新的HttpSession对象返回.若有，直接返回关联的对象.
    - request.getSession():
等用于reuqest.getSession(true）   

**二、销毁**  
- 直接调用HttpSession的 invaliate() 使当前的Session对象失效.
- 服务器卸载了当前WEB应用.
- 超出HttpSession 的过期时间.默认是1800s,半个小时. 
    - 设置HttpSession的过期时间:session.setMaxInactiveInterval();单位为秒.
    - 在web.xml 文件中设置HttpSession的过期时间.在tomcat的config文件夹下的web.xml文件里，这是全局的.   
    
    ```
   <session-config>
        <!--这个30的单位是分钟-->
        <session-timeout>30</session-timeout>
    </session-config>
    ‵‵`