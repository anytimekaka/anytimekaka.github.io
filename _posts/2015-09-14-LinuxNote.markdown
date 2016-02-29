---
layout:     post
title:      "gdb使用手册"
subtitle:   "GDB是GNU开源组织发布的一个强大的UNIX下的程序调试工具。通常，我们习惯于使用图形界面的集成开发工具，像VC、Eclipse等IDE的调试，但如果你是在UNIX平台下做软件，你会发现GDB这个调试工具有比VC、Eclipse的图形化调试器更强大的功能。"
date:       2015-04-01 10:22:00
author:     "anytimekaka"
header-img: "img/post-bg-05.jpg"
---

#目录

* gdb的功能
* 示例演示
* 启动，调试和断点
* 查看运行时数据
* 改变程序执行
* 总结
* 参考文献

#gdb的功能

1. 启动你的程序，可以按照你的自定义的要求随心所欲的运行程序。 
2. 可让被调试的程序在你所指定的调置的断点处停住。（断点可以是条件表达式） 
3. 当程序被停住时，可以检查此时你的程序中所发生的事。 
4. 动态的改变你程序的执行环境。 

#示例演示

程序gdb_test.cpp：

	#include <iostream>
	void print_num(int);

	int main(){
        int x=10;
        int y=20;
        for(int i=1; i<=x; i++){
        	std::cout <<i <<std::endl;
        }
        print_num(y);
        std::cout << "Hello world!"<<std::endl;
        return 0;
	}

	void print_num(int x){
        std::cout <<"the arg number is: " <<x << std::endl;
	}

编译：

	$ g++ -g gdb_test.cpp -o gdb_test

要调试C/C++程序，需要把调试信息加到可执行文件中去，使用编译器的`-g`参数可以实现这点。

启动：

	$ gdb gdb_test

<img src="http://anytimekaka.github.io/img/postimg/20150401104746.png"/>

gdb调试过程中可以调用Linux shell程序，也可以设置断点，下图中设置了2个断点：

<img src="http://anytimekaka.github.io/img/postimg/20150401110302.png"/>

下面开始调试程序

<img src="http://anytimekaka.github.io/img/postimg/20150401110952.png"/>

#启动，调试和断点

###启动gdb

	$ gdb <program>	// program是程序
	$ gdb <program> core	// core是程序非法执行后core dump后产生的文件

调试已运行的程序有两种方法：一是在UNIX下用ps查看正在运行的程序的PID（进程ID），然后用gdb PID格式挂接正在
运行的程序：

	$ gdb <PID>		//	程序是一个服务程序，指定这个服务程序运行时的进程ID
	
二是用gdb关联上源代码，并进行gdb，在gdb中用attach命令来挂接进程的PID。并用detach来取消挂接的进程。 

###暂停和恢复

在gdb中，我们可以有以下几种暂停方式：断点（BreakPoint）、观察点（WatchPoint）、捕捉点（CatchPoint）、信号（Signals）、线程停止（Thread Stops）。如果要恢复程序运行，可以使用c或是continue命令。 

###设置断点

关键词：`break` —— b

<table>
<tr><td>break line_num</td><td>指定行设置断点</td></tr>
<tr><td>break +/-num</td><td>在当前行号的后面(+)或者前面(-)num行设置断点</td></tr>
<tr><td>break func_name</td><td>在func_name函数的入口处设置断点，还未进入函数</td></tr>
<tr><td>break filename:line_num/func_name</td><td>源文件filename的指定地方</td></tr>
<tr><td>break if condition</td><td>condition表示条件，在条件成立时停住</td></tr>
<tr><td>info break</td><td>查看断点</td></tr>
</table>

###设置观察点

观察点一般来观察某个表达式（变量也是一种表达式）的值是否有变化了，如果有变化，马上停住程序。

<table>
<tr><td>watch varName</td><td>一量varName有变化时，马上停住程序</td></tr>
<tr><td>rwatch varName</td><td>当varName被读时，停住程序</td></tr>
<tr><td>awatch</td><td>当varName被读/写时，停住程序</td></tr>
</table>

设置暂停，是调试过程中常用的方式。gdb设置暂停的方式有多种，断点是最常用的。这里我们介绍了如何设置断点和观察点，对于其他的几种暂停方式，不再一一详细说明。详细信息可以参考<a href="https://sourceware.org/gdb/current/onlinedocs/gdb/">gdb官方文档</a>。

#查看运行时数据

###查看栈信息

关键字：`backtrace`--bt。默认打印当前的函数调用栈的所有信息

	(gdb) bt n/-n	//  打印栈顶/栈底n层的栈信息
	(gdb) frame 0/n		//查看当前栈/第n层栈的详细信息
	(gdb) up/down		//往上一层、下一层

###查看变量

关键字：`print`--p

	(gdb) p x	//	打印变量x的值

###查看内存

关键字：`examine`--x

###自动显示

设置一些自动显示的变量，当程序停住时，或是单步跟踪时，这些变量会自动显示。关键字：`display`:

	(gdb) display x

###查看寄存器

	(gdb) info registers 

#改变程序执行

	(gdb) print x=20	//将x改为20，并打印
	(gdb) set var x=30	//将x改为30，不会打印修改后的值
	(gdb) print print_num(20)	//强制调用函数，并打印返回值
	(gdb) call print_num(20)	//强制调用函数，如果返回值是void，就不显示

#总结

GDB是一个强大的命令行调试工具。命令行的强大就是在于，其可以形成执行序列，形成脚本。UNIX下的软件全是命令行的，这给程序开发提代供了极大的便利，命令行软件的优势在于，它们可以非常容易的集成在一起，使用几个简单的已有工具的命令，就可以做出一个非常强大的功能。 

#参考文献

* <a href="http://blog.csdn.net/dadalan/article/details/3758025">dadalan的专栏</a>
* <a href="https://sourceware.org/gdb/current/onlinedocs/gdb/">gdb官方文档</a>

