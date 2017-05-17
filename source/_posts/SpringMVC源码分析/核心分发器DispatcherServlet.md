---
title: SpringMVC源码分析--核心分发器DispatcherServlet
date: 2017-05-17 18:31:23
categories: Technology
tags: [SpringMVC源码分析]
---
本文将分析SpringMVC的核心分发器DispatcherServlet的初始化过程以及处理请求的过程，让读者了解这个入口Servlet的作用。

## SpringMVC配置
1. 指明Servlet，在配置文件web.xml中如下配置：	
![DispatherServlet](http://ww1.sinaimg.cn/large/91ddf859gy1ffof5rcl9jj20k004pglr.jpg)
> load-on-startup：表示启动容器时初始化该Servlet；
> url-pattern：表示哪些请求交给Spring Web MVC处理， “/” 是用来定义默认servlet映射的。也可以如“*.html”表示拦截所有以html为扩展名的请求。
 
2. 配置SpringMVC信息 
	2.1. **配置扫描路径**
	2.2. **启用注解功能**
	2.3. **视图配置信息：前缀和后缀**
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffoffsmj4kj20ml06jdg4.jpg)


至此，SpringMVC的简单配置已结束，下面让我们来具体看一下DispatcherServlet的源码。

## SpringMVC初始化流程图
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffoj4iu5rhj20qd0gddgi.jpg)

## DispatcherServlet源码分析

DispatcherServlet主要用作职责调度工作，本身主要用于控制流程，主要职责如下：

1. 文件上传解析，如果请求类型是multipart将通过MultipartResolver进行文件上传解析；
2. 通过HandlerMapping，将请求映射到处理器（返回一个HandlerExecutionChain，它包括一个处理器、多个HandlerInterceptor拦截器）；
3. 通过HandlerAdapter支持多种类型的处理器(HandlerExecutionChain中的处理器)；
4. 通过ViewResolver解析逻辑视图名到具体视图实现；
5. 本地化解析；
6. 渲染具体的视图等；
7. 如果执行过程中遇到异常将交给HandlerExceptionResolver来解析。
	
	
