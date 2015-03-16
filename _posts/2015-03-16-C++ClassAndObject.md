---
layout:     post
title:      "C++对象初始化与函数调用"
subtitle:   "构造函数与析构函数在何时调用，函数定义中的形参为何选择引用类型，const关键字的用法等"
date:       2015-03-16 10:51:00
author:     "anytimekaka"
header-img: "img/post-bg-03.jpg"
---

#前言

关于面向对象OOP，本文不再做说明。本文的重点是说明：在使用C++这门语言进行程序开发中，对类和对象的使用需要注意的地方。

插一句：结构和类的不同，在C++中表现在于：类的默认成员为private，结构是public——《C++ Primer》

#构造函数与析构函数

构造函数与析构函数的调用时间，其实相对容易理解。构造函数在初始化对象的时候会调用到，析构函数在对象被销毁时调用。

>关于析构函数被调用的时间，以我们常用的“自动存储对象”，在执行到其作用域之后，析构函数就会被调用。如果是使用new初始化的对象，在delete删除的时候，调用析构函数。

举个例子用于说明临时变量：

类Rectangle的定义：

	class Rectangle{
	private:
		int height;
		int width;

	public:
		Rectangle(int height1, int width1){		//构造函数
			height = height1;
			width = width1;
			std::cout << "init Rectangle instance" << std::endl;
			std::cout << "height:" << height << "  width:" << width << std::endl;
		}

		~Rectangle(){		//析构函数
			std::cout << "Rectangle(" << height << "," << width << ")distroyed" << std::endl;
		}

main函数：

	int main(){
		{
			Rectangle rect1 = Rectangle(1, 1);
			Rectangle rect2 = Rectangle(2, 2);
			rect1 = Rectangle(3, 3); //创建了临时变量，并立刻销毁
			Rectangle rect4 = Rectangle(4, 4);
			Rectangle rect5;  //没有默认构造函数，不能编译
		}
	}

由于自定义了带参数的构造函数，没有了不带参数的默认构造函数，所以rect5不能编译通过。删除这一行之后，运行结果如下图：

<img src="anytimekaka.github.io/img/postimg/201503161446.png"/>

