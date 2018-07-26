---
title: Spring Web MVC 学习笔记
date: 2018-07-26 22:10:18
tags:
 - Spring
 - Spring MVC
---

# 1. Spring Web MVC
## 1.1 简介
Spring Web MVC是基于Servlet API构建的原始Web框架，从一开始就包含在Spring框架中。正式名称“Spring Web MVC”来自其源模块spring-webmvc的名称，但它通常被称为“Spring MVC”。
<!--more-->
## 1.2 DispatcherServlet
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

### 1.2.1 上下文层次结构
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