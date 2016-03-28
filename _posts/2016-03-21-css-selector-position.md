---
layout: post
title:  "CSS层叠规则、选择器、定位"
date:   2016-03-21 12:00:00
categories: 前端技术
permalink: /archivers/css-selector-position
---

[TOC]

# 选择符

## 上下文选择符

**一般上下文选择符：**

```CSS
article h1 em {color:green;}
/* 选中的em 必须有一个祖先是h1，后者进而还要有一个祖先是article */
```

**子选择符**：label1>lalel2。label1必须是label2的父元素，不能是其他的上级元素。

**一般同胞选择符：**label1~label2，label2必须跟在同胞标签label1的后面，不一定紧跟。

**紧邻同胞选择符：**label1+label2，label2必须**紧跟**label1的后面。

**通用选择符：**使用`*`作为通配符，匹配任何元素。

```CSS
* {color:green;}
/* 可以匹配所有元素，color为前景色 */
```

## ID和类选择符

id和类选择符在使用时，不需要考虑html文档的层次结构，只要再标签中添加id和class属性，body标签中的任何元素都可以添加这两个属性。

**类选择符：**

```CSS
.specialtext {font-style:italic;}
/* 对于带specicaltext类的标签内内容有效 */
p.specialtext span {font-weight:blod;}
/* 对于处于带有specialtext类的<p>标签内的span标签有效 */
<p class="specialtext">  <span>content</span>    </p>
```

**多类选择符：**

```CSS
/* 对同时存在这两个类名的元素产生作用 */
.specialtext.featured {font-size:120%;}
/* 两个类名直接不能有空格，如果加了空格，就变成了“上下文”选择符了！ */
<p class="specialtext featured">  content </p>
```

**ID选择符：**与类选择符基本一样

```CSS
#specialtext {CSS样式声明;}
<p id="specialtext "> content </p>
```

>id与class的区别：id可用于页面内导航。
>
>`<a href="#bio">Biography</a>`带有#的链接表示此链接是导航链接，如果没有#，浏览器默认加载bio目录下的默认页面。如果只有一个#，表示返回页面顶部。另外，如果暂时不知道一个href应该设置什么URL，应该以#作为占位符。

**什么时候用id，什么时候用class**

id可以**唯一**标识一个元素，所以，当需要标识页面的一个唯一独立的部分时，可以使用id，例如页面导航栏等。

class通常用于表示一类元素，例如一个页面有多个按钮，可以为这些按钮设置一样的样式。

## 属性选择符

**属性名选择符：**标签名[属性名]

```CSS
img[title] {border:2px solid blue;}
<img src="" title="flower" alt="flower"/>
/* 带有title的img元素会显示2像素宽的蓝色边框，title属性是什么值无所谓，只要有title这个属性 */
```

**属性值选择符：**标签名[属性名=属性值]。与属性名选择符基本类似，只是为属性名添加了特定的值限制。

```CSS
img[title="flower"] {border:2px solid blue;}
<img src="" title="flower" alt="flower"/>
/* title="flower"的img元素会显示2像素宽的蓝色边框 */
```

前面介绍的都是针对具体的HTML元素定义的css样式，例如根据**标签名、类名、ID、属性值**来确定展示的样式。但是在某些动作发生时，例如**获得鼠标焦点**，这一类事件发生时对样式的影响。这就是我们即将讨论的**伪类**了。

## 伪类

从字面意义上，我们可以以为伪类与前面说的类相似，但是实际上是有本质区别的。

伪类有两种：

* UI伪类：会在HTML元素处于某个状态时（比如鼠标指针位于链接上），为该元素应用CSS样式
* 结构化伪类：当标记中存在某种结构关系时，为相应元素应用CSS样式

### UI伪类

**链接伪类**

最常使用UI伪类的元素是**链接**，利用UI伪类，链接可以在用户鼠标悬停时改变文本颜色，或者去掉文本的下划线等。

```CSS
a:link {color:black;}    /* 链接的展示情况 */
a:visited {color:orange;}    /* 访问过的链接 */
a:hover {text-decoration: none;}    /* 鼠标悬停（不显示下划线） */
a:active {color: red;}    /* 链接正在被点击 */
/* 由于这四个伪类的**特指度**（什么鬼）相同，如果不按照上面的顺序使用，浏览器可能不会正常显示。 */
```

有些伪类可以用于任何元素，而不仅仅是链接，例如hover：

```CSS
p:hover {background-color:gray;}
```

**:focus（焦点）伪类**

```CSS
input:focus {border: 3px solid blue;}
```

当input元素获得焦点，光标位于input元素上时，添加一个蓝色的边框。可以让用户明确地知道当前的光标焦点。

**:target（目标）伪类**

如果用户点击一个指向页面中其它元素的链接，则那个元素就是目标（target），可以使用`target`伪类选中它。例如如下html代码：

```CSS
<a href="#moreinfo">Get more information? Click here!</a>
<p id="moreinfo">Some detail info will be shown here.</p>
```

就可以使用CSS规则进行修饰：

```CSS
#moreinfo:target {background-color: #eee;}
```   

当用户单击链接转向ID为`moreinfo`的元素时，为该元素添加浅灰色背景。维基百科在引用中大量使用了`:target`伪类。

### 结构化伪类

结构化伪类可以根据标记的结构应用样式，比如根据元素的父元素或前面的同胞元素是什么。

**:first-child和:last-child**

:first-child表示一组同胞元素中的第一个元素，last-child是最后一个

```html
ol.result li:first-child {color:blue;}

<ol class="result">
	<li>My fast pony</li>    //选中，蓝色
    <li>My fast pony</li>
    <li>My fast pony</li>
</ol>
```

**:nth-child(n)**

n表示一个数值（odd、even）。例如`li:nth-child(2)`会选中列表中的第二项。此伪类常用于提高**表格**的可读性。

## 伪元素

伪元素是文档中**若有实无**的元素。

```html
p::first-letter {font-size: 300%;}       //段落的首字母放大三倍
p::first-line {font-variant:small-caps;}    //把第一行以小型大写字母显示
p.age::before {content: "Age: ";}
p.age::after {content:" years";}

<p class="age">25</p>    //显示：Age: 25 years
```

## 选择符总结

理解选择符是学习CSS的基础，现在的网站开发基本不会将样式写在HTML文档中，都是独立出CSS静态文件。而且选择符也使得管理样式更加简单。

常用的选择符主要有这些：**上下文选择符，ID和类选中符，伪类中的几个**，伪元素使用比较少。所有的选择符可以参考这里[stylinwithcss](http://www.stylinwithcss.com/resources_css_selectors.php#)

#2. CSS的原理



###2.1 CSS样式的层叠顺序：



* 浏览器默认样式

* 用户样式表（很少使用）

* 作者链接样式（按照它们链接到页面的先后顺序）

* 作者嵌入样式

* 作者行内样式



###2.2 层叠规则：



* 找到应用给每个元素和属性的所有声明

* 按照**顺序**和**权重**排序

* 按**特指度**排序

* 顺序决定权重



第一条比较好理解，下面解释一下后面三条：



按照**顺序**和**权重**排序：**顺序**表示浏览器依次检查5个来源，并设定匹配的属性，如果匹配的属性在下一个来源也有定义，则更新该属性的值。权重是指使用`!important`关键字声明了，相当于确定了这个属性。



按**特指度**排序：先看个例子。



    p {font-size:12px;}

    .largetext {font-size:16px;}

    <p class="largetext">some content</p>



上述这种情况，两条规则都匹配同一个标签，到底应用哪个样式，就需要根据**特指度**来判断了。



>计算特指度——ICE公式

>

>选择符中有ID，I位加1；选择符中有一个类，C位加1；选择符中有一个元素名，E位加1。



所以，上述示例中，使用类选择符的样式会被应用，字体以16px像素显示。



顺序决定权重：如果两条规则都影响同一个元素的同一个属性，特指度也相同，那么**声明靠后**的规则有效。



#定位



###围住浮动元素的三种方法



1、为父元素添加`overflow:hidden`声明，强制包围浮动元素。



overflow:hidden的真正用途是防止包含元素被超大内容撑大，有了这个元素，超大内容会被容器剪切掉。



2、同时浮动父元素（需要注意的是，通常情况下需要添加width:100%，而且对父元素的后面一个元素添加clear:left，强制其呆在父元素的下方）



3、在父元素中添加非浮动的清除元素。



具体做法是，在父元素的最后添加一个非浮动的子元素，然后清除该元素。包含元素一定会包围非浮动的子元素，而且清除会让这个子元素位于**清除一侧**浮动元素的下方，所以包含元素一定会包含这个子元素，从而包含前面的浮动元素。



在包含元素最后添加子元素作为清除元素的方式有两种，一种是简单地添加一个子元素，给它应用clear属性。但是有时加这样一个空内容元素显得多余，这时可以使用第二种方法，如下所示：



    <div class="clearfix">

        //子元素

    </div>



    .clearfix:after {

        content:".";

        display:block;

        height:0;

        visibility:hidden;

        clear:both;

    }



上面代码的作用是添加了一个清除的包含句点作为非浮动元素，必须有内容，句点是最小的内容。规则中的其他内容确保了这个伪元素没有高度，而且在页面上不可见。



###position & display



CSS布局的核心是position属性，对元素盒子应用这个属性，相当于重新定义其在**常规文档流**中的位置。



<table>

<tr><td>static</td><td>静态定位</td></tr>

<tr><td>relative</td><td>相对定位</td></tr>

<tr><td>absolute</td><td>绝对定位</td></tr>

<tr><td>fixed</td><td>固定定位</td></tr>

</table>



静态定位是默认的文档流定位方式；相对定位是指**相对于元素原来在文档流中的位置，也就是默认位置**。可以使用top/right/bottom/left来改变它的位置。



绝对定位中，元素从文档流中被“连根拔起”，然后再相对于其他元素定位。这里引出了一个“定位上下文”的概念，默认的定位上下文是body元素。



固定定位与绝对定位类似，不过，其定位上下文是**浏览器窗口**。所以不会随着页面滚动而移动。



#####关于定位上下文的说明



在将元素的position属性设定为relative、absolute或者fixed之后，可以使用top、right、bottom、left属性设置该元素**相对另外一个元素**的位置。这里的**另外一个元素**就是该元素的**定位上下文**。



绝对定位的默认定位上下文是body。如果想使用其他元素成为绝对定位的定位上下文，只需将其position属性设置为relative就好！



请看示例：



    #outer {

        width:200px;

        margin:50px 40px;

        border-top:3px solid red;

        border-left: 3px solid red;

    }

    #inner {

        top:10px;

        left: 20px;

        background: #ccc;

    }

    

    <div id="outer">

        <div id="inner">内部段落文字</div>

    </div>



实际效果：



<img src="http://anytimekaka.github.io/img/postimg/20150523112534.png"/>



问题：为什么内部div没有相对于外部div移动呢？ 原因在于两个div 默认都是静态定位，它们之间不存在谁是谁的定位上下文这个问题，所以top、left的设置是无效的。在常规文档流中，由于外部div没有内容，内部的div会跟它共享相同的起点。只有将position属性设置为另外三个值，top、left才会起作用。



下面将内部div的position设置为absolute：







从结果中我们可以看出，现在是以body作为定位上下文，内部div完全无视外部div的存在。



现在**将外部div的position属性设置为relative**，看看结果：







可以看到内部div现在是相对于外部div偏移。



#####display属性



我们知道html元素分块级元素 和 行内元素，display属性可以重新设置这一属性。



    p {display:inline; }

    a {display:block; }



另外，display属性还有一个值：none，设置后元素不在页面显示，同时，它们占据的空间也会被回收。于此相对的是visibility属性，其常用的两个值为visible和hidden。如果设置元素的visibility=hidden，元素会隐藏，但是其占据的空间仍然“虚伪以待”。



#字体和文本






