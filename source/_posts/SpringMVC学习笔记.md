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

## 1.2 DispatcherServlet
和其他很多框架类似，Spring MVC围绕前端控制器模式设计，其中`DispatcherServlet`作为中央`Servlet`，为请求处理提供共享算法，而实际工作由可配置的委托组件执行。该模型非常灵活，支持多种工作流程。

