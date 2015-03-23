---
layout:     post
title:      "C++关键知识点梳理"
subtitle:   "构造函数与析构函数在何时调用，函数定义中的形参为何选择引用类型，const关键字的用法等"
date:       2015-03-16 10:51:00
author:     "anytimekaka"
header-img: "img/post-bg-03.jpg"
---

#前言

关于面向对象OOP，本文不再做说明。本文的重点是说明：在使用C++这门语言进行程序开发中，对类和对象的使用需要注意的地方。

插一句：结构和类的不同，在C++中表现在于：类的默认成员为private，结构是public——《C++ Primer》

#目录

* 构造函数与析构函数
* const函数
* 对象数组
* 运算符重载与友元函数
* 类的自动转换和强制类型转换

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

<img src="http://anytimekaka.github.io/img/postimg/201503161446.png"/>

结果分析：在将Rectangle(3,3)赋值给rect1之后，会马上删除掉临时变量，我们可以看到析构函数被调用了一次。后面析构函数的调用顺序说明了，局部变量是被放在栈中，先实例化的后销毁。

#const函数

在说const函数是什么之前，我们设想以下一种情况：

	//我们给Rectangle类定义一个成员函数，计算矩形的面积
	int Rectangle::area(){
		return width*height;
	}

	//main函数
	const Rectangle rect(1,1);
	rect.area();	//编译不通过

当我在编译器中写下这样的代码之后，有个这样的错误提示：

<img src="http://anytimekaka.github.io/img/postimg/201503161515.png"/>

其实错误很容易理解：rect对象是const类型，不能确保在调用area()时不改变rect。解决方法就是如下声明：

	int area() const;

	int Rectangle::area() const{
		return width*height;
	}

#对象数组

我在阅读《C++ Primer》这本书时，看到这么一段：

>初始化对象数组的方案是，首先使用默认构造函数创建数组元素，然后花括号中的构造函数会创建临时对象，然后将临时对象的内容复制到相应的元素中。因此，要创建类对象数组，这个类必须有默认构造函数。P369

但是在实际应用中，情况并不是这样。

	Rectangle rectArr[2] = {
		Rectangle(2, 2),
		Rectangle(3, 3)
	};		//没有问题

	Rectangle rectArr[3] = {
		Rectangle(2, 2),
		Rectangle(3, 3)
	};		//编译错误

重新添加了默认构造函数之后，发现[2]数组声明过程中没有调用默认构造函数，[3]数组声明调用了一次。

#运算符重载与友元函数

这里先举两个运算符重载的例子，然后说明为什么需要友元函数。

第一个：

	//运算符 + 
	Rectangle Rectangle::operator+(const Rectangle & rect) const{
		Rectangle result;
		result.height = this->height + rect.height;
		result.width = this->width + rect.width;
		return result;
	}

	//使用
	Rectangle rect1(1, 1);
	Rectangle rect2(1, 1);
	std::cout << (rect1 + rect2).area()<<std::endl;
	//本质rect1.operator+(rect2).area()

第二个：

	
	Rectangle Rectangle::operator*(int arg) const{
		Rectangle result;
		result.height = this->height * arg;
		result.width = this->width * arg;
		return result;
	}

	//使用情景一
	Rectangle rect1(2, 2);
	std::cout << (rect1 * 2).area()<<std::endl;
	//本质rect1.operator+(2).area()

	//使用情景二
	Rectangle rect1(2, 2);
	std::cout << (2 * rect1).area()<<std::endl;
	//由于没有对int进行 * 重载（也不能对基本类型进行重载），不能这么使用

那么如何实现情景二中的应用呢？答案就是使用友元函数。

	//声明
	friend Rectangle operator*(int arg, const Rectangle & rect);

	//定义
	Rectangle operator*(int arg, const Rectangle & rect){
		Rectangle result;
		result.height = rect.height * arg;
		result.width = rect.width * arg;
		return result;
	}
	//注：千万不要写成Rectangle Rectangle::operator*....
	//因为此处定义的友元函数并不是类的成员函数

友元函数不是类的成员函数，但是可以在访问到类的成员变量，这是其特别的地方。

典型使用示例，与cout结合，重载<<

	friend std::ostream & operator<<(std::ostream & out, const Rectangle & rect);

	std::ostream & operator<<(std::ostream & out, const Rectangle & rect){
		out << "(" << rect.height << "," << rect.width << ")" << std::endl;
		return out;
	}

	Rectangle rect1(1, 1);
	Rectangle rect2(2, 2);
	std::cout << "rect1 is " << rect1 << "rect2 is " << rect2;

运行结果：

<img src="http://anytimekaka.github.io/img/postimg/201503171902.PNG"/>

#类的自动转换和强制类型转换

自动转换：

	int i = 1.2;
	double j = 9;

C++不会自动转换不兼容的类型，所以如果要使用自动类型转换这一特性，需要用户自己来定义。

我们以之前例子中的Rectangle类来说明如何实现不同类型间的**自动类型转换**：

	//定义构造函数，用于定义正方形，使用一个int形参。
	Rectangle(int hw){
		this->height = hw;
		this->width = hw;
	}
	
	//自动转换
	Rectangle rect = 1;

例子中定义的构造函数，可以将一个int型转换为Rectangle类型。由这里可以看出，只有接受一个参数的构造函数才能作为转换函数。

关键词`explicit`用来说明，函数只能用于显示转换。

	explicit Rectangle(int hw);

	//显式强制类型转换，上一个例子中的声明编译不能通过
	Rectangle rect = (Rectangle) 6;

构造函数可以用于将int转换为Rectangle，但是Rectangle怎样自动转换成int呢？（其实这种转换好不现实，汗。。。这里只用于说明转换函数）

	//转换函数：operator typeName();
	operator int() const;
	Rectangle::operator int() const {
		return this->height;
	}
	//请注意：转换函数没有返回类型，operator并不是返回类型；同时不能有参数

	Rectangle rect = 1;
	int i = rect;				//隐式转换
	int i = Rectangle(rect);	//显式转换

#静态成员变量

注意点：不能在类声明中初始化。下图是编译器的自动提示

<img src="http://anytimekaka.github.io/img/postimg/201503230930.png"/>

	//声明中
	static int number;

	//初始化，在声明之外的文件中
	int StringBad::number = 0;

类声明位于头文件中，程序可能在多个地方包含头文件，如果在头文件中进行初始化，就会出现多个初始化语句，从而引发错误。但是，如果静态成员是const整数类型，或者枚举型，则可以在类声明中进行初始化。（在编译时就进行了初始化替换）

#特殊成员函数

构造函数和析构函数是特殊成员函数，如果声明一个类，会默认包含这两个函数。那么是否还有其他的特殊成员函数呢？

先看下面这个栗子有没有问题：

	private:
		char * str;
		int len;
		static int number;

	int StringBad::number = 0;
	StringBad::StringBad(const char * s){
		len = std::strlen(s) + 1;
		str = new char[len];
		std::strcpy(str, s);
		number++;
		std::cout << number << " :\"" << str << "\" String object created." << std::endl;
	}
	StringBad::~StringBad(){
		std::cout << "\"" << str << "\" String object deleted." << std::endl;
		--number;
		std::cout << number << " objects left." << std::endl;
	}

上面是一个类似String类的一个类定义，静态成员变量number用于保存对象的声明个数。现在有下面这种使用情形：

	StringBad bad1("bad one");
	StringBad bad2("bad two");
	StringBad bad3 = bad1;

运行结果如下图：

<img src="http://anytimekaka.github.io/img/postimg/201503231431.png"/>

从图中我们看到bad1被删除了2次，导致number值变成了1。主要原因是这一句：`StringBad bad3 = bad1;`，执行过程中，**赋值运算符和复制构造函数**被调用。

特殊成员函数：

* 默认构造函数/析构函数
* **复制构造函数**
* **赋值运算符**
* 地址运算符
* 移动构造函数
* 移动赋值运算符

特殊成员函数都是自动定义的。下面对这几个特殊成员函数作详细介绍，默认构造函数和析构函数就直接跳过了，这个很简单，相信大家都清楚。

##复制构造函数

上面的那一句赋值语句等效于：

	StringBad bad3 = new StringBad(bad1);

此处的复制构造函数的原型：

	StringBad(const StringBad &);

当新建一个对象并将其初始化为同类现有对象时，复制构造函数会被调用。

一. 以下4种声明都会调用复制构造函数：


	StringBad bad3 = StringBad(bad1);
	StringBad bad3(bad1);
	StringBad bad3 = bad1;
	StringBad * bad3 = new StringBad(bad1);

二. 当程序生成对象副本时，编译器也会调用复制构造函数：

	void callStr(StringBad bad){
		std::cout << bad <<endl;
	}

	callStr(bad1);

当程序调用callStr函数时，会创建一个bad1对象的副本，这时会调用复制构造函数。

##赋值运算符
##地址运算符
##移动构造函数
##移动赋值运算符

#动态内存分配







































