---
title: Java枚举
date: 2019-03-15 16:06:12
tags:
  - Java
  - 枚举
  - Enum
---

在 Java 1.5 之前，传统定义枚举的方式是借用定义常量的方式，例如定义颜色：

```java
public static final int RED = 1;
public static final int YELLOW = 2;
public static final int BLUE = 3;
```

在 Java 1.5 中增加了新的引用类型`枚举`。
Java 的枚举类型的父类均为 `java.lang.Enum`，枚举本质上是 int 值。
使用枚举方式定义颜色：

```java
public enum Color {
    RED,YELLOW,BLUE;
}
```
