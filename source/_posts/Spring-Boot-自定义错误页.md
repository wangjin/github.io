---
title: Spring Boot 自定义错误页
date: 2017-04-10 09:28:25
tags: 
- Java 
- Spring
- Spring Boot
---
## 在Spring Boot自定义错误页面
### 添加Java风格配置
```Java
@Configuration
public class ErrorPageConfig {

    @Bean
    public EmbeddedServletContainerCustomizer containerCustomizer() {
        return container -> {
            container.addErrorPages(new ErrorPage(HttpStatus.BAD_REQUEST, "/400"));
            container.addErrorPages(new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/500"));
            container.addErrorPages(new ErrorPage(HttpStatus.NOT_FOUND, "/404"));
        };
    }
}
```
### Controller中添加RequestMapping绑定自定义页面
```Java
@RequestMapping(value = "/404")
public String pageNotFound() {

    return "base/404";
}

@RequestMapping(value = "/500")
public String serverError() {

    return "base/500";
}
```