# 将Guacamole集成到Spring mvc项目笔记
===============================================
## guacamole 环境的搭建
环境搭建
## 使用http连接集成
使用http连接没什么需要说明的, 直接按照guacamole的官网demo一步步的操作就行
## 使用websocket连接集成
### 说明
使用websocket连接guacamole, 具体代码不是我写的, 是使用guacamole官网给的实例中代码, 
[http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.0.0/source/guacamole-client-1.0.0.tar.gz](http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.0.0/source/guacamole-client-1.0.0.tar.gz), 路径:\guacamole-client-1.0.0\guacamole
具体使用到的代码类名如下
```java
GuacamoleServletContextListener.java	//监听器, 主要启动入口
GuacamoleStreamException.java	//异常类
HTTPTunnelRequest.java	//http请求实现类
ListenerFactory.java
ListenerService.java
ProviderFactory.java
RestrictedGuacamoleHTTPTunnelServlet.java	//http tunnel方法
RestrictedGuacamoleWebSocketTunnelEndpoint.java	// websocket tunnel方法
TunnelLoader.java	//接口 判断是否支持websocket方法
TunnelModule.java	// http servlet请求映射
TunnelRequest.java	//抽象类, 定义一些request 参数的验证
TunnelRequestService.java	//逻辑实现类, 连接guacamole的主要参数信息和代码
WebSocketTunnelModule.java	//websocket servlet请求映射
WebSocketTunnelRequest.java	//websocket请求实现类
```
也就是guacamole-1.0.0.war 的源代码 [http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.0.0/binary/guacamole-1.0.0.war](http://apache.org/dyn/closer.cgi?action=download&filename=guacamole/1.0.0/binary/guacamole-1.0.0.war)
因为guacamole.war 使用的web框架是Guice框架, 所以需要将web.xml中的一下配置放在最后
```xml
  <!-- Guice -->
  <filter>
    <filter-name>guiceFilter</filter-name>
    <filter-class>com.google.inject.servlet.GuiceFilter</filter-class>
  </filter>
  <filter-mapping>
    <filter-name>guiceFilter</filter-name>
    <url-pattern>/portal/guacamole/*</url-pattern>
  </filter-mapping>
  <listener>
    <listener-class>com.ean.online.web.socket.GuacamoleServletContextListener</listener-class>
  </listener>
```
### 准备
## 集成说明

## 