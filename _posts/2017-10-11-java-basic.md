---
layout: post
title:  "Java基础笔记"
date:   2017-10-11 23:00:00
categories: Java
permalink: /archivers/java-basic
---

# 面向对象

优点：

* 对现实世界进行建模，易于理解
* 更好的代码重用，具备继承性
* 模块化，维护成本低

面向对象的四大主要特征为：

* 封装（Encapsulation）
* 继承（Inheritance）
* 多态（Polymorphism）
* 抽象（Abstraction）

## 封装 
Java提供的可见性修饰符包括public/ default/ protected/ private，用来在不同层面上隐藏变量、方法和类，但最终目的在于封装那些不需要进行修改的东西

优点：

* 通过隐藏属性来保护对象的内部状态
* 促进代码模块化，增强可用性
* 可以独立地进行代码修改

## 继承
继承是指派生类中包含了基类中的所有的行为（即方法）和状态（即变量），并能通过该派生类进行访问。

优点：促进重用性

## 多态
多态是指使用相同的接口来引用不同的实现方式

* 可用于创建可重用代码：一旦类被创建，实施和测试，就可以直接进行使用而不考虑具体的代码细节；
* 提供更为泛化和松耦合的代码；

> 方法重载与覆盖的区别：方法重载实际上就是指两个方法使用相同的名称，但参数不同；覆盖涉及到两个不同的方法，一个父类的方法，另一个则是子类中的方法，两个方法具有相同的函数名和方法签名。

优点：

* 通过隐藏属性来保护对象的内部状态
* 促进代码模块化，增强可用性
* 可以独立地进行代码修改

## 抽象 
抽象意味着只需要开发类的接口和功能声明，而不需要实现具体的实施细节。抽象类呈现的是接口，而不需要具体实现，将对象的实施与行为或实现分离开来，进而通过隐藏无关的细节来减少复杂度。

优点：

* 通过使用抽象，我们可以将不同类别的东西分离开来；
* 经常需要修改的属性和方法可以被分离出来形成一个单独的类别，而那些主要留下的部分就不需要进行修改了，进而增强面向对象的分析与设计（OOAD）原则，即“代码应该易于扩展，而不应该经常修改”；

## 接口和抽象类之间的区别

* 接口只是一个提供了方法声明的空壳；
* 抽象类定义了一些通用方法，其子类可以定义新的具体或特殊的方法；
* 继承一个抽象类，子类需要实现其中的抽象方法，然而接口可以扩展另一个接口而无需实现其中的方法；
* 子类只能继承单个抽象类，而一个接口或类可以实现多个接口；
* 继承抽象类的子类可以以相同或更低的可见性实现父类中的抽象方法，而实现接口的类只能以与原抽象方法相同的可见性实现接口中的方法；
* 接口没有构造函数，抽象类有；
* 接口中的变量都是final型的，而抽象类中可以包含非final型变量；
* 接口中的成员默认是public类型的，但抽象类中的成员的访问类型可以是public，protected和默认类型。

## 可见性修饰符 
Java提供的可见性修饰符包括public/ default/ protected/ private，用来在不同层面上隐藏变量、方法和类，但最终目的在于封装那些不需要进行修改的东西



# String

## 字符串不变性

```java
String s = "abcd";
s = s.concat("ef");
```

![字符串常量池](http://anytimekaka.github.io/images/string01.jpeg)

# 集合类

Collections和Collection的区别:

![Collections包含有各种有关集合操作的静态多态方法](http://anytimekaka.github.io/images/collections.jpeg)

# 异常

## Java异常类的层次结构

图中红色部分为受检查异常。它们必须被捕获，或者在函数中声明为抛出该异常。

![](http://anytimekaka.github.io/images/exception.jpeg)

# equals()方法、hashCode()方法的区别

HashCode被设计用来提高性能。equals()方法与hashCode()方法的区别在于：

* 如果两个对象相等(equal)，那么他们一定有相同的哈希值。
* 如果两个对象的哈希值相同，但他们未必相等(equal)。

![](http://anytimekaka.github.io/images/equals_hashcode.png)