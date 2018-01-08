---
title: "CSS设计指南笔记"
date: 2018-01-04T15:57:10+08:00
draft: true
---
<!--more-->

### 特殊的上下文选择符
* `>`子选择符 `section > h2 {font-style:italic;}`, section必须是h2的父元素，而不能是其它的祖先元素
* `+`紧邻同胞选择符 `h2 + p {font-variant:small-caps;}`, p必须紧跟在其同胞h2的后面
* `~`一般同胞选择符 `h2 ~ a {color:red;}`, a必须跟（不一定紧跟）在其同胞h2的后面
* `*`通用选择符 `* {color:green;}` * 是一个通配符，匹配任何元素

```html
<section>    
    <h2>An H2 Heading</h2>          /* > 选中 */
    <p>This is paragraph 1</p>      /* + 选中 */ 
    <p>Paragraph 2 has <a href="javascript:;">a link</a> in it.</p>    
    <a href="javascript:;">Link</a> /* ~ 选中 */
</section>
```

* 设定所有容器的内外边距为0

```css
* {margin: 0; padding: 0;}
```
## 布局

### 三栏-固定宽度布局

```html
<div id="wrapper">
    <header></header>
    <nav><div></div></nav>
    <article><div></div></article>
    <aside><div></div></aside>
    <footer></footer>
</div>
```

```css
* {margin: 0; padding:0;}
#wrapper {width: 960px; margin:0 auto; border:1px solid;}
header {background: #f00; }
nav     {width:150px; float:left; background:#dcd9c0;}
article {width:600px; float:left; background:#ffed53;}
aside   {width:210px; float:left; background:#3f7ccf;}
footer {clear:both; background:#000}
```
* 防止栏目宽度发生改变的方法

1. 重设宽度以抵消内边距和边框
2. 给容器内部的元素应用内边距和边框
3. 使用CSS3的`box-sizing: border-box`属性切换box缩放方式

### 三栏-中栏流动布局

两种方法：

* 在中栏改变大小时使用负边距定位右栏
* 使用CSS3让栏容器具有类似表格单元的行为

```html
<nav><!-- 内容 --></nav>
<article><!-- 内容 --></article>
<aside><!-- 内容 --></aside>
```
```css
nav     {display:table-cell; width:150px; padding:10px; background:#dcd9c0;}
article {display:table-cell; padding:10px 20px; background:#ffed53;}
aside   {display:table-cell; width:210px; padding:10px; background:#3f7ccf;}
```
只要把每一栏的display属性设定为table-cell,中栏不设宽度就行了

### 多行多栏布局

```html
<div id="wrapper">
    <header><h1>Full-width conent</h1><header>
    <nav><p>Navigation menus go here</p></nav>
    <section id="branding"> </section>
    <section id="feature_area"> </section>
    <section id="promo_area"></section>
    <footer></footer>
</div>
```
## 元素属性

### 浮动
* 围住浮动元素的三种方法

1. 为父元素添加`overflow:hidden`
2. 同时浮动父元素
3. 添加非浮动的清除元素

```html
<section>
    <img src="images/rubber_duck2.jpg">    
    <p>It’s fun to float.</p>
</section>
<footer> Here is the footer element that runs across the bottom of the page.</footer>
```
```css
section {border:1px solid blue; margin:0 0 10px 0;}/*删除默认的上下外边距*/    
img {float:left;}
p {margin 0;}   /*为简明起见，省略了字体声明*/
footer {border:1px solid red;}
```
方法一：

```css
section {border:1px solid blue; margin:0 0 10px 0; overflow:hidden;}
img {float:left;}
p {border:1px solid red;}
```
方法二：

```css
section {border:1px solid blue; float:left; width:100%;}
img {float:left;}
footer {border:1px solid red; clear:left;}
```
方法三:

```css
section {border:1px solid blue;}
img {float:left;}
.clear_me {clear:left;}
footer {border:1px solid red;}
```
给父元素添加.clearfix

```css
.clearfix:after {content:"."; display:block; height:0; visibility:hidden; clear:both;}
```

### 定位

可以使用`top right bottom left`改变位置

* `relative`相对定位：相对的是它原来在文档流中的位置（或者默认位置）
* `absolute`绝对位置：定位起点是`body`元素,当其父元素中有`relative`属性时，则定位起点为此父元素
* `fixed`   固定位置：定位起点是当前屏幕

把元素的position属性设定为`relative、absolute或fixed`后，继而可以使用`top、right、bottom和left`属性，
相对于另一个元素移动该元素的位置。这里的“另一个元素”，就是该元素的定位上下文。

绝对定位元素默认的定位上下文是body。这是因为body是标记中所有元素唯一的祖先元素。
实际上，绝对定位元素的任何祖先元素都可以成为它的定位上下文，只要你把相应祖先元素的position设定为relative即可。
```css
div#outer {position:relative; width:250px; margin:50px 40px; border-top:3px solid red;}
div#inner {position:absolute; top:10px; left:20px; background:#ccc;}
```

### 显示

* 块级元素，比如段落p、标题h2、列表li等，在浏览器中上下堆叠显示,每个元素分别占一行。
* 行内元素，比如a、span和img，在浏览器中左右并排显示，只有前一行没有空间时才会显示到下一行。

把块级元素变成行内元素（或者相反）的魔法如下:

```css
/*默认为block*/p {display:inline;}
/*默认为inline*/a {display:block;}
```

* display:none属性:
该元素及所有包含在其中的元素，都不会在页面中显示。它们原先占据的所有空间也都会被“回收”，就好像相关的标记根本不存在一样。与此相对的是

* visibility属性:
这个属性最常用的两个相对的值是visible（默认值）和hidden。把元素的visibility设定为hidden，元素会隐藏，但它占据的页面空间仍然“虚位以待”。

## 文本布局

这个例子虽然又简单又基础，但它却展示了如何通过最少量的文本样式来提高页面版式的专业水准和内容的可读性

三步调整页面文体布局:
```css
# 1. 去掉元素外边框、设定主字体大小
/*删除所有元素的外边距*/
* {margin:0; padding:0;}

/*设定主字体族和字体大小*/
body {font:1.0em helvetica, arial, sans-serif;}

/*居中显示盒子*/
article {width:500px; margin:20px auto; padding:20px; border:2px solid #999;}

# 2. 为段落下方添加空白
/*标题周围的空白*/
h1, h2, h3, h4, h5, h6 {line-height:1.15em; margin-bottom:.1em;}

/*文本元素周围的空白*/
p, ul, blockquote {line-height:1.15em; margin-bottom:.75em;}

/*缩进列表*/
ul {margin-left:32px;}

# 3.调整字体大小
/*调整标题文本*/
h1 {font-size:1.9em;}
h2 {font-size:1.6em;}
h3 {font-size:1.4em;}
h4 {font-size:1.2em;}
h5 {font-size:1em;}
h6 {font-size:.9em;}

/*调整段落文本*/
p {font-size:.9em;}

/*调整代码文本（默认值太小了）*/
code {font-size:1.3em;}
```
总的来说，用于设定字体大小的单位有两种，
一种是绝对单位，比如像素或点，
另一种是相对单位，比如百分比或em。
推荐使用相对大小，因为使用相对大小后，通过调整body元素的字体大小，可以成比例地改变所有元素的字体大小。
默认情况下，1em等于16像素，这也是`font-size`的基准大小。如果把`body`的字体大小设定为20px，就是重新设定了基准大小

行高推荐也使用相对值，示例：`p {line-height:1.5;}`。CSS中的行高平均分布在一行文本的上方和下方。
修改默认行高最简单的方式就是使用font快捷属性，以复合值的形式把font-size和line-height值写在一块，比如：
```css
div#intro {font:1.2em/1.4 helvetica, arial, sans-serif;}
```
就这个例子来说，行高就是字体大小1.2em的1.4倍。注意这里不用给`line-height` 值指定单位，只要一个数值就可以。此时，浏览器会用计算得到的1.2em的像素数乘以1.4，结果就是行高。

### 弹出层
```css
figcaption {
    display:none; 				/*隐藏弹出层*/    
    position:absolute; 			/*相对于容器（图片）定位*/    
    left:74%; top:14px; 		/*把弹出层放到图片右侧*/    
    width:130px; 				/*弹出层宽度*/    
    padding:10px; 				/*弹出层内边距*/    
    background:#f2eaea;    
    border:3px solid red;    
    border-radius:6px;
}

figure:hover figcaption {display:block;} /*鼠标悬停在图片上时显示弹出层*/
figure:hover figcaption {display:block; z-index:2;} /*把悬停图片的弹出层放到最前面*/

figcaption h3 { 				/*弹出层的内容*/
    font-size:14px;    
    color:#666;    
    margin-bottom:6px;
}

figcaption a { 					/*弹出层的内容*/
    display:block;    
    text-decoration:none;    
    font-size:12px;    
    color:#000;
} 
figcaption::after {             /*红色三角形的盒子*/    
    content:"";                 /*需要有内容，这里是一个空字符串*/    
    position:absolute;          /*相对于弹出层定位*/    
    right:100%; top:17px;       /*相对于盒子边框定位三角形*/    
    border:12px solid;     
    border-color:transparent red transparent transparent;    
    height:0px; width:0px;      /*收缩边框创造三角形*/
}

```

## 列表:导航菜单

* 纵向菜单

```html
<nav >
    <ul>
        <li><a href="javascript:;">Alternative</a></li>
        <li><a href="javascript:;">Country</a></li>
        <li><a href="javascript:;">Jazz</a></li>
        <li><a href="javascript:;">Rock</a></li>
    </ul>
</nav>
```
```css
* {margin:0; padding:0;}         /*去掉默认的内边距和外边距*/
nav {margin:50px; width:150px;}  /*设定本例中菜单的大小和位置*/
.list1 ul {                     /*给菜单加上边框*/
    border:1px solid #f00; 
    border-radius:3px;    
    padding:5px 10px 3px;
} 
.list1 li {                     /*去掉项目符号并为链接添加间距*/
    list-style-type:none; 
    padding:3px 10px;
}      
.list1 li + li {border-top:1px solid #f00;} /*“非首位子元素”选择符*/
.list1 a {                      /*为链接添加样式*/
    text-decoration:none;
    font:20px Exo, helvetica, arial, sans-serif;
    font-weight:400; color:#000;
    background:#ffed53;
}
.list1 a:hover {color:#069;}    /*悬停高亮*/
```
上面的CSS中，只有文本是可以点的，因为链接（a）是行内元素，它会收缩并包住其中的文本。
然而，更好的用户体验是让列表项所在的整行都能点击。
此外，在每个li元素的上方和下方，各有3像素的内边距，导致链接文本与列表项边框之间有了距离。
正因为有了这段距离，当用户鼠标移动到链接间的间隙时，光标会从“悬停于链接之上”时的小手形状，变成常规的箭头形状。
解决这此问题的方式如下:

首先得把内边距从li元素转移到链接内部，然后让链接完全填满整个列表项。
```css
.list1 li {
    list-style-type:none; 
}
.list1 li + li a {border-top:1px solid #f00;}
.list1 a {
    display:block;          /* 让链接完全填满整个列表项 */ 
    padding:3px 10px;       /* 内边距从li元素移到a内部 */ 
    textdecoration: none; 
    font:20px Exo, helvetica, arial, sansserif;
    font-weight:400; color:#000; 
    background:#ffed53;
}
.list1 a:hover {color:#069;}
```

1. 选择符li + li变成li + li a，就可以把上边框添加到列表项后面的列表项所包含的链接元素上
2. 把每个链接的display属性设定为block，这样链接元素的盒子就会由“收缩包围内容”变成“扩展填充父元素”。

上面`li + li`选择符的意思是“任何跟在li之后的li”。实现相同效果的其他方法：

```css
li {border-top:1px solid #f00;}     /*给所有li上方添加一条边框*/
li:first-child {border-top:none;}   /*去掉第一个li上方的边框*/
```

* 横向菜单

```html
<nav >    
    <ul>        
        <li><a href="javascript:;">Shirts</a></li>        
        <li><a href="javascript:;">Pants</a></li>        
        <li><a href="javascript:;">Dresses</a></li>        
        <li><a href="javascript:;">Shoes</a></li>        
        <li><a href="javascript:;">Accessories</a></li>    
    </ul>
</nav>
```
```css
.list1 ul {    
    overflow:hidden;         /*强制ul包围浮动的li元素*/    
}
.list1 li {    
    float:left;             /*让li元素水平排列*/    
    list-style-type:none;   /*去掉项目符号*/    
}
.list1 a {    
    display:block;          /*让链接填满li元素*/    
    padding:0 16px;    
    text-decoration:none;   /*去掉链接的下划线*/    
    color:#999;
}
.list1 li + li a {border-left:1px solid #aaa;}
.list1 a:hover {color:#555;}
```
上面的CSS代码：`float:left`让li元素从垂直变成水平，`display:block`让链接从收缩变成扩张，从而整个li元素都变成了可以点击的。
另外，选择符`li + li a`为除第一个链接之外的每个链接左侧都加了一条竖线，作为视觉分隔线。

### 垂直居中

* 用CSS实现垂直居中也不简单。如果你想在一个固定高度的元素内垂直居中一行文本，
可以把这一行文本的line-height设定为该元素的高度。假设元素高度为300像素，可以这样写：

```css
text-align:center;      /*水平居中*/
line-height:300px;      /*垂直居中：行高=容器高度*/
```
* 如果想垂直居中其他元素，比如图片，可以将容器的display属性设定为table-row，
再设定（只对单元格有效的）vertical-align属性为middle，比如：

```css
display:table-cell;     /*借用表格的行为*/
vertical-align:middle;  /*垂直居中*/
text-align:center;      /*水平居中*/
```

### CSS写法

推荐优先级顺序：

1. display及相关声明；
2. position及相关的声明；
3. margin、padding和border及相关声明；
4. 字体/文本相关声明；
5. 装饰相关声明。

```css
.demo {    
    display:block; 
    position:absolute;    
    height:100px; width:300px; left:10px; top:10px;    
    margin:0 5px; padding:10px;    
    font-size:10px; line-height:1.2;    
    background-color:#eee; border:1px solid; border-radius:6px;
}
```
