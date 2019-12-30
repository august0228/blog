### 线程阻塞在SocketInputStream.socketRead0()

程序可能与多个远程应用通过多种协议交互：SOAP, REST, HTTP, HTTPS, JDBC, RMI...等
所有连接都通过 JDK `java.net` 层去做更底层的 `TCP-IP/Socket`操作。
在这一层，通过`SocketInputStream.socketRead0()` API `读取(read)`和`接收`远程主机的数据，有些可能立即返回了结果，有些可能需要一些时间返回结果，
有些可能根本不会返回结果，在完全接收响应之前，程序会被阻塞在 `SocketInputStream.socketRead0()` API。

### 解决方案：

如果一个用户发起了请求，后台线程被阻塞在`SocketInputStream.socketRead0()` 并且很久都没有恢复，用户可能会很困惑；
如果很多线程被阻塞并且很久都不能恢复，可能会给系统带来严重的可用性问题。

下面列出来几个可能的解决方案：
1. 设置超时时间
  - JVM Network settings
  - setSoTimeout
  - JDBC
  - Oracle JDBC
  
2. 验证网络连接
3. 检查远程应用程序
4. Non-blocking HTTP client

#### 设置超时时间 

大多数系统都没有设置超时时间来恢复`SocketInputStream.socketRead0()`，因此他们会被阻塞在`SocketInputStream.socketRead0()` 很长一段时间。
设置超时时间是一个很好的系统自我防御机制，最好都应该设置。下面是一些常见的超时设置:

##### JVM Network settings

可以通过两个强大的超时时间配置，适用于所有使用`java.net.URLConnection`的协议处理程序

- -Dsun.net.client.defaultConnectTimeout
- -Dsun.net.client.defaultReadTimeout

`-Dsun.net.client.defaultConnectTimeout` 指定连接到主机的超时时间(单位毫秒)。

对于 HTTP 连接 它是与 HTTP 服务器建立连接的超时时间；
对于 FTP 连接 它是与 FTP 服务器建立连接的超时时间；

`-Dsun.net.client.defaultReadTimeout` 指定从输入流(input stream) 读取数据的超时时间(单位毫秒)。

##### setSoTimeout

如果你直接使用`socket`编程，你可以考虑通过[setSoTimeout()](https://docs.oracle.com/javase/7/docs/api/java/net/Socket.html#setSoTimeout%28int%29) API 来设置超时时间(单位毫秒)。

如果远程程序在指定时间内没有返回结果，将会抛出 `java.net.SocketTimeoutException` 异常。
抛出异常会释放线程，使线程可以使用在其他请求上。

备注：如果超时时间设置为0，意味着无穷大，即没有超时时间，会一直阻塞。


##### JDBC

如果你正在使用 JDBC(Java DataBase Connectivity) to connect，
你可以考虑通过[setQueryTimeout()](https://docs.oracle.com/javase/7/docs/api/java/sql/Statement.html#setQueryTimeout(int)) API设置超时时间(单位秒)。

设置JDBC driver 等待从数据库获取执行结果的秒数，如果超过了时间，将会抛出 `SQLTimeoutException`异常，该API限制只能在 `execute`,`executeQuery()` 和 `executeUpdate()` 方法中使用。
默认情况下，对于执行语句的完成时间没有限制。

##### Oracle JDBC

如果你正在连接Oracle 数据库并且看到很多线程阻塞在 `SocketInputStream.socketRead0()`，你可以通过设置 `-Doracle.jdbc.ReadTimeout` 系统变量(单位毫秒)。

需要在系统启动时传递上述参数。

#### 验证网络连接

线程被阻塞在`SocketInputStream.socketRead0()` 也可能是网络连接和负载均衡的问题，有些问题的原因是远程程序可能没有发出适当的`ACK` 或者 `FIN` 包。
可以使用诸如Wireshark之类的TCP/IP跟踪工具来查看在你和远程应用程序之间的网络中发送的数据包。

#### 检查远程应用程序

有时候可能是因为远程程序的性能问题，处理事务速度会变慢。这种情况下，需要先去找到远程程序变慢的原因并去解决它。

#### Non-blocking HTTP client

你可以考虑使用像[Netty](http://netty.io/wiki/user-guide-for-5.x.html)没有阻塞操作的非阻塞HTTP客户端来挂起一个线程。


#### References
<hr/>

[Threads stuck in java.net.SocketInputStream.socketRead0]: https://blog.fastthread.io/2018/09/02/threads-stuck-in-java-net-socketinputstream-socketread0/