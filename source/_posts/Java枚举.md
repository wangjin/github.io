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

<!-- more -->

`Enmu`包含两个属性`name`和`ordinal`以及两个对应的方法`name()`和`ordinal()`。
两个方法都被声明成 final，不可以被覆盖：

```java
public final String name() {
    return name;
}

public final int ordinal() {
    return ordinal;
}
```

`ordinal()`方法返回`ordinal`属性值，该属性为枚举值的索引，类型为`int`，默认从`0`开始。
因此，按照定义枚举的顺序

> RED.ordinal() = 0
> YELLOW.ordinal() = 1
> BLUE.ordinal() = 2

### 枚举提供编译时的类型安全检查

声明方法的参数为枚举型时，则可以保证传递到该方法的参数必须为非 null 值且必须为枚举型的任一枚举值。

### 枚举禁止 clone

枚举重写了 clone()方法，调用 clone 会抛出`CloneNotSupportedException`异常：

```java
protected final Object clone() throws CloneNotSupportedException {
    throw new CloneNotSupportedException();
}
```

### 枚举不会被 finalize

```java
/**
 * enum classes cannot have finalize methods.
 */
protected final void finalize() { }

```

### 枚举禁用了反序列化

```java
/**
 * prevent default deserialization
 */
private void readObject(ObjectInputStream in) throws IOException,
    ClassNotFoundException {
    throw new InvalidObjectException("can't deserialize enum");
}

private void readObjectNoData() throws ObjectStreamException {
   throw new InvalidObjectException("can't deserialize enum");
}
```
