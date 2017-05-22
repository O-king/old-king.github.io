---
title: SpringMVC源码分析--核心分发器DispatcherServlet（初始化）
date: 2017-05-17 18:31:23
categories: Technology
tags: [SpringMVC源码分析]
---
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffubaw9xlaj206y024dfp.jpg)
本文将分析SpringMVC的核心分发器DispatcherServlet的初始化过程以及处理请求的过程，让读者了解这个入口Servlet的作用。

## SpringMVC配置
1. 指明Servlet，在配置文件web.xml中如下配置：	
```xml
<!-- Spring MVC servlet -->
<servlet>
    <servlet-name>springMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath*:spring/spring-mvc.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>springMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```
> load-on-startup：表示启动容器时初始化该Servlet；
> url-pattern：表示哪些请求交给Spring Web MVC处理， “/” 是用来定义默认servlet映射的。也可以如“*.html”表示拦截所有以html为扩展名的请求。
 
2. 配置SpringMVC信息 
	```xml
	<!-- 自动扫描该包，使SpringMVC认为包下用了@controller注解的类是控制器 -->
	<context:component-scan base-package="com.springmvc.example"/>
	
	<!-- 启动SpringMVC的注解功能，完成请求和注解POJO的映射 -->
	<bean class="org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter">
	</bean>
	
	<!-- 定义跳转的文件的前后缀，视图模式配置-->
	<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	    <!-- 这里的配置我的理解是自动给后面action的方法return的字符串加上前缀和后缀，变成一个可用的url地址 -->
	    <property name="prefix" value="/jsp/"/>
	    <property name="suffix" value=".jsp"/>
	</bean>
	```
	2.1. **配置扫描路径**
	2.2. **启用注解功能**
	2.3. **视图配置信息：前缀和后缀**


至此，SpringMVC的简单配置已结束，下面让我们来具体看一下DispatcherServlet的源码。

## SpringMVC初始化流程图
![](http://ww1.sinaimg.cn/large/91ddf859gy1ffoj4iu5rhj20qd0gddgi.jpg)

## DispatcherServlet初始化流程分析
```java
	/**
	 * This implementation calls {@link #initStrategies}.
	 */
	@Override
	protected void onRefresh(ApplicationContext context) {
		initStrategies(context);
	}

	/**
	 * Initialize the strategy objects that this servlet uses.
	 * <p>May be overridden in subclasses in order to initialize further strategy objects.
	 */
	protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		//初始化请求地址
		initHandlerMappings(context);
		//初始化请求解析器
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
```
初始化流程在FrameworkServlet类中流转，建立了上下文后，通过**onRefresh(ApplicationContext context)**方法的回调，进入到DispatcherServlet类中。

以其中**initHandlerMappings(context)**方法为例，分析一下这些SpringMVC编程元素的初始化策略，其他的方法，都是以类似的策略初始化的。
### 关键代码片段
```java
	/**
	 * Create a List of default strategy objects for the given strategy interface.
	 * <p>The default implementation uses the "DispatcherServlet.properties" file (in the same
	 * package as the DispatcherServlet class) to determine the class names. It instantiates
	 * the strategy objects through the context's BeanFactory.
	 * @param context the current WebApplicationContext
	 * @param strategyInterface the strategy interface
	 * @return the List of corresponding strategy objects
	 */
	@SuppressWarnings("unchecked")
	protected <T> List<T> getDefaultStrategies(ApplicationContext context, Class<T> strategyInterface) {
		String key = strategyInterface.getName();
		String value = defaultStrategies.getProperty(key);
		if (value != null) {
			String[] classNames = StringUtils.commaDelimitedListToStringArray(value);
			List<T> strategies = new ArrayList<T>(classNames.length);
			for (String className : classNames) {
				try {
					//通过反射，得到传入参数的Class对象
					Class<?> clazz = ClassUtils.forName(className, DispatcherServlet.class.getClassLoader());
					//关键
					Object strategy = createDefaultStrategy(context, clazz);
					strategies.add((T) strategy);
				}
				catch (ClassNotFoundException ex) {
					throw new BeanInitializationException(
							"Could not find DispatcherServlet's default strategy class [" + className +
									"] for interface [" + key + "]", ex);
				}
				catch (LinkageError err) {
					throw new BeanInitializationException(
							"Error loading DispatcherServlet's default strategy class [" + className +
									"] for interface [" + key + "]: problem with class file or dependent class", err);
				}
			}
			return strategies;
		}
		else {
			return new LinkedList<T>();
		}
	}
```

**DefaultAnnotationHandlerMapping.determineUrlsForHandler**：
```java
	/**
	 * Checks for presence of the {@link org.springframework.web.bind.annotation.RequestMapping}
	 * annotation on the handler class and on any of its methods.
	 */
	@Override
	protected String[] determineUrlsForHandler(String beanName) {
		ApplicationContext context = getApplicationContext();
		Class<?> handlerType = context.getType(beanName);
		RequestMapping mapping = context.findAnnotationOnBean(beanName, RequestMapping.class);
		if (mapping != null) {
			// @RequestMapping found at type level
			this.cachedMappings.put(handlerType, mapping);
			Set<String> urls = new LinkedHashSet<String>();
			String[] typeLevelPatterns = mapping.value();
			if (typeLevelPatterns.length > 0) {
				// @RequestMapping specifies paths at type level
				String[] methodLevelPatterns = determineUrlsForHandlerMethods(handlerType, true);
				for (String typeLevelPattern : typeLevelPatterns) {
					if (!typeLevelPattern.startsWith("/")) {
						typeLevelPattern = "/" + typeLevelPattern;
					}
					boolean hasEmptyMethodLevelMappings = false;
					for (String methodLevelPattern : methodLevelPatterns) {
						if (methodLevelPattern == null) {
							hasEmptyMethodLevelMappings = true;
						}
						else {
							String combinedPattern = getPathMatcher().combine(typeLevelPattern, methodLevelPattern);
							addUrlsForPath(urls, combinedPattern);
						}
					}
					if (hasEmptyMethodLevelMappings ||
							org.springframework.web.servlet.mvc.Controller.class.isAssignableFrom(handlerType)) {
						addUrlsForPath(urls, typeLevelPattern);
					}
				}
				return StringUtils.toStringArray(urls);
			}
			else {
				// actual paths specified by @RequestMapping at method level
				return determineUrlsForHandlerMethods(handlerType, false);
			}
		}
		else if (AnnotationUtils.findAnnotation(handlerType, Controller.class) != null) {
			// @RequestMapping to be introspected at method level
			return determineUrlsForHandlerMethods(handlerType, false);
		}
		else {
			return null;
		}
	}
```

通过遍历每一个类，查找**RequestMapping**注解，得到了所有controller层的请求Url。
最后讲Url和handle存入Map<String, Object>集合中，以备解析请求的时候快速找到controller。
**initHandlerMappings**的流程大概就是以上这些。

其他方法和**initHandlerMappings**类似，就不废话了。

## 总结
回顾整个SpringMVC的初始化流程，我们看到，通过HttpServletBean、FrameworkServlet、DispatcherServlet三个不同的类层次，
SpringMVC的设计者将三种不同的职责分别抽象，运用模版方法设计模式分别固定在三个类层次中。
其中：
- HttpServletBean完成的是<init-param>配置元素的依赖注入，
- FrameworkServlet完成的是容器上下文的建立，
- DispatcherServlet完成的是SpringMVC具体编程元素的初始化策略。