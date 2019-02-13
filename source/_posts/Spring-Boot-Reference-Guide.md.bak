---
title: Spring Boot 参考指南（2.0.3.RELEASE）
date: 2018-07-17 15:39:26
tags: 
 - Spring
 - Spring Boot
---

> Spring Boot官方文档翻译，水平有限，不喜勿喷

<!-- more -->

# Part I. Spring Boot 文档
本节是对Spring Boot参考文档的简要概述。
## 1. 关于文档
Spring Boot参考指南有以下几种文档形式（官方）
 - [HTML](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/html)
 - [PDF](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/pdf/spring-boot-reference.pdf)
 - [EPUB](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/epub/spring-boot-reference.epub)

最新的文档可以在[docs.spring.io/spring-boot/docs/current/reference](https://docs.spring.io/spring-boot/docs/current/reference)查看
## 2. 获取帮助
如果你在使用Spring Boot时遇到问题，可以参考以下内容：
 - 尝试使用[How-to documents](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/#howto)，里面提供最常见的问题的解决方案。
 - 学习Spring基础知识。Spring Boot建立在许多其他Spring项目之上。[spring.io](https://spring.io/)网站提供了大量参考文档以供查看。如果你刚开始使用Spring，可以先从[guides](https://spring.io/guides)看起。
 - 在[stackoverflow.com](https://stackoverflow.com/)用`spring-boot`标签提交问题，我们会关注相关问题。
 - 在Spring Boot [Issue](https://github.com/spring-projects/spring-boot/issues)提交问题
## 3. 第一步
如果你要入门Spring Boot或Spring，可以参考以下话题：
 - 从头开始：[概述](#getting-started-introducing-spring-boot)|[要求](#getting-started-system-requirements)|[安装](#getting-started-installing-spring-boot)
 - 教程：[第一部分](#getting-started-first-application) | [第二部分](#getting-started-first-application-code)
 - 运行示例：[第一部分](#getting-started-first-application-run) | [第二部分](#getting-started-first-application-executable-jar)
## 4. 使用Spring Boot
准备好开始使用Spring Boot了吗？[我们为你提供了以下内容](https://docs.spring.io/spring-boot/docs/2.0.3.RELEASE/reference/htmlsingle/#using-boot):
 - 构建系统：[Maven](#using-boot-maven) | [Gradle](#using-boot-gradle) | [Ant](#using-boot-ant) | [Starters](#using-boot-starter)
 - 最佳实践：[代码结构](#using-boot-structuring-your-code)| [@Configuration](#using-boot-configuration-classes) | [@EnableAutoConfiguration](#using-boot-auto-configuration) | [Beans和依赖注入](#using-boot-spring-beans-and-dependency-injection)
 - 运行代码：[IDE](#using-boot-running-from-an-ide) | [打包](#using-boot-running-as-a-packaged-application) | [Maven](#using-boot-running-with-the-maven-plugin) | [Gradle](#using-boot-running-with-the-gradle-plugin)
 - 打包应用：[生产jars](#using-boot-packaging-for-production)
 - Spring Boot CLI：[使用CLI](#cli)
## 5. 了解Spring Boot功能
[以下内容](#boot-features)让你了解更多Spring Boot核心功能：
 - 核心功能：[Spring应用](#boot-features-spring-application) | [外部配置](#boot-features-external-config) | [配置文件](#boot-features-profiles) | [日志记录](#boot-features-logging)
 - Web应用：[MVC](#boot-features-spring-mvc) | [嵌入式容器](#boot-features-embedded-container)
 - 数据操作：[SQL](#boot-features-sql) | [NO-SQL](#boot-features-nosql)
 - 消息：[概览](#boot-features-messaging) | [JMS](#boot-features-jms)
 - 测试：[概览](#boot-features-testing) | [Boot应用](#boot-features-testing-spring-boot-applications) | [工具](#boot-features-test-utilities)
 - 扩展：[自动配置（Auto-configuration）](#boot-features-developing-auto-configuration) | [@Conditions注解](#boot-features-condition-annotations)
## 6. 转向生产
当你准备将Spring Boot应用程序转向生产时，下面这些技巧可能对你有帮助：
 - 管理端点：[概览](#production-ready-endpoints) | [定制]()
 - 连接选项：[HTTP](#production-ready-monitoring) | [JMX](#production-ready-jmx)
 - 监控：[Metrics](#production-ready-metrics) | [Auditing](#production-ready-auditing) | [Tracing](#production-ready-http-tracing) | [Process](#production-ready-process-monitoring) 
## 7. 高级主题
最后，为高级用户提供了一些其他主题：
 - Spring Boot应用程序部署：[云端部署](#cloud-deployment) | [系统服务](#deployment-service)
 - 构建工具插件：[Maven](#build-tool-plugins-maven-plugin) | [Gradle](#build-tool-plugins-gradle-plugin)
 - 附录：[Application Properties](#common-application-properties) | [Auto-configuration classes](#auto-configuration-classes) | [Executable Jars](#executable-jar)

# Part II. 入门
如果你刚开始使用Spring Boot或通常所说的的“Spring”，请首先阅读本节。它回答了基本的“是什么？”，“如何做？”以及“为什么？”等问题。包括Spring Boot简介以及安装说明。然后将引导你构建第一个Spring Boot应用程序，并在其中穿插讨论一些核心原则。

<span id="getting-started-introducing-spring-boot"></span>
## 8. Spring Boot简介
Spring Boot使得构建一个基于Spring的可以运行的独立的生产级的应用程序变得更加轻松。我们用自己的观点来看待Spring平台和第三方库，这样你就可以轻松上手了。大多数Spring Boot应用程序只需要很少的Spring配置。
你可以使用Spring Boot创建使用java -jar或更传统的war部署启动的Java应用程序。我们还提供了一个运行“spring脚本”的命令行工具。
主要目标是：
 - 为所有Spring开发提供更快且可广泛接收的入门体验。
 - 开箱即用，且随着需求变更而可以迅速进行调整。
 - 提供一系列对大型项目通用的非功能性特性（例如嵌入式服务器，安全性，指标，运行状况检查和外部化配置）。
 - 绝对没有代码生成，也不需要XML配置。
 
<span id="getting-started-system-requirements"></span>
## 9. 系统要求
Spring Boot 2.0.3.RELEASE需要**Java8或9**以及**Spring Framework 5.0.7.RELEASE**或以上版本，构建工具需要**Maven 3.2+**或**Gradle 4**

### 9.1. Servlet 容器
Spring Boot支持以下内置servlet容器：

| 名称         | Servlet 版本 |
| ------------ | ------------ |
| Tomcat 8.5   | 3.1          |
| Jetty 9.4    | 3.1          |
| Undertow 1.4 | 3.1          |

你也可以将Spring Boot应用程序部署到任何兼容Servlet 3.1+的容器。

<span id="getting-started-installing-spring-boot"></span>
## 10. 安装 Spring Boot
Spring Boot可以与“经典”Java开发工具一起使用，也可以通过命令行工具安装。不管哪种方式，你都需要安装**Java SDK 1.8**及以上版本。在进行安装之前，你应该使用使用以下命令检查一下当前的Java环境是否安装正确：
```shelll
java -version
```
如果你刚开始接触Java或者你想先试验一下Spring Boot，你可以先使用[Spring Boot CLI](#getting-started-installing-the-cli)（命令行界面）。或者你可以接续阅读“传统”安装说明。

### 10.1. 针对Java开发者的安装说明
你可以像使用任何标准Java库一样使用Spring Boot。只要在类路径中包含相应的spring-boot-*.jar文件。Spring Boot不需要任何特殊工具集成，因此你可以使用任何IDE或文本编辑器。此外，Spring Boot应用程序没有什么特别之处，因此你可以像运行任何其他Java程序一样运行和调试Spring Boot应用程序。
虽然可以复制Spring Boot jar，但通常建议使用支持依赖关系管理的构建工具（例如Maven或Gradle）。

#### 10.1.1. Maven Installation
#### 10.1.2. Gradle Installation
<span id="getting-started-installing-the-cli"></span>
### 10.2. Installing the Spring Boot CLI
#### 10.2.1. Manual Installation
#### 10.2.2. Installation with SDKMAN!
#### 10.2.3. OSX Homebrew Installation
#### 10.2.4. MacPorts Installation
#### 10.2.5. Command-line Completion
#### 10.2.6. Windows Scoop Installation
#### 10.2.7. Quick-start Spring CLI Example
### 10.3. Upgrading from an Earlier Version of Spring Boot
<span id="getting-started-first-application"></span>
## 11. Developing Your First Spring Boot Application
### 11.1. Creating the POM
### 11.2. Adding Classpath Dependencies
<span id="getting-started-first-application-code"></span>
### 11.3. Writing the Code
#### 11.3.1. The @RestController and @RequestMapping Annotations
#### 11.3.2. The @EnableAutoConfiguration Annotation
#### 11.3.3. The “main” Method
<span id="getting-started-first-application-run"></span>
### 11.4. Running the Example
<span id="getting-started-first-application-executable-jar"></span>
### 11.5. Creating an Executable Jar
## 12. What to Read Next
# Part III. Using Spring Boot
## 13. Build Systems
### 13.1. Dependency Management
<span id="using-boot-maven"></span>
### 13.2. Maven
#### 13.2.1. Inheriting the Starter Parent
#### 13.2.2. Using Spring Boot without the Parent POM
#### 13.2.3. Using the Spring Boot Maven Plugin
<span id="using-boot-gradle"></span>
#### 13.3. Gradle
<span id="using-boot-ant"></span>
### 13.4. Ant
<span id="using-boot-starter"></span>
### 13.5. Starters
<span id="using-boot-structuring-your-code"></span>
## 14. Structuring Your Code
### 14.1. Using the “default” Package
### 14.2. Locating the Main Application Class
<span id="using-boot-configuration-classes"></span>
## 15. Configuration Classes
### 15.1. Importing Additional Configuration Classes
### 15.2. Importing XML Configuration
<span id="using-boot-auto-configuration"></span>
## 16. Auto-configuration
### 16.1. Gradually Replacing Auto-configuration
### 16.2. Disabling Specific Auto-configuration Classes
<span id="using-boot-spring-beans-and-dependency-injection"></span>
## 17. Spring Beans and Dependency Injection
## 18. Using the @SpringBootApplication Annotation
## 19. Running Your Application
<span id="using-boot-running-from-an-ide"></span>
### 19.1. Running from an IDE
<span id="using-boot-running-as-a-packaged-application"></span>
### 19.2. Running as a Packaged Application
<span id="using-boot-running-with-the-maven-plugin"></span>
### 19.3. Using the Maven Plugin
<span id="using-boot-running-with-the-gradle-plugin"></span>
### 19.4. Using the Gradle Plugin
### 19.5. Hot Swapping
## 20. Developer Tools
### 20.1. Property Defaults
### 20.2. Automatic Restart
#### 20.2.1. Logging changes in condition evaluation
#### 20.2.2. Excluding Resources
#### 20.2.3. Watching Additional Paths
#### 20.2.4. Disabling Restart
#### 20.2.5. Using a Trigger File
#### 20.2.6. Customizing the Restart Classloader
#### 20.2.7. Known Limitations
### 20.3. LiveReload
### 20.4. Global Settings
### 20.5. Remote Applications
#### 20.5.1. Running the Remote Client Application
#### 20.5.2. Remote Update
<span id="using-boot-packaging-for-production"></span>
## 21. Packaging Your Application for Production
## 22. What to Read Next
<span id="boot-features"></span>
# Part IV. Spring Boot features
<span id="boot-features-spring-application"></span>
## 23. SpringApplication
### 23.1. Startup Failure
### 23.2. Customizing the Banner
### 23.3. Customizing SpringApplication
### 23.4. Fluent Builder API
### 23.5. Application Events and Listeners
### 23.6. Web Environment
### 23.7. Accessing Application Arguments
### 23.8. Using the ApplicationRunner or CommandLineRunner
### 23.9. Application Exit
### 23.10. Admin Features
<span id="boot-features-external-config"></span>
## 24. Externalized Configuration
### 24.1. Configuring Random Values
### 24.2. Accessing Command Line Properties
### 24.3. Application Property Files
### 24.4. Profile-specific Properties
### 24.5. Placeholders in Properties
### 24.6. Using YAML Instead of Properties
#### 24.6.1. Loading YAML
#### 24.6.2. Exposing YAML as Properties in the Spring Environment
#### 24.6.3. Multi-profile YAML Documents
#### 24.6.4. YAML Shortcomings
### 24.7. Type-safe Configuration Properties
#### 24.7.1. Third-party Configuration
#### 24.7.2. Relaxed Binding
#### 24.7.3. Merging Complex Types
#### 24.7.4. Properties Conversion Converting durations
#### 24.7.5. @ConfigurationProperties Validation
#### 24.7.6. @ConfigurationProperties vs. @Value
<span id="boot-features-profiles"></span>
## 25. Profiles
### 25.1. Adding Active Profiles
### 25.2. Programmatically Setting Profiles
### 25.3. Profile-specific Configuration Files
<span id="boot-features-logging"></span>
## 26. Logging
### 26.1. Log Format
### 26.2. Console Output
#### 26.2.1. Color-coded Output
### 26.3. File Output
### 26.4. Log Levels
### 26.5. Custom Log Configuration
### 26.6. Logback Extensions
#### 26.6.1. Profile-specific Configuration
#### 26.6.2. Environment Properties
## 27. Developing Web Applications
<span id="boot-features-spring-mvc"></span>
### 27.1. The “Spring Web MVC Framework”
#### 27.1.1. Spring MVC Auto-configuration
#### 27.1.2. HttpMessageConverters
#### 27.1.3. Custom JSON Serializers and Deserializers
#### 27.1.4. MessageCodesResolver
#### 27.1.5. Static Content
#### 27.1.6. Welcome Page
#### 27.1.7. Custom Favicon
#### 27.1.8. Path Matching and Content Negotiation
#### 27.1.9. ConfigurableWebBindingInitializer
#### 27.1.10. Template Engines
#### 27.1.11. Error Handling Custom Error Pages Mapping Error Pages outside of Spring MVC
#### 27.1.12. Spring HATEOAS
#### 27.1.13. CORS Support
### 27.2. The “Spring WebFlux Framework”
#### 27.2.1. Spring WebFlux Auto-configuration
#### 27.2.2. HTTP Codecs with HttpMessageReaders and HttpMessageWriters
#### 27.2.3. Static Content
#### 27.2.4. Template Engines
#### 27.2.5. Error Handling Custom Error Pages
#### 27.2.6. Web Filters
### 27.3. JAX-RS and Jersey
<span id="boot-features-embedded-container"></span>
### 27.4. Embedded Servlet Container Support
#### 27.4.1. Servlets, Filters, and listeners Registering Servlets, Filters, and Listeners as Spring Beans
#### 27.4.2. Servlet Context Initialization Scanning for Servlets, Filters, and listeners
#### 27.4.3. The ServletWebServerApplicationContext
#### 27.4.4. Customizing Embedded Servlet Containers Programmatic Customization Customizing ConfigurableServletWebServerFactory Directly
#### 27.4.5. JSP Limitations
## 28. Security
### 28.1. MVC Security
### 28.2. WebFlux Security
### 28.3. OAuth2
#### 28.3.1. Client
#### 28.3.2. Server
### 28.4. Actuator Security
#### 28.4.1. Cross Site Request Forgery Protection
<span id="boot-features-sql"></span>
## 29. Working with SQL Databases
### 29.1. Configure a DataSource
#### 29.1.1. Embedded Database Support
#### 29.1.2. Connection to a Production Database
#### 29.1.3. Connection to a JNDI DataSource
### 29.2. Using JdbcTemplate
### 29.3. JPA and “Spring Data”
#### 29.3.1. Entity Classes
#### 29.3.2. Spring Data JPA Repositories
#### 29.3.3. Creating and Dropping JPA Databases
#### 29.3.4. Open EntityManager in View
### 29.4. Using H2’s Web Console
#### 29.4.1. Changing the H2 Console’s Path
### 29.5. Using jOOQ
#### 29.5.1. Code Generation
#### 29.5.2. Using DSLContext
#### 29.5.3. jOOQ SQL Dialect
#### 29.5.4. Customizing jOOQ
<span id="boot-features-nosql"></span>
## 30. Working with NoSQL Technologies
### 30.1. Redis
#### 30.1.1. Connecting to Redis
### 30.2. MongoDB
#### 30.2.1. Connecting to a MongoDB Database
#### 30.2.2. MongoTemplate
#### 30.2.3. Spring Data MongoDB Repositories
#### 30.2.4. Embedded Mongo
### 30.3. Neo4j
#### 30.3.1. Connecting to a Neo4j Database
#### 30.3.2. Using the Embedded Mode
#### 30.3.3. Neo4jSession
#### 30.3.4. Spring Data Neo4j Repositories
#### 30.3.5. Repository Example
### 30.4. Gemfire
### 30.5. Solr
#### 30.5.1. Connecting to Solr
#### 30.5.2. Spring Data Solr Repositories
### 30.6. Elasticsearch
#### 30.6.1. Connecting to Elasticsearch by Using Jest
#### 30.6.2. Connecting to Elasticsearch by Using Spring Data
#### 30.6.3. Spring Data Elasticsearch Repositories
### 30.7. Cassandra
#### 30.7.1. Connecting to Cassandra
#### 30.7.2. Spring Data Cassandra Repositories
### 30.8. Couchbase
#### 30.8.1. Connecting to Couchbase
#### 30.8.2. Spring Data Couchbase Repositories
### 30.9. LDAP
#### 30.9.1. Connecting to an LDAP Server
#### 30.9.2. Spring Data LDAP Repositories
#### 30.9.3. Embedded In-memory LDAP Server
### 30.10. InfluxDB
#### 30.10.1. Connecting to InfluxDB
## 31. Caching
### 31.1. Supported Cache Providers
#### 31.1.1. Generic
#### 31.1.2. JCache (JSR-107)
#### 31.1.3. EhCache 2.x
#### 31.1.4. Hazelcast
#### 31.1.5. Infinispan
#### 31.1.6. Couchbase
#### 31.1.7. Redis
#### 31.1.8. Caffeine
#### 31.1.9. Simple
#### 31.1.10. None
<span id="boot-features-messaging"></span>
## 32. Messaging
<span id="boot-features-jms"></span>
### 32.1. JMS
#### 32.1.1. ActiveMQ Support
#### 32.1.2. Artemis Support
#### 32.1.3. Using a JNDI ConnectionFactory
#### 32.1.4. Sending a Message
#### 32.1.5. Receiving a Message
### 32.2. AMQP
#### 32.2.1. RabbitMQ support
#### 32.2.2. Sending a Message
#### 32.2.3. Receiving a Message
### 32.3. Apache Kafka Support
#### 32.3.1. Sending a Message
#### 32.3.2. Receiving a Message
#### 32.3.3. Additional Kafka Properties
## 33. Calling REST Services with RestTemplate
### 33.1. RestTemplate Customization
## 34. Calling REST Services with WebClient
### 34.1. WebClient Customization
## 35. Validation
## 36. Sending Email
## 37. Distributed Transactions with JTA
### 37.1. Using an Atomikos Transaction Manager
### 37.2. Using a Bitronix Transaction Manager
### 37.3. Using a Narayana Transaction Manager
### 37.4. Using a Java EE Managed Transaction Manager
### 37.5. Mixing XA and Non-XA JMS Connections
### 37.6. Supporting an Alternative Embedded Transaction Manager
## 38. Hazelcast
## 39. Quartz Scheduler
## 40. Spring Integration
## 41. Spring Session
## 42. Monitoring and Management over JMX
<span id="boot-features-testing"></span>
## 43. Testing
### 43.1. Test Scope Dependencies
### 43.2. Testing Spring Applications
<span id="boot-features-testing-spring-boot-applications"></span>
### 43.3. Testing Spring Boot Applications
#### 43.3.1. Detecting Web Application Type
#### 43.3.2. Detecting Test Configuration
#### 43.3.3. Excluding Test Configuration
#### 43.3.4. Testing with a running server
#### 43.3.5. Using JMX
#### 43.3.6. Mocking and Spying Beans
#### 43.3.7. Auto-configured Tests
#### 43.3.8. Auto-configured JSON Tests
#### 43.3.9. Auto-configured Spring MVC Tests
#### 43.3.10. Auto-configured Spring WebFlux Tests
#### 43.3.11. Auto-configured Data JPA Tests
#### 43.3.12. Auto-configured JDBC Tests
#### 43.3.13. Auto-configured jOOQ Tests
#### 43.3.14. Auto-configured Data MongoDB Tests
#### 43.3.15. Auto-configured Data Neo4j Tests
#### 43.3.16. Auto-configured Data Redis Tests
#### 43.3.17. Auto-configured Data LDAP Tests
#### 43.3.18. Auto-configured REST Clients
#### 43.3.19. Auto-configured Spring REST Docs Tests Auto-configured Spring REST Docs Tests with Mock MVC Auto-configured Spring REST Docs Tests with REST Assured
#### 43.3.20. User Configuration and Slicing
#### 43.3.21. Using Spock to Test Spring Boot Applications
<span id="boot-features-test-utilities"></span>
### 43.4. Test Utilities
#### 43.4.1. ConfigFileApplicationContextInitializer
#### 43.4.2. TestPropertyValues
#### 43.4.3. OutputCapture
#### 43.4.4. TestRestTemplate
## 44. WebSockets
## 45. Web Services
<span id="boot-features-developing-auto-configuration"></span>
## 46. Creating Your Own Auto-configuration
### 46.1. Understanding Auto-configured Beans
### 46.2. Locating Auto-configuration Candidates
<span id="boot-features-condition-annotations"></span>
### 46.3. Condition Annotations
#### 46.3.1. Class Conditions
#### 46.3.2. Bean Conditions
#### 46.3.3. Property Conditions
#### 46.3.4. Resource Conditions
#### 46.3.5. Web Application Conditions
#### 46.3.6. SpEL Expression Conditions
### 46.4. Testing your Auto-configuration
#### 46.4.1. Simulating a Web Context
#### 46.4.2. Overriding the Classpath
### 46.5. Creating Your Own Starter
#### 46.5.1. Naming
#### 46.5.2. autoconfigure Module
#### 46.5.3. Starter Module
## 47. Kotlin support
### 47.1. Requirements
### 47.2. Null-safety
### 47.3. Kotlin API
#### 47.3.1. runApplication
#### 47.3.2. Extensions
### 47.4. Dependency management
### 47.5. @ConfigurationProperties
### 47.6. Testing
### 47.7. Resources
#### 47.7.1. Further reading
#### 47.7.2. Examples
## 48. What to Read Next
# Part V. Spring Boot Actuator: Production-ready features
## 49. Enabling Production-ready Features
<span id="production-ready-endpoints"></span>
## 50. Endpoints
### 50.1. Enabling Endpoints
### 50.2. Exposing Endpoints
### 50.3. Securing HTTP Endpoints
### 50.4. Configuring Endpoints
### 50.5. Hypermedia for Actuator Web Endpoints
### 50.6. Actuator Web Endpoint Paths
### 50.7. CORS Support
### 50.8. Implementing Custom Endpoints
#### 50.8.1. Receiving Input Input type conversion
#### 50.8.2. Custom Web Endpoints Web Endpoint Request Predicates Path HTTP method Consumes Produces Web Endpoint Response Status Web Endpoint Range Requests Web Endpoint Security
#### 50.8.3. Servlet endpoints
#### 50.8.4. Controller endpoints
### 50.9. Health Information
#### 50.9.1. Auto-configured HealthIndicators
#### 50.9.2. Writing Custom HealthIndicators
#### 50.9.3. Reactive Health Indicators
#### 50.9.4. Auto-configured ReactiveHealthIndicators
### 50.10. Application Information
#### 50.10.1. Auto-configured InfoContributors
#### 50.10.2. Custom Application Information
#### 50.10.3. Git Commit Information
#### 50.10.4. Build Information
#### 50.10.5. Writing Custom InfoContributors
<span id="production-ready-monitoring"></span>
## 51. Monitoring and Management over HTTP
### 51.1. Customizing the Management Endpoint Paths
### 51.2. Customizing the Management Server Port
### 51.3. Configuring Management-specific SSL
### 51.4. Customizing the Management Server Address
### 51.5. Disabling HTTP Endpoints
<span id="production-ready-jmx"></span>
## 52. Monitoring and Management over JMX
### 52.1. Customizing MBean Names
### 52.2. Disabling JMX Endpoints
### 52.3. Using Jolokia for JMX over HTTP
#### 52.3.1. Customizing Jolokia
#### 52.3.2. Disabling Jolokia
## 53. Loggers
### 53.1. Configure a Logger
<span id="production-ready-metrics"></span>
## 54. Metrics
### 54.1. Getting started
### 54.2. Supported monitoring systems
#### 54.2.1. Atlas
#### 54.2.2. Datadog
#### 54.2.3. Ganglia
#### 54.2.4. Graphite
#### 54.2.5. Influx
#### 54.2.6. JMX
#### 54.2.7. New Relic
#### 54.2.8. Prometheus
#### 54.2.9. SignalFx
#### 54.2.10. Simple
#### 54.2.11. StatsD
#### 54.2.12. Wavefront
### 54.3. Supported Metrics
#### 54.3.1. Spring MVC Metrics
#### 54.3.2. Spring WebFlux Metrics
#### 54.3.3. RestTemplate Metrics
#### 54.3.4. Cache Metrics
#### 54.3.5. DataSource Metrics
#### 54.3.6. RabbitMQ Metrics
### 54.4. Registering custom metrics
### 54.5. Customizing individual metrics
#### 54.5.1. Per-meter properties
### 54.6. Metrics endpoint
<span id="production-ready-auditing"></span>
## 55. Auditing
<span id="production-ready-http-tracing"></span>
## 56. HTTP Tracing
### 56.1. Custom HTTP tracing
<span id="production-ready-process-monitoring"></span>
## 57. Process Monitoring
## 57.1. Extending Configuration
## 57.2. Programmatically
## 58. Cloud Foundry Support
### 58.1. Disabling Extended Cloud Foundry Actuator Support
### 58.2. Cloud Foundry Self-signed Certificates
### 58.3. Custom context path
## 59. What to Read Next
# Part VI. Deploying Spring Boot Applications
<span id="cloud-deployment"></span>
## 60. Deploying to the Cloud
### 60.1. Cloud Foundry
#### 60.1.1. Binding to Services
### 60.2. Heroku
### 60.3. OpenShift
### 60.4. Amazon Web Services (AWS)
#### 60.4.1. AWS Elastic Beanstalk Using the Tomcat Platform Using the Java SE Platform
#### 60.4.2. Summary
### 60.5. Boxfuse and Amazon Web Services
### 60.6. Google Cloud
## 61. Installing Spring Boot Applications
### 61.1. Supported Operating Systems
<span id="deployment-service"></span>
### 61.2. Unix/Linux Services
#### 61.2.1. Installation as an init.d Service (System V) Securing an init.d Service
#### 61.2.2. Installation as a systemd Service
#### 61.2.3. Customizing the Startup Script Customizing the Start Script when It Is Written Customizing a Script When It Runs
### 61.3. Microsoft Windows Services
## 62. What to Read Next
<span id="cli"></span>
# Part VII. Spring Boot CLI
## 63. Installing the CLI
## 64. Using the CLI
### 64.1. Running Applications with the CLI
#### 64.1.1. Deduced “grab” Dependencies
#### 64.1.2. Deduced “grab” Coordinates
#### 64.1.3. Default Import Statements
#### 64.1.4. Automatic Main Method
#### 64.1.5. Custom Dependency Management
### 64.2. Applications with Multiple Source Files
### 64.3. Packaging Your Application
### 64.4. Initialize a New Project
### 64.5. Using the Embedded Shell
### 64.6. Adding Extensions to the CLI
## 65. Developing Applications with the Groovy Beans DSL
## 66. Configuring the CLI with settings.xml
## 67. What to Read Next
# Part VIII. Build tool plugins
<span id="build-tool-plugins-maven-plugin"></span>
## 68. Spring Boot Maven Plugin
### 68.1. Including the Plugin
### 68.2. Packaging Executable Jar and War Files
<span id="build-tool-plugins-gradle-plugin"></span>
## 69. Spring Boot Gradle Plugin
## 70. Spring Boot AntLib Module
### 70.1. Spring Boot Ant Tasks
#### 70.1.1. spring-boot:exejar
#### 70.1.2. Examples
### 70.2. spring-boot:findmainclass
#### 70.2.1. Examples
## 71. Supporting Other Build Systems
### 71.1. Repackaging Archives
### 71.2. Nested Libraries
### 71.3. Finding a Main Class
### 71.4. Example Repackage Implementation
## 72. What to Read Next
# Part IX. ‘How-to’ guides
## 73. Spring Boot Application
### 73.1. Create Your Own FailureAnalyzer
### 73.2. Troubleshoot Auto-configuration
### 73.3. Customize the Environment or ApplicationContext Before It Starts
### 73.4. Build an ApplicationContext Hierarchy (Adding a Parent or Root Context)
### 73.5. Create a Non-web Application
## 74. Properties and Configuration
### 74.1. Automatically Expand Properties at Build Time
#### 74.1.1. Automatic Property Expansion Using Maven
#### 74.1.2. Automatic Property Expansion Using Gradle
### 74.2. Externalize the Configuration of SpringApplication
### 74.3. Change the Location of External Properties of an Application
### 74.4. Use ‘Short’ Command Line Arguments
### 74.5. Use YAML for External Properties
### 74.6. Set the Active Spring Profiles
### 74.7. Change Configuration Depending on the Environment
### 74.8. Discover Built-in Options for External Properties
## 75. Embedded Web Servers
### 75.1. Use Another Web Server
### 75.2. Disabling the Web Server
### 75.3. Change the HTTP Port
### 75.4. Use a Random Unassigned HTTP Port
### 75.5. Discover the HTTP Port at Runtime
### 75.6. Enable HTTP Response Compression
### 75.7. Configure SSL
### 75.8. Configure HTTP/2
#### 75.8.1. HTTP/2 with Undertow
#### 75.8.2. HTTP/2 with Jetty
#### 75.8.3. HTTP/2 with Tomcat
### 75.9. Configure the Web Server
### 75.10. Add a Servlet, Filter, or Listener to a Application
#### 75.10.1. Add a Servlet, Filter, or Listener by Using a Spring Bean Disable Registration of a Servlet or Filter
#### 75.10.2. Add Servlets, Filters, and Listeners by Using Classpath Scanning
### 75.11. Configure Access Logging
### 75.12. Running Behind a Front-end Proxy Server
#### 75.12.1. Customize Tomcat’s Proxy Configuration
### 75.13. Enable Multiple Connectors with Tomcat
### 75.14. Use Tomcat’s LegacyCookieProcessor
### 75.15. Enable Multiple Listeners with Undertow
### 75.16. Create WebSocket Endpoints Using @ServerEndpoint
## 76. Spring MVC
### 76.1. Write a JSON REST Service
### 76.2. Write an XML REST Service
### 76.3. Customize the Jackson ObjectMapper
### 76.4. Customize the @ResponseBody Rendering
### 76.5. Handling Multipart File Uploads
### 76.6. Switch Off the Spring MVC DispatcherServlet
### 76.7. Switch off the Default MVC Configuration
### 76.8. Customize ViewResolvers
## 77. Jersey
### 77.1. Secure Jersey endpoints with Spring Security
## 78. HTTP Clients
### 78.1. Configure RestTemplate to Use a Proxy
## 79. Logging
### 79.1. Configure Logback for Logging
#### 79.1.1. Configure Logback for File-only Output
### 79.2. Configure Log4j for Logging
#### 79.2.1. Use YAML or JSON to Configure Log4j 2
## 80. Data Access
### 80.1. Configure a Custom DataSource
### 80.2. Configure Two DataSources
### 80.3. Use Spring Data Repositories
### 80.4. Separate @Entity Definitions from Spring Configuration
### 80.5. Configure JPA Properties
### 80.6. Configure Hibernate Naming Strategy
### 80.7. Use a Custom EntityManagerFactory
### 80.8. Use Two EntityManagers
### 80.9. Use a Traditional persistence.xml File
### 80.10. Use Spring Data JPA and Mongo Repositories
### 80.11. Expose Spring Data Repositories as REST Endpoint
### 80.12. Configure a Component that is Used by JPA
### 80.13. Configure jOOQ with Two DataSources
## 81. Database Initialization
### 81.1. Initialize a Database Using JPA
### 81.2. Initialize a Database Using Hibernate
### 81.3. Initialize a Database
### 81.4. Initialize a Spring Batch Database
### 81.5. Use a Higher-level Database Migration Tool
#### 81.5.1. Execute Flyway Database Migrations on Startup
#### 81.5.2. Execute Liquibase Database Migrations on Startup
## 82. Messaging
### 82.1. Disable Transacted JMS Session
## 83. Batch Applications
### 83.1. Execute Spring Batch Jobs on Startup
## 84. Actuator
### 84.1. Change the HTTP Port or Address of the Actuator Endpoints
### 84.2. Customize the ‘whitelabel’ Error Page
### 84.3. Sanitize sensible values
## 85. Security
### 85.1. Switch off the Spring Boot Security Configuration
### 85.2. Change the UserDetailsService and Add User Accounts
### 85.3. Enable HTTPS When Running behind a Proxy Server
## 86. Hot Swapping
### 86.1. Reload Static Content
### 86.2. Reload Templates without Restarting the Container
#### 86.2.1. Thymeleaf Templates
#### 86.2.2. FreeMarker Templates
#### 86.2.3. Groovy Templates
### 86.3. Fast Application Restarts
### 86.4. Reload Java Classes without Restarting the Container
## 87. Build
### 87.1. Generate Build Information
### 87.2. Generate Git Information
### 87.3. Customize Dependency Versions
### 87.4. Create an Executable JAR with Maven
### 87.5. Use a Spring Boot Application as a Dependency
### 87.6. Extract Specific Libraries When an Executable Jar Runs
### 87.7. Create a Non-executable JAR with Exclusions
### 87.8. Remote Debug a Spring Boot Application Started with Maven
### 87.9. Build an Executable Archive from Ant without Using spring-boot-antlib
## 88. Traditional Deployment
### 88.1. Create a Deployable War File
### 88.2. Convert an Existing Application to Spring Boot
### 88.3. Deploying a WAR to WebLogic
### 88.4. Use Jedis Instead of Lettuce
# Part X. Appendices
<span id="common-application-properties"></span>
## A. Common application properties
## B. Configuration Metadata
### B.1. Metadata Format
#### B.1.1. Group Attributes
#### B.1.2. Property Attributes
#### B.1.3. Hint Attributes
#### B.1.4. Repeated Metadata Items
### B.2. Providing Manual Hints
#### B.2.1. Value Hint
#### B.2.2. Value Providers Any Class Reference Handle As Logger Name Spring Bean Reference Spring Profile Name
### B.3. Generating Your Own Metadata by Using the Annotation Processor
#### B.3.1. Nested Properties
#### B.3.2. Adding Additional Metadata
<span id="auto-configuration-classes"></span>
## C. Auto-configuration classes
### C.1. From the “spring-boot-autoconfigure” module
### C.2. From the “spring-boot-actuator-autoconfigure” module
## D. Test auto-configuration annotations
<span id="executable-jar"></span>
## E. The Executable Jar Format
### E.1. Nested JARs
#### E.1.1. The Executable Jar File Structure
#### E.1.2. The Executable War File Structure
### E.2. Spring Boot’s “JarFile” Class
#### E.2.1. Compatibility with the Standard Java “JarFile”
### E.3. Launching Executable Jars
#### E.3.1. Launcher Manifest
#### E.3.2. Exploded Archives
### E.4. PropertiesLauncher Features
### E.5. Executable Jar Restrictions
### E.6. Alternative Single Jar Solutions
## F. Dependency versions