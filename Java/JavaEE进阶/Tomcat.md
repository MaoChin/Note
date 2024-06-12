# `Tomcat`

一个`Java`编写的`HTTP`服务器，既能支持静态页面，也能支持动态页面。

动态页面：可以根据不同的用户或不同的输入展示不同的页面。为了能够支持动态页面，`Tomcat`提供了一组`API`，封装了`HTTP`协议，这一组`API`就是`Servlet`。

`Tomcat`启动时需要的端口：8080（业务端口），8005（管理端口）

`Tomcat`要求的结构！！（webapp，WEB-INF，web.xml）必须是这样的。其中，web.xml 中的内容先不用管，找个模板就行。

![image-20240522152029912](E:\Note\Java\JavaEE进阶\Tomcat.assets\image-20240522152029912.png)

`Tomcat`的程序打包：`.war`包。然后把`war`包放到`Tomcat`的`webapps`目录下即可。

使用`Servlet`的大致流程：

1. 创建`Maven`项目
2. `pom.xml`中导入依赖
3. 创建必须的文件/文件夹（webapp，WEB-INF，web.xml）
4. 编写代码
5. 打包（需要在 `pom.xml`文件中进行配置  war包）
6. 把`war`包复制到`Tomcat`目录下（webapps）。启动Tomcat。
7. 在浏览器访问，注意 `context path`和`servlet path`

### `Tomcat`网页出错

1. 403  访问权限问题
2. 404  访问路径有问题；`web.xml`文件有问题....
3. 405  访问方式有问题（GET, POST...）；对应的访问方式没有实现....
4. 500  服务器内部出错，一般会抛异常

### `Servlet`

在使用时就是一个第三方的`jar`包，注意要和`tomcat`的版本对应上。

总的思想：自己写好一些代码，`Tomcat`会在合适的时候调用我们写的代码！！有点回调函数的意思。（不是我们自己  调用自己的方法）

==框架==：简单来说就是上述的思想，程序的主要部分已经写好了，我们自己写一些特定的代码，框架主体会在**合适的时候**调用我们写的方法。

框架的使用简化了开发，但是也限制了“自由”，只能继承框架内的类，重写方法使用。总的来说，限制“自由”是好事，提高了下限。

#### 1. 三个重点的类

`HttpServlet`，`HttpServletRequest`，`HttpServletResponse`

![image-20240522174339532](E:\Note\Java\JavaEE进阶\Tomcat.assets\image-20240522174339532.png)

本质来说，就是把我们写的代码“嵌入”到`Tomcat`中了。

#### `HttpServlet`

| 方法名称                     | 调用时机                                      |
| ---------------------------- | --------------------------------------------- |
| init （构造）                | 在 HttpServlet 实例化之后被调用一次           |
| destroy （析构）             | 在 HttpServlet 实例不再使用的时候调用一次     |
| service                      | 收到 HTTP 请求的时候调用                      |
| doGet                        | 收到 GET 请求的时候调用(由 service 方法调用)  |
| doPost                       | 收到 POST 请求的时候调用(由 service 方法调用) |
| doPut/doDelete/doOptions/... | 收到其他请求的时候调用(由 service 方法调用)   |

#### `HttpServletRequest`

有很多只读的方法，用于获取请求中的各种数据，比如 `HTTP`版本，请求方法，`context path`......

| 方法                                     | 描述                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| String getProtocol()                     | 返回请求协议的名称和版本。                                   |
| String getMethod()                       | 返回请求的 HTTP 方法的名称，例如，GET、POST 或 PUT。         |
| String getRequestURI()                   | 从协议名称直到 HTTP 请求的第一行的查询字符串中，返回该 请求的 URL 的一部分。 |
| String getContextPath()                  | 返回指示请求上下文的请求 URI 部分。                          |
| String getQueryString()                  | 返回包含在路径后的请求 URL 中的查询字符串。                  |
| Enumeration getParameterNames()          | 返回一个 String 对象的枚举，包含在该请求中包含的参数的名 称。 |
| String getParameter(String name)         | 以字符串形式返回请求参数的值，或者如果参数不存在则返回 null。 |
| String[] getParameterValues(String name) | 返回一个字符串对象的数组，包含所有给定的请求参数的值， 如果参数不存在则返回 null。 |
| Enumeration getHeaderNames()             | 返回一个枚举，包含在该请求中包含的所有的头名。               |
| String getHeader(String name)            | 以字符串形式返回指定的请求头的值。                           |
| String getCharacterEncoding()            | 返回请求主体中使用的字符编码的名称。                         |
| String getContentType()                  | 返回请求主体的 MIME 类型，如果不知道类型则返回 null。        |
| int getContentLength()                   | 以字节为单位返回请求主体的长度，并提供输入流，或者如果 长度未知则返回 -1。 |
| InputStream getInputStream()             | 用于读取请求的 body 内容. 返回一个 InputStream 对象          |

通过上述这些方法就能获取到前端的各种数据以及参数信息。

一般`url`中不要有中文，不然一些服务器/浏览器可能无法处理，当需要中文时进行`urlencode`编码。

```c
// url ip:port路径?参数&参数
// ? 后的所有参数叫做 Query String  （GET方法在URL里传参数！！）
// POST方法在body里传参！！！
ip:port/.../...?username=aaa&password=111
  
// 其中，POST的传参也有两种类型，一是上述的Query String形式  （form表单）
// 此时 Content-Type: application/x-www-form-urlencoded
// 二是json格式！
// 此时 Content-Type: application/json
  
// 对于Query String形式，无论参数在哪里，都是使用 getParameter 方法获取参数
// 而对于json格式则需要再引入处理json第三方库
```

#### `HttpServletResponse`

设置响应。

| 方法                                      | 描述                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| void setStatus(int sc)                    | 为该响应设置状态码。                                         |
| void setHeader(String name, String value) | 设置一个带有给定的名称和值的 header. 如果 name 已经存在, 则覆盖旧的值. |
| void addHeader(String name, String value) | 添加一个带有给定的名称和值的 header. 如果 name 已经存在, 不覆盖旧的值, 并列添加新的键值对 |
| void setContentType(String type)          | 设置被发送到客户端的响应的内容类型。                         |
| void setCharacterEncoding(String charset) | 设置被发送到客户端的响应的字符编码（MIME 字符集）例 如，UTF-8。 |
| void sendRedirect(String location)        | 使用指定的重定向位置 URL 发送临时重定向响应到客户端。        |
| PrintWriter getWriter()                   | 用于往 body 中写入文本格式数据.                              |
| OutputStream getOutputStream()            | 用于往 body 中写入二进制格式数据.                            |

#### 2. `Servlet`中的`session`和`cookie`

==`http`是无状态的==，但有时是需要我们保存一些信息的，这时就涉及到cookie和session。

`cookie`：浏览器持久化存储数据的一种机制（==`cookie`存在客户端浏览器==），使用键值对格式保存数据。服务器要使用`cookie`完成业务逻辑。

一般在服务端可以通过`cookie`从数据库中拿到更多的信息，在把这些信息加载到内存中，这些加载到内存中的信息就是`session`。（==`session`存在服务端 内存中==，不是持久化的，但是可以配置持久化）

正是因为如此，`cookie`也可以理解为`sessionId`（或者说`cookie` 中存有`sessionId`），通过`sessionId`可以拿到`session`。服务端有很多的`session`。

简单理解：

`cookie` -》医院的患者卡，只能标识患者，没有太多信息

`session` -》 医院系统里患者的信息



`cookie`和`session`的一个重要功能：实现登录。首次登录时服务器会设置`session`，并把`sessionId`返回给客户端，客户端保存在`cookie`中，后续客户端就有了这个唯一的`sessionId`，就不需要再登录了，也会访问到其特定的内容。

##### `HttpServletRequest`   

| 方法                     | 描述                                                         |
| ------------------------ | ------------------------------------------------------------ |
| HttpSession getSession() | 在服务器中获取会话. 参数如果为 true, 则当不存在会话时新建会话; 参数如果 为 false, 则当不存在会话时返回 null |
| Cookie[] getCookies()    | 返回一个数组, 包含客户端发送该请求的所有的 Cookie 对象. 会自动把 Cookie 中的格式解析成键值对. |

##### `HttpServletResponse`  

| 方法                          | 描述                          |
| ----------------------------- | ----------------------------- |
| void addCookie(Cookie cookie) | 把指定的 cookie 添加到响应中. |

##### `HttpSession`  

操作键值对。

| 方法                                         | 描述                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| Object getAttribute(String name)             | 该方法返回在该 session 会话中具有指定名称的对象，如果没 有指定名称的对象，则返回 null. |
| void setAttribute(String name, Object value) | 该方法使用指定的名称绑定一个对象到该 session 会话            |
| boolean isNew()                              | 判定当前是否是新创建出的会话                                 |

##### `Cookie`  

也是操作键值对。

| 方法                           | 描述                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| String getName()               | 该方法返回 cookie 的名称。名称在创建后不能改变。(这个值是 Set Cooke 字段设置给浏览器的) |
| String getValue()              | 该方法获取与 cookie 关联的值                                 |
| void setValue(String newValue) | 该方法设置与 cookie 关联的值。                               |

## 3. token

`cookie`和`session`是`session`存在服务端，并把`sessionId`返回给客户端，客户端把`sessionId`存在`cookie`中。

`token`方式：服务端生成`token`，返回给客户端；后续客户端访问时携带`token`就可以了（一般在`http`请求的`header`中），服务端可以校验`token`的合法性！！！==`token`不是加密，只是不能被伪造。==	
