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
`DispatcherServlet`使用扩展自普通`ApplicationContext`的`WebApplicationContext`来进行自己的配置。`WebApplicationContext`有一个指向它与之关联的`ServletContext`和`Servlet`的链接。它还绑定到`ServletContext`，以便应用程序在需要的时候可以使用`RequestContextUtils`中的静态方法来查找`WebApplicationContext`。
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
`DispatcherServlet`委托特殊bean处理请求并渲染适当的响应。 “特殊bean”是指由Spring管理的，实现WebFlux框架约定的Object实例。那些通常带有内置约定，但也可以自定义其属性，扩展或替换它们。

下表列出了会被`DispatcherHandler`检测到的特殊bean：

| Bean type                                                    | Explanation                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [HandlerMapping](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-handlermapping) | 将请求映射到处理程序以及用于预处理和后处理的拦截器列表。映射基于某些标准，其细节因`HandlerMapping`实现而异。 两个主要的`HandlerMapping`实现是`RequestMappingHandlerMapping`，它支持`@RequestMapping`带注解的方法和`SimpleUrlHandlerMapping`，它维护对处理程序的URI路径模式的显式注册。|
| HandlerAdapter                                               | 无论实际调用处理程序如何，都帮助`DispatcherServlet`调用映射到请求的处理程序。例如，调用带注解的控制器需要解析注解。`HandlerAdapter`的主要目的是保护`DispatcherServlet`不受此类细节的影响。|
| [HandlerExceptionResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-exceptionhandlers) | 解决异常的策略，可能将它们映射到处理程序，或HTML错误视图或其他。请参阅Exceptions。|
| [ViewResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-viewresolver) | 解析从处理程序返回的基于String的逻辑视图名称，渲染实际`View`的返回给响应。 See [View Resolution](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-viewresolver) and [View Technologies](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-view). |
| [LocaleResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-localeresolver), [LocaleContextResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-timezone) | 解析客户端正在使用的区域设置以及可能的时区，以便能够提供国际化视图。See [Locale](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-localeresolver). |
| [ThemeResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-themeresolver) | 解析Web应用程序可以使用的主题，例如，提供个性化布局。 See [Themes](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-themeresolver). |
| [MultipartResolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-multipart) | 用于在一些分片解析库的帮助下解析分片请求（例如，浏览器表单文件上载）的抽象。 See [Multipart resolver](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-multipart). |
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

### 1.2.5. 处理流程

`DispatcherServlet`按如下方式处理请求：

- `WebApplicationContext`作为一个属性被搜索和绑定在请求中，流程中的控制器和其他元素都可以使用它。它默认绑定在键`DispatcherServlet.WEB_APPLICATION_CONTEXT_ATTRIBUTE下`。
- 语言环境解析器绑定到请求，以使进程中的元素能够解析处理请求时使用的语言环境（呈现视图，准备数据等）。如果您不需要区域设置解析，则不需要它。
- 主题解析器绑定到请求，以允许视图等元素确定要使用的主题。如果您不使用主题，则可以忽略它。
- 如果指定了分片文件解析程序，则会检查请求的分片;如果存在分批，请求将被包装在`MultipartHttpServletRequest`中，以供进程中的其他元素进一步处理。有关多部分处理的更多信息，请参见`Multipart解析器`。
- 搜索是否有适当的处理程序。如果找到处理程序，则执行与处理程序（预处理程序，后处理程序和控制器）关联的执行链以准备模型或渲染视图。或者，对于带注解的控制器，可以呈现响应（在`HandlerAdapter`内）而不是返回视图。
- 如果返回模型，则渲染视图。如果没有返回模型（可能是由于预处理器或后处理器拦截请求，可能是出于安全原因），则不会渲染任何视图，因为该请求可能已经完成。

`WebApplicationContext`中声明的`HandlerExceptionResolver`bean用于解决在请求处理期间抛出的异常。这些异常解析器允许自定义逻辑以解决异常。有关详细信息，请参阅Exceptions。
Spring `DispatcherServlet`还支持返回Servlet API所指定的最后修改日期。确定特定请求的最后修改日期的过程很简单：`DispatcherServlet`查找适当的处理程序映射并测试其是否实现`LastModified`接口。如果是的话，`LastModified`接口的`long getLastModified（request）`方法的值将返回给客户端。
您可以通过将Servlet初始化参数（`init-param`元素）添加到web.xml文件中的`Servlet`声明来自定义各个`DispatcherServlet`实例。有关支持的参数列表，请参阅下表。

*表 1. DispatcherServlet初始化参数*

| 参数                        | 说明                                                  |
| -------------------------------- | ------------------------------------------------------------ |
| contextClass                   | 实现`WebApplicationContext`的类，它实例化此Servlet使用的上下文。默认情况下，使用`XmlWebApplicationContext`。|
| contextConfigLocation          | 传递给上下文实例（由contextClass指定）的字符串，用于指示可以找到上下文的位置。该字符串可能包含多个字符串（使用逗号作为分隔符）以支持多个上下文。如果多个上下文位置的bean定义了两次，则最新位置优先。 |
| namespace                      | `WebApplicationContext`的命名空间。默认为`[servlet-name] -servlet`。 |
| throwExceptionIfNoHandlerFound | 在没有找到请求的处理程序时是否抛出`NoHandlerFoundException`。然后可以使用`HandlerExceptionResolver`捕获异常，例如通过`@ExceptionHandler`控制器方法，并处理任何其他方法。默认情况下，它设置为“false”，在这种情况下，`DispatcherServlet`将响应状态设置为404（NOT_FOUND），而不会引发异常。请注意，如果还配置了[默认servlet处理]()，则始终将未解析的请求转发到默认servlet，并且永远不会引发404。|

### 1.2.6. 拦截器
所有`HandlerMapping`实现都支持处理程序拦截器，当你要将特定功能应用于某些请求时（例如，检查主体），这些处理程序拦截器很有用。拦截器必须实现`org.springframework.web.servlet`包中的`HandlerInterceptor`接口并重写三个方法，这些方法应该提供足够的灵活性来执行各种预处理和后处理：
- `preHandle(..)` - 在执行实际处理程序之前
- `postHandle(..)` - 处理程序执行后
- `afterCompletion(..)` - 完成请求完成后

`preHandle(..)`方法返回一个布尔值。可以使用此方法来中断或继续执行链的处理。当此方法返回`true`时，处理程序执行链将继续;当它返回`false`时，`DispatcherServlet`假定拦截器本身已处理请求（例如，呈现了适当的视图），并且不继续执行执行链中的其他拦截器和实际处理程序。
有关如何配置[拦截器]()的示例，请参阅MVC配置一节中的拦截器。也可以通过各个`HandlerMapping`实现的setter方法直接注册它们。
请注意，`postHandle`对于`@ResponseBody`和`ResponseEntity`方法不太有用，因为响应是在`HandlerAdapter`中、`postHandle`之前编写和提交的。这意味着对响应进行任何更改都太晚了，例如添加额外的`header`。对于此类方案，您可以实现`ResponseBodyAdvice`并将其声明为[Controller Advice]()bean或直接在`RequestMappingHandlerAdapter`上进行配置。

### 1.2.7. 异常
如果在请求映射期间发生异常或从请求处理程序（如`@Controller`）抛出异常，`DispatcherServlet`将委托给`HandlerExceptionResolver`bean链以解决异常并提供备用处理，通常是一个错误响应。

下表列出了可用的`HandlerExceptionResolver`实现：

表2. *`HandlerExceptionResolver`实现*

| HandlerExceptionResolver                                     | 描述                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| SimpleMappingExceptionResolver                            | 异常类名称和错误视图名称之间的映射。用于在浏览器应用程序中渲染错误页面。 |
| [DefaultHandlerExceptionResolver]() | 解决Spring MVC引发的异常并将它们映射到HTTP状态代码。另请参阅ResponseEntityExceptionHandler和[REST API异常]()。 |
| ResponseStatusExceptionResolver                           | 使用@ResponseStatus注释解析异常，并根据注解中的值将它们映射到HTTP状态代码。 |
| ExceptionHandlerExceptionResolver                         | 通过在@Controller或@ControllerAdvice类中调用@ExceptionHandler方法来解决异常。请参阅[@ExceptionHandler方法]()。|

#### 解析器链
只需在Spring配置中声明多个`HandlerExceptionResolver`bean并根据需要设置其`order`属性，就可以形成异常解析器链。order属性越高，异常解析器定位的越晚。

`HandlerExceptionResolver`的合约指定它可以返回：
- 指向错误视图的`ModelAndView`
- 如果在解析程序中处理异常，则清空`ModelAndView`。
- 如果异常仍然未解决，则为`null`，以供后续解析器尝试;如果异常保留在最后，则允许传播到Servlet容器。

[MVC配置]()自动声明内置的解析器，用于默认的Spring MVC异常，`@ResponseStatus`带注解的异常，以及对`@ExceptionHandler`方法的支持。也可以自定义该列表或替换它。

#### 容器错误页面

如果任何`HandlerExceptionResolver`仍未解决异常并因此将其传播，或者如果响应状态设置为错误状态（即4xx，5xx），则Servlet容器可能会呈现HTML中的默认错误页面。要自定义容器的默认错误页面，可以在`web.xml`中声明错误页面映射：
```xml
<error-page>
    <location>/error</location>
</error-page>
```

鉴于上述情况，当异常传播或响应具有错误状态时，Servlet容器会在容器内对配置的URL进行ERROR调度（例如“/error”）。然后由`DispatcherServlet`处理，可以将其映射到`@Controller`，实现该控制器以返回带有模型的错误视图名称或渲染JSON响应，如下所示：
```java
@RestController
public class ErrorController {

    @RequestMapping(path = "/error")
    public Map<String, Object> handle(HttpServletRequest request) {
        Map<String, Object> map = new HashMap<String, Object>();
        map.put("status", request.getAttribute("javax.servlet.error.status_code"));
        map.put("reason", request.getAttribute("javax.servlet.error.message"));
        return map;
    }
}
```

> Servlet API没有提供在Java中创建错误页面映射的方法。但是，可以同时使用`WebApplicationInitializer`和最小的`web.xml`。

### 1.2.8. 视图解析
Spring MVC定义了`ViewResolver`和`View`接口，使你能够在浏览器中渲染模型，而无需自己使用特殊的视图技术。`ViewResolver`提供视图名称和实际视图之间的映射。`View`在移交给特定视图技术之前讲需要处理的数据准备好。

下表提供了有关`ViewResolver`层次结构的更多详细信息：
表3.*ViewResolver实现*

| ViewResolver                       | 描述                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| AbstractCachingViewResolver    | `AbstractCachingViewResolver`的子类缓存其解析的视图实例。缓存可提高某些视图技术的性能。可以通过将`cache`属性设置为`false`以关闭缓存。此外，如果必须在运行时刷新某个视图（例如，当修改FreeMarker模板时），则可以使用`removeFromCache(String viewName, Locale loc)`方法。 |
| XmlViewResolver                | `ViewResolver`的实现接受使用与Spring的XML bean工厂相同的DTD以XML编写的配置文件。默认配置文件是 `/WEB-INF/views.xml`。 |
| ResourceBundleViewResolver     | `ViewResolver`的实现在`ResourceBundle`中使用bean定义，由bundle基本名称指定，并且对于它应该解析的每个视图，它使用属性的值`[viewname].(class)`作为视图类，并使用属性的值`[viewname].url`作为视图url。示例可以在[View Technologies]()一章中找到 。 |
| UrlBasedViewResolver           | 简单实现`ViewResolver`接口，实现将逻辑视图名称直接解析为URL，而无需显式映射定义。如果你的逻辑名称以直接的方式与视图资源的名称匹配，则这是合适的，而不需要任何映射。 |
| InternalResourceViewResolver   | 方便使用的子类`UrlBasedViewResolver`支持`InternalResourceView`（实际上，Servlet和JSP）和其他子类，如`JstlView`和`TilesView`。你可以使用`setViewClass(..)`指定此解析程序生成的所有视图的视图类 。有关`UrlBasedViewResolver`详细信息，请参阅javadocs。 |
| FreeMarkerViewResolver         | 方便使用的子类`UrlBasedViewResolver`支持`FreeMarkerView`及其自定义子类。 |
| ContentNegotiatingViewResolver | `ViewResolver`接口的实现，用于解析基于请求的文件名或`Accept`请求头的视图。请参阅[内容协商]()。 |

#### 处理
你可以通过声明多个解析程序bean来链接视图解析程序，并在必要时通过设置order属性来指定排序。请记住，order属性越高，视图解析器在链中的位置越靠后。
`ViewResolver`约定它*可以*返回null以表示无法找到该视图。但是，对于JSP和`InternalResourceViewResolver`，确定JSP是否存在的唯一方法是通过`RequestDispatcher`执行调度。因此，必须始终将`InternalResourceViewResolver`配置为视图解析器的整体顺序中的最后一个。
配置视图解析就像在Spring配置中添加`ViewResolver`bean一样简单。[MVC Config]()为[View Resolvers]()提供了一个专用的配置API，并且还提供了无逻辑的[View控制器]()，这些控制器对于渲染没有控制器逻辑的HTML模板非常有用。

#### 重定向
视图名称中的特殊前缀`redirect:`允许你执行重定向。`UrlBasedViewResolver`（和子类）将此识别为需要重定向的指令。视图名称的其余部分是重定向URL。
控制器返回`RedirectView`可以达到相同的效果，但现在控制器本身可以简单地按照逻辑视图名称进行操作。逻辑视图名称（例如`redirect:/myapp/some/resource`）将相对于当前Servlet上下文重定向，而诸如`redirect:http//myhost.com/some/arbitrary/path`之类的名称将重定向到绝对URL。
请注意，如果使用`@ResponseStatus`注解控制器方法，则注解值优先于`RedirectView`设置的响应状态。

#### 转发
对于最终由`UrlBasedViewResolver`及其子类解析的视图名称，也可以使用特殊的`forward:`前缀。这将创建一个`InternalResourceView`，它执行`RequestDispatcher.forward()`。因此，此前缀对于`InternalResourceViewResolver`和`InternalResourceView`（对于JSP）没有用，但如果使用其他视图技术，但仍希望强制Servlet/JSP引擎处理资源的转发，则它可能会有所帮助。请注意，你也可以链接多个视图解析器。

#### 内容协调
`ContentNegotiatingViewResolver`本身不解析视图，而是委托给其他视图解析器，并选择类似于客户端请求的表示的视图。该表示可以从`Accept`请求头或查询参数确定，例如，"/path?format=pdf"。
`ContentNegotiatingViewResolver`通过将请求媒体类型与与其每个`ViewResolvers`关联的View支持的媒体类型（也称为`Content-Type`）进行比较，选择适当的`View`来处理请求。列表中具有兼容`Content-Type`的第一个`View`将表示返回给客户端。如果`ViewResolver`链无法提供兼容视图，则将查询通过`DefaultViews`属性指定的视图列表。后一个选项适用于单个视图，它可以呈现当前资源的适当表示，而不管逻辑视图名称如何。`Accept`请求头可以包括通配符，例如`text/*`，在这种情况下，Content-Type为`text/xml`的`View`就是兼容的匹配。
有关配置详细信息，请参阅[MVC配置]()下的[视图解析器]()章节。

### 1.2.9. 语言环境
Spring的体系结构大多数都支持国际化，就像Spring Web MVC框架一样。`DispatcherServlet`使你可以使用客户端的语言环境自动解析消息。通过`LocaleResolver`对象可以轻松完成。
当接收到请求时，`DispatcherServlet`会查找区域设置解析程序，如果找到，则会尝试使用其来设置区域。使用`RequestContext.getLocale（)`方法，你始终可以检索由区域设置解析程序解析的区域设置。
除了自动语言环境解析之外，你还可以将拦截器附加到处理程序映射（有关处理程序映射拦截器的更多信息，请参阅[拦截器]()）以在特定情况下更改语言环境，例如，根据请求中的参数。
区域设置解析器和拦截器在`org.springframework.web.servlet.i18n`包中定义，并以通常方式在应用程序上下文中进行配置。以下是Spring中包含的语言环境解析器的选择。

#### TimeZone
除了获取客户端的区域设置外，了解其时区通常也很有用。`LocaleContextResolver`接口提供`LocaleResolver`的扩展，允许解析器提供更丰富的`LocaleContext`，其中可能包含时区信息。
可用时，可以使用`RequestContext.getTimeZone()`方法获取用户的`TimeZone`。时区信息将由Spring的`ConversionService`注册的Date/Time `Converter`和`Formatter`对象自动使用。

#### Header resolver
该区域设置解析器检查客户端（例如，Web浏览器）发送的请求中的`accept-language`请求头。通常，此标头字段包含客户端操作系统的区域设置。*请注意，此解析程序不支持时区信息*。

#### Cookie resolver
此区域设置解析程序检查客户端上可能存在的`Cookie`，以查看是否指定了`Locale`或`TimeZone`。如果是，则使用指定的详细信息。使用此区域设置解析程序的属性，你可以指定cookie的名称以及最大年龄。下面是定义`CookieLocaleResolver`的示例。
```xml
<bean id="localeResolver" class="org.springframework.web.servlet.i18n.CookieLocaleResolver">

    <property name="cookieName" value="clientlanguage"/>

    <!-- in seconds. If set to -1, the cookie is not persisted (deleted when browser shuts down) -->
    <property name="cookieMaxAge" value="100000"/>

</bean>
```

*表4. CookieLocaleResolver 属性*

| 属性         | 默认值                     | 描述                                                          |
| ------------ | ------------------------- | ------------------------------------------------------------ |
| cookieName   | 类名 + 区域设置        | cookie的名称                                              |
| cookieMaxAge | Servlet 容器 默认 | Cookie在客户端上保持持久的最长时间。如果指定-1，则不会保留cookie;只有在客户端关闭浏览器之后才可用。 |
| cookiePath   | /                         | 限制cookie对网站某个部分的可见性。指定cookiePath时，cookie只对该路径及其下方的路径可见。 |

#### Session resolver
`SessionLocaleResolver`允许你从可能与用户请求关联的会话中检索`Locale`和`TimeZone`。与`CookieLocaleResolver`相比，此策略将本地选择的区域设置存储在`Servlet`容器的`HttpSession`中。因此，这些设置对于每个会话来说都是临时的，因此在每个会话终止时都会丢失。
请注意，与Spring Session项目等外部会话管理机制没有直接关系。此`SessionLocaleResolver`将简单地针对当前的`HttpServletRequest`评估和修改相应的`HttpSession`属性。

#### Locale interceptor
可以通过将`LocaleChangeInterceptor`添加到其中一个处理程序映射来启用语言环境的更改（请参阅[mvc-handlermapping]()）。它将检测请求中的参数并更改区域设置。在`LocaleResolver`上调用`setLocale()`，并且也存在于上下文中。以下示例显示对包含名为`siteLanguage`的参数的所有`*.view`资源的调用更改区域设置。因此，例如，对以下URL的请求`http://www.sf.net/home.view?siteLanguage=nl`会将站点语言更改为荷兰语。
```xml
<bean id="localeChangeInterceptor"
        class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor">
    <property name="paramName" value="siteLanguage"/>
</bean>

<bean id="localeResolver"
        class="org.springframework.web.servlet.i18n.CookieLocaleResolver"/>

<bean id="urlMapping"
        class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
    <property name="interceptors">
        <list>
            <ref bean="localeChangeInterceptor"/>
        </list>
    </property>
    <property name="mappings">
        <value>/**/*.view=someController</value>
    </property>
</bean>
```

### 1.2.10. 主题
你可以使用Spring Web MVC框架主题来设置应用程序的整体外观，从而增强用户体验。主题是静态资源的集合，通常是样式表和图像，它们会影响应用程序的视觉样式。

#### 定义主题
要在Web应用程序中使用主题，必须实现`org.springframework.ui.context.ThemeSource`接口。`WebApplicationContext`接口扩展了`ThemeSource`，但将其职责委托给专用实现。默认情况下，委托将是一个`org.springframework.ui.context.support.ResourceBundleThemeSource`实现，它从类路径的根目录加载属性文件。要使用自定义`ThemeSource`实现或配置`ResourceBundleThemeSource`的基本名称前缀，可以在应用程序上下文中使用保留名称`themeSource`注册bean。 Web应用程序上下文自动检测具有该名称的bean并使用它。
使用`ResourceBundleThemeSource`时，主题在简单属性文件中定义。属性文件列出构成主题的资源。例如：
```
styleSheet=/themes/cool/style.css
background=/themes/cool/img/coolBg.jpg
```
属性的键是从视图代码引用主题元素的名称。对于JSP，通常使用`spring:theme`自定义标签执行此操作，该标记与`spring:message`标签非常相似。以下JSP片段使用上一示例中定义的主题来自定义外观：
```jsp
<%@ taglib prefix="spring" uri="http://www.springframework.org/tags"%>
<html>
    <head>
        <link rel="stylesheet" href="<spring:theme code='styleSheet'/>" type="text/css"/>
    </head>
    <body style="background=<spring:theme code='background'/>">
        ...
    </body>
</html>
```
默认情况下，`ResourceBundleThemeSource`使用空的基本名称前缀。因此，属性文件从类路径的根加载。因此，你可以将`cool.properties`主题定义放在类路径根目录的目录中，例如，在`/WEB-INF/classes`中。 `ResourceBundleThemeSource`使用标准的Java资源包加载机制，允许主题的完全国际化。例如，我们可以有一个`/WEB-INF/classes/cool_nl.properties`，它引用一个带有荷兰文本的特殊背景图像。

#### 解析主题
定义主题后，如上一节所述，您可以决定使用哪个主题。 `DispatcherServlet`将查找名为`themeResolver`的bean，以找出要使用的`ThemeResolver`实现。主题解析器的工作方式与`LocaleResolver`的工作方式大致相同。它检测用于特定请求的主题，还可以更改请求的主题。 Spring提供以下主题解析器：
*表5.ThemeResolver实现*

| 类                  | 描述                                                  |
| ---------------------- | ------------------------------------------------------------ |
| FixedThemeResolver   | 选择使用`defaultThemeName`属性设置的固定主题。 |
| SessionThemeResolver | 主题在用户的HTTP会话中维护。它只需要为每个会话设置一次，但不会在会话之间保留。 |
| CookieThemeResolver  | 所选主题存储在客户端的cookie中。      |

Spring还提供了一个`ThemeChangeInterceptor`，它允许使用简单的请求参数对每个请求进行主题更改。

### 1.2.11. 分片解析器
`org.springframework.web.multipart`包中的`MultipartResolver`是一种用于解析包括文件上载在内的分片请求的策略。有一个基于[Commons FileUpload](https://jakarta.apache.org/commons/fileupload)的实现，以及一个基于Servlet 3.0的分片请求解析。
要启用分片处理，需要在`DispatcherServlet`Spring配置中声明一个名为"multipartResolver"的`MultipartResolver`bean。 `DispatcherServlet`检测到它并将其应用于传入请求。当收到内容类型为“multipart/form-data”的POST请求时，解析器解析内容并将当前的`HttpServletRequest`包装为`MultipartHttpServletRequest`，以便除了将它们作为请求参数公开之外，还提供对已解析部分的访问。

#### Apache FileUpload
要使用Apache Commons FileUpload，只需注册一个名为"multipartResolver"类型为`CommonsMultipartResolver`的bean。当然，你还需要在classpath中添加`commons-fileupload`依赖。

#### Servlet 3.0
通过Servlet容器配置启用Servlet 3.0分片解析：
- 在Java中，在Servlet注册上设置`MultipartConfigElemen`t
- 在`web.xml`中，将`"<multipart-config>"`部分添加到servlet声明中。
```java
public class AppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {

    // ...

    @Override
    protected void customizeRegistration(ServletRegistration.Dynamic registration) {

        // Optionally also set maxFileSize, maxRequestSize, fileSizeThreshold
        registration.setMultipartConfig(new MultipartConfigElement("/tmp"));
    }

}
```
一旦Servlet 3.0配置到位，只需添加名为"multipartResolver"，类型为`StandardServletMultipartResolver`的bean。