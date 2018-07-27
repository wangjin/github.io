---
title: Spring Web MVC 学习笔记
date: 2018-07-26 22:10:18
tags:
 - Spring
 - Spring MVC
---

# 1. Spring Web MVC
## 1.1. 简介
Spring Web MVC是基于Servlet API构建的原始Web框架，从一开始就包含在Spring框架中。正式名称“Spring Web MVC”来自其源模块spring-webmvc的名称，但它通常被称为“Spring MVC”。
<!--more-->
## 1.2. DispatcherServlet
和其他很多框架类似，Spring MVC围绕前端控制器模式设计，其中`DispatcherServlet`作为中央`Servlet`，为请求处理提供共享算法，而实际工作由可配置的委托组件执行。该模型非常灵活，支持多种工作流程。
`DispatcherServlet`与其他`Servlet`一样，需要根据Servlet规范要求，使用Java配置或在`web.xml`中配置进行声明和映射。反过来，`DispatcherServlet`使用Spring配置来发现请求映射，视图解析，异常处理等所需的委托组件。
下面是注册和初始化`DispatcherServlet`的Java配置示例。 Servlet容器自动检测此类（see Servlet Config）：
```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext servletCxt) {

        // 加载Spring Web应用程序配置
        AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
        ac.register(AppConfig.class);
        ac.refresh();

        // 创建并注册DispatcherServlet
        DispatcherServlet servlet = new DispatcherServlet(ac);
        ServletRegistration.Dynamic registration = servletCxt.addServlet("app", servlet);
        registration.setLoadOnStartup(1);
        registration.addMapping("/app/*");
    }
}
```
> 除了直接使用ServletContext API之外，也可以继承`AbstractAnnotationConfigDispatcherServletInitializer`并覆盖特定方法（请参阅Context Hierarchy下的示例）。

下面是注册和初始化`DispatcherServlet`的`web.xml`配置示例：
```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/app-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value></param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app</servlet-name>
        <url-pattern>/app/*</url-pattern>
    </servlet-mapping>

</web-app>
```
> Spring Boot遵循不同的初始化顺序。 Spring Boot使用Spring配置来引导自身和嵌入式Servlet容器，而不是挂钩到Servlet容器的生命周期。在Spring配置中检测`Filter`和`Servlet`声明，并在Servlet容器中注册。有关更多详细信息，请查看Spring Boot文档。

### 1.2.1. 上下文层次结构
`DispatcherServlet`使用扩展自普通`ApplicationContext`的`WebApplicationContext`来进行自己的配置。 `WebApplicationContext`有一个指向它与之关联的`ServletContext`和`Servlet`的链接。它还绑定到`ServletContext`，以便应用程序在需要的时候可以使用`RequestContextUtils`中的静态方法来查找`WebApplicationContext`。
对于许多应用程序而言，包含一个`WebApplicationContext`就已经足够了。也可以有一个上下文层次结构，其中一个根`WebApplicationContext`在多个`DispatcherServlet`（或其他`Servlet`）实例之间共享，每个实例都有自己的子`WebApplicationContext`配置。有关上下文层次结构功能的更多信息，请参阅`ApplicationContext`的其他功能。 
根`WebApplicationContext`通常包含基础架构bean，例如需要跨多个`Servlet`实例共享的数据存储库和业务服务。这些bean是有效继承的，可以在特定于`Servlet`的子`WebApplicationContext`中重写（即重新声明），该子`WebApplicationContext`通常包含给定`Servlet`本地的bean：

![mvc-context-hierarchy](/images/mvc-context-hierarchy.png)

以下是`WebApplicationContext`层次结构的示例配置：
```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[] { RootConfig.class };
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { App1Config.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/app1/*" };
    }
}
```
> 如果不需要应用程序上下文层次结构，则应用程序可以通过调用`getRootConfigClasses()`返回所有配置，调用`getServletConfigClasses()`则会返回`null`。

`web.xml`配置与以上Java配置方式相当：
```xml
<web-app>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>/WEB-INF/root-context.xml</param-value>
    </context-param>

    <servlet>
        <servlet-name>app1</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/app1-context.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>app1</servlet-name>
        <url-pattern>/app1/*</url-pattern>
    </servlet-mapping>

</web-app>
```
> 如果不需要应用程序上下文层次结构，则应用程序可以仅配置“根”上下文，并将`contextConfigLocation`Servlet参数设为空。

### 1.2.2. 特殊bean类型
`DispatcherServlet`委托特殊bean处理请求并呈现适当的响应。 “特殊bean”是指由Spring管理的，实现WebFlux框架约定的Object实例。那些通常带有内置约定，但也可以自定义其属性，扩展或替换它们。

下表列出了会被`DispatcherHandler`检测到的特殊bean：

| Bean type                                                    | Explanation                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HandlerMapping](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-handlermapping) | 将请求映射到处理程序以及用于预处理和后处理的拦截器列表。映射基于某些标准，其细节因`HandlerMapping`实现而异。 两个主要的`HandlerMapping`实现是`RequestMappingHandlerMapping`，它支持`@RequestMapping`带注解的方法和`SimpleUrlHandlerMapping`，它维护对处理程序的URI路径模式的显式注册。|
| HandlerAdapter                                               | 无论实际调用处理程序如何，都帮助`DispatcherServlet`调用映射到请求的处理程序。例如，调用带注解的控制器需要解析注解。 `HandlerAdapter`的主要目的是保护`DispatcherServlet`不受此类细节的影响。|
| [HandlerExceptionResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-exceptionhandlers) | 解决异常的策略，可能将它们映射到处理程序，或HTML错误视图或其他。请参阅Exceptions。|
| [ViewResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-viewresolver) | 解析从处理程序返回的基于String的逻辑视图名称，渲染实际`View`的返回给响应。 See [View Resolution](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-viewresolver) and [View Technologies](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-view). |
| [LocaleResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-localeresolver), [LocaleContextResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-timezone) | 解析客户端正在使用的区域设置以及可能的时区，以便能够提供国际化视图。See [Locale](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-localeresolver). |
| [ThemeResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-themeresolver) | 解析Web应用程序可以使用的主题，例如，提供个性化布局。 See [Themes](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-themeresolver). |
| [MultipartResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-multipart) | 用于在一些multipart解析库的帮助下解析multipart请求（例如，浏览器表单文件上载）的抽象。 See [Multipart resolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-multipart). |
| [FlashMapManager](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-flash-attributes) | 存储和检索“输入”和“输出”FlashMap，可用于将属性从一个请求传递到另一个请求，通常是通过重定向。 See [Flash attributes](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-flash-attributes). |

### 1.2.3. Web MVC配置
应用程序可以根据处理请求所需，声明特殊Bean类型中列出的基础结构bean。`DispatcherServlet`检查每个特殊bean的`WebApplicationContext`。如果没有匹配的bean类型，它将回退到`DispatcherServlet.properties`中列出的默认类型。
在大多数情况下，MVC Config是最佳起点。它以Java或XML声明所需的bean，并提供更高级别的配置回调API来支持自定义。
> Spring Boot依赖于MVC Java配置来配置Spring MVC，还提供了许多额外的便捷选项。

### 1.2.4. Servlet配置
在Servlet 3.0+环境中，您可以选择以编程方式配置Servlet容器作为替代方法，也可以与web.xml文件结合使用。下面是注册`DispatcherServlet`的示例：
```java
import org.springframework.web.WebApplicationInitializer;

public class MyWebApplicationInitializer implements WebApplicationInitializer {

    @Override
    public void onStartup(ServletContext container) {
        XmlWebApplicationContext appContext = new XmlWebApplicationContext();
        appContext.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");

        ServletRegistration.Dynamic registration = container.addServlet("dispatcher", new DispatcherServlet(appContext));
        registration.setLoadOnStartup(1);
        registration.addMapping("/");
    }
}
```
`WebApplicationInitializer`是Spring MVC提供的一个接口，可确保检测到你的实现并自动用于初始化任何Servlet 3容器。实现`WebApplicationInitializer`的抽象基类`AbstractDispatcherServletInitializer`通过简单地重写方法来指定servlet映射和`DispatcherServlet`配置的位置，使得注册`DispatcherServlet`变得更容易。

对于使用基于Java的Spring配置的应用程序，建议使用此方法：
```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    @Override
    protected Class<?>[] getRootConfigClasses() {
        return null;
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[] { MyWebConfig.class };
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
```

如果使用基于XML的Spring配置，则应直接`从AbstractDispatcherServletInitializer`扩展：
```java
public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

    @Override
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }

    @Override
    protected WebApplicationContext createServletApplicationContext() {
        XmlWebApplicationContext cxt = new XmlWebApplicationContext();
        cxt.setConfigLocation("/WEB-INF/spring/dispatcher-config.xml");
        return cxt;
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
```

`AbstractDispatcherServletInitializer`还提供了一种方便的方法来添加`Filter`实例并使它们自动映射到`DispatcherServlet`：
```java
public class MyWebAppInitializer extends AbstractDispatcherServletInitializer {

    // ...

    @Override
    protected Filter[] getServletFilters() {
        return new Filter[] {
            new HiddenHttpMethodFilter(), new CharacterEncodingFilter() };
    }
}
```
每个过滤器都根据其具体类型添加默认名称，并自动映射到`DispatcherServlet`。
`AbstractDispatcherServletInitializer`的`isAsyncSupported`受保护方法提供了一个单独的位置来启用`DispatcherServlet`上的异步支持以及映射到它的所有过滤器。默认情况下，此标志设置为`true`。
最后，如果还需要进一步自定义`DispatcherServlet`本身，则可以覆盖`createDispatcherServlet`方法。