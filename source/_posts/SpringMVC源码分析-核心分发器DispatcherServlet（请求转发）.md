---
title: SpringMVC源码分析--核心分发器DispatcherServlet（请求转发）
date: 2017-05-27 14:15:02
categories: [SpringMVC]
tags: [SpringMVC源码分析]
---
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffxpu07otbj20go07r749.jpg)
本文将分析SpringMVC的核心分发器DispatcherServlet的处理请求的过程，让读者了解SpringMVC是如何处理请求的。

原生的Servlet 生命周期可被定义为从创建直到毁灭的整个过程。
以下是 Servlet 遵循的过程：
1. Servlet 通过调用 init () 方法进行初始化。
2. Servlet 调用 service() 方法来处理客户端的请求。
3. Servlet 通过调用 destroy() 方法终止（结束）。

最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

SpringMVC的处理方式是基于Servlet的，所以也跳不出这个圈子。

## SpringMVC请求流程图
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzwfp78swj20nw0badgy.jpg)

![](http://ww1.sinaimg.cn/large/91ddf859gy1ffzylyzsszj212e0iqmxs.jpg)
SpringMVC核心处理流程：
1. DispatcherServlet前端控制器接收发过来的请求，交给HandlerMapping处理器映射器

2. HandlerMapping处理器映射器，根据请求路径找到相应的HandlerAdapter处理器适配器（处理器适配器就是那些拦截器或Controller）

3. HandlerAdapter处理器适配器，处理一些功能请求，返回一个ModelAndView对象（包括模型数据、逻辑视图名）

4. ViewResolver视图解析器，先根据ModelAndView中设置的View解析具体视图

5. 然后再将Model模型中的数据渲染到View上

这些过程都是以DispatcherServlet为中轴线进行的.