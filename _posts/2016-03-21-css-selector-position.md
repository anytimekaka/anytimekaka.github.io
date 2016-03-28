---
layout: post
title:  "CSS选择器"
date:   2016-03-21 12:00:00
categories: 前端技术
permalink: /archivers/css-selector-position
---

全面介绍CSS中的所有类型的选择符。

## 上下文选择符

**一般上下文选择符：**

```html
article h1 em {color:green;}
//选中的em 必须有一个祖先是h1，后者进而还要有一个祖先是article
```

**子选择符**：label1>lalel2。label1必须是label2的父元素，不能是其他的上级元素。

**一般同胞选择符：**label1~label2，label2必须跟在同胞标签label1的后面，不一定紧跟。

**紧邻同胞选择符：**label1+label2，label2必须**紧跟**label1的后面。

**通用选择符：**使用`*`作为通配符，匹配任何元素。

```html
* {color:green;}
//可以匹配所有元素，color为前景色
```

## ID和类选择符

id和类选择符在使用时，不需要考虑html文档的层次结构，只要再标签中添加id和class属性，body标签中的任何元素都可以添加这两个属性。

**类选择符：**

```html
.specialtext {font-style:italic;}
//对于带specicaltext类的标签内内容有效
p.specialtext span {font-weight:blod;}
//对于处于带有specialtext类的<p>标签内的span标签有效
<p class="specialtext">  <span>content</span>    </p>
```

**多类选择符：**

```html
//对同时存在这两个类名的元素产生作用
.specialtext.featured {font-size:120%;}
//两个类名直接不能有空格，如果加了空格，就变成了“上下文”选择符了！
<p class="specialtext featured">  content </p>
```

**ID选择符：**与类选择符基本一样

```html
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

```html
img[title] {border:2px solid blue;}
<img src="" title="flower" alt="flower"/>
//带有title的img元素会显示2像素宽的蓝色边框，title属性是什么值无所谓，只要有title这个属性
```

**属性值选择符：**标签名[属性名=属性值]。与属性名选择符基本类似，只是为属性名添加了特定的值限制。

```html
img[title="flower"] {border:2px solid blue;}
<img src="" title="flower" alt="flower"/>
//title="flower"的img元素会显示2像素宽的蓝色边框
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

```html
a:link {color:black;}    //链接的展示情况
a:visited {color:orange;}    //访问过的链接
a:hover {text-decoration: none;}    //鼠标悬停（不显示下划线）
a:active {color: red;}    //链接正在被点击
//由于这四个伪类的特指度相同，如果不按照上面的顺序使用，浏览器可能不会正常显示。
```

有些伪类可以用于任何元素，而不仅仅是链接，例如hover：

```html
p:hover {background-color:gray;}
```

**:focus（焦点）伪类**

```html
input:focus {border: 3px solid blue;}
```

当input元素获得焦点，光标位于input元素上时，添加一个蓝色的边框。可以让用户明确地知道当前的光标焦点。

**:target（目标）伪类**

如果用户点击一个指向页面中其它元素的链接，则那个元素就是目标（target），可以使用`target`伪类选中它。例如如下html代码：

```html
<a href="#moreinfo">Get more information? Click here!</a>
<p id="moreinfo">Some detail info will be shown here.</p>
```

就可以使用CSS规则进行修饰：

```html
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






