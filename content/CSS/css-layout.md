---
title: "Css Layout"
date: 2017-12-30T21:01:14+08:00
draft: true
---
本文转载自：[CSS布局十八般武艺都在这里了](https://zhuanlan.zhihu.com/p/25565751)
***
布局是CSS中一个重要部分，本文总结了CSS布局中的常用技巧，包括常用的水平居中、垂直居中方法，以及单列布局、多列布局的多种实现方式（包括传统的盒模型布局和比较新的flex布局实现），希望能给需要的小伙伴带来一些帮助。
<!--more-->

## 1.常用居中方法
居中在布局中很常见，我们假设DOM文档结构如下，子元素要在父元素中居中：
```
<div class="parent">
    <div class="child"></div>
</div>
```

### 水平居中

子元素为行内元素还是块状元素，宽度一定还是宽度未定，采取的布局方案不同。

**子元素为:** 

* 行内元素：对父元素设置text-align:center;
* 定宽块状元素: 设置左右margin值为auto;
* 不定宽块状元素: 设置子元素为display:inline,然后在父元素上设置text-align:center;
* 通用方案: flex布局，对父元素设置display:flex;justify-content:center;

### 垂直居中

垂直居中对于子元素是单行内联文本、多行内联文本以及块状元素采用的方案是不同的。

* 父元素一定，子元素为单行内联文本：设置父元素的height等于行高line-height
* 父元素一定，子元素为多行内联文本：设置父元素的display:table-cell或inline-block，再设置vertical-align:middle;
* 块状元素:设置子元素position:absolute 并设置top、bottom为0，父元素要设置定位为static以外的值，margin:auto;
* 通用方案: flex布局，给父元素设置{display:flex; align-items:center;}。

## 2.单列布局

特征：定宽、水平居中

常见的单列布局有两种：

* 一种是header、content、footer宽度都相同，其一般不会占满浏览器的最宽宽度，但当浏览器宽度缩小低于其最大宽度时，宽度会自适应。
* 一种是header、footer宽度为浏览器宽度，但content以及header和footer里的内容却不会占满浏览器宽度。


### 第一种布局

<style>
.bg_sg { background: seagreen; }
.bg_fg { background: forestgreen; }
.ht100 { height:100px; }
.ht200 { height:200px; }
.layout{ max-width: 480px; margin:0 auto; }
</style>
<div class="layout">
  <div id="l_header" class="bg_sg ht100">头部</div>
  <div id="l_content" class="bg_fg ht200">内容</div>
  <div id="l_footer" class="bg_sg ht100">尾部</div>
</div>

对header、content、footer统一设置width或max-width，并通过margin:auto实现居中。

布局代码：
```css
.layout{
	/* width: 960px; *//*设置width当浏览器窗口宽度小于960px时，单列布局不会自适应。*/
	max-width: 960px;
	margin: 0 auto;
}
<div class="layout">
  <div id="header">头部</div>
  <div id="content">内容</div>
  <div id="footer">尾部</div>
</div>
```

### 第二种布局

<div id="l_header"  class="bg_sg ht100">
  <div class="layout">头部</div>
</div>
<div id="l_content" class="layout bg_fg ht200">内容</div>
<div id="l_footer" class="bg_sg ht100">
  <div class="layout">尾部</div>
</div>

设置header、footer的内容宽度为100%，但header、footer的内容区以及content统一设置width 或 max-width，并通过margin:auto实现居中。

布局代码
```css
.layout{
	/* width: 960px; *//*设置width当浏览器窗口宽度小于960px时，单列布局不会自适应。*/
	max-width: 960px;
	margin: 0 auto;
}
<div id="header">
  <div class="layout">头部</div>
</div>
<div id="content" class="layout">内容</div>
<div id="footer">
  <div class="layout">尾部</div>
</div>
```

## 3.二列&三列布局

* 二列布局的特征是侧栏固定宽度，主栏自适应宽度。
* 三列布局的特征是两侧两列固定宽度，中间列自适应宽度。

二列布局可以看做去掉一个侧栏的三列布局，其布局的思想有异曲同工之妙。

### A. float+margin

<style>
/* a. float+margin */
.a_sub  { width: 100px; float: left; }
.a_extra{ width: 200px; float: right;}
.a_main { margin-left: 100px; margin-right: 200px;}
</style>
<div id="l_content">
  <div class="a_sub bg_sg ht200">left</div>
  <div class="a_extra bg_sg ht200">right</div>
  <div class="a_main bg_fg ht200">main</div>
</div>

原理说明：设置两个侧栏分别向左向右浮动，中间列通过外边距给两个侧栏腾出空间，中间列的宽度根据浏览器窗口自适应。

布局步骤:

1. 对两边侧栏分别设置宽度，并对左侧栏添加左浮动，对右侧栏添加有浮动。
2. 对主面板设置左右外边距，margin-left的值为左侧栏的宽度，margin-right的值为右侧栏的宽度。

```css
.sub  { width: 100px; float: left; }
.extra{ width: 200px; float: right; }
.main { margin-left: 100px; margin-right: 200px;
}
<div id="content">
  <div class="sub">left</div>
  <div class="extra">right</div>
  <div class="main">main</div>
</div>
```
一些说明:

* 注意DOM文档的书写顺序，先写两侧栏，再写主面板，更换后则侧栏会被挤到下一列（圣杯布局和双飞翼布局都会用到）。 　
* 这种布局方式比较简单明了，但缺点是渲染时先渲染了侧边栏，而不是比较重要的主面板。

二列的实现方法:

* 如果是左边带有侧栏的二栏布局，则去掉右侧栏，不要设置主面板的margin-right值，其他操作相同。反之亦然。

### B. position+margin

原理说明：通过绝对定位将两个侧栏固定，同样通过外边距给两个侧栏腾出空间，中间列自适应。

布局步骤:

1. 对两边侧栏分别设置宽度，设置定位方式为绝对定位。
2. 设置两侧栏的top值都为0，设置左侧栏的left值为0， 右侧栏的right值为0。
3. 对主面板设置左右外边距，margin-left的值为左侧栏的宽度，margin-right的值为右侧栏的宽度。

```css
sub,extra { position: absolute; top: 0; width: 200px }
sub  { left: 0; }
extra{ right: 0; }
main { margin: 0 200px; }
<div class="sub>left</div>
<div class="main>main</div>
<div class="extra>right</div>
```

一些说明:

* 本方法不限制DOM书写顺序，先写主面板会使主面板部分优先渲染（一般主面板会比侧栏内容重要）。
* 与上一种方法相比，本种方法是通过定位来实现侧栏的位置固定。
* 如果中间栏含有最小宽度限制，或是含有宽度的内部元素，则浏览器窗口小到一定程度，主面板与侧栏会发生重叠。

二列的实现方法

* 如果是左边带有侧栏的二栏布局，则去掉右侧栏，不要设置主面板的margin-right值，其他操作相同。反之亦然。

### C. float + 负margin(圣杯布局)

<style>
.c_main {float: left; width: 100%; }
.c_sub  {float: left; width: 190px; margin-left: -100%; position: relative; left: -190px; }
.c_extra{float: left; width: 230px; margin-left: -230px; position: relative; right: -230px; }
#c_bd   {padding: 0 230px 0 190px; }
.c_bd	{margin-bottom: 230px;}
</style>
<div id="c_bd" class="c_bd">         
  <div class="c_main bg_fg ht200">main</div> <div class="c_sub bg_sg ht200">left</div> <div class="c_extra bg_sg ht200">right</div>
</div>


原理说明：

主面板设置宽度为100%，主面板与两个侧栏都设置浮动，常见为左浮动，这时两个侧栏会被主面板挤下去。通过负边距将浮动的侧栏拉上来，左侧栏的负边距为100%，刚好是窗口的宽度，因此会从主面板下面的左边跑到与主面板对齐的左边，右侧栏此时浮动在主面板下面的左边，设置负边距为负的自身宽度刚好浮动到主面板对齐的右边。为了避免侧栏遮挡主面板内容，在外层设置左右padding值为左右侧栏的宽度，给侧栏腾出空间，此时主面板的宽度减小。由于侧栏的负margin都是相对主面板的，两个侧栏并不会像我们理想中的停靠在左右两边，而是跟着缩小的主面板一起向中间靠拢。此时使用相对布局，调整两个侧栏到相应的位置。



布局步骤:

1. 三者都设置向左浮动。
2. 设置main宽度为100%，设置两侧栏的宽度。
3. 设置 负边距，sub设置负左边距为100%，extra设置负左边距为负的自身宽度。
4. 设置main的padding值给左右两个子面板留出空间。
5. 设置两个子面板为相对定位，sub的left值为负的sub宽度，extra的right值为负的extra宽度。

```css
.main {float: left; width: 100%; }
.sub  {float: left; width: 190px; margin-left: -100%; position: relative; left: -190px; }
.extra{float: left; width: 230px; margin-left: -230px; position: relative; right: -230px; }
#bd   {padding: 0 230px 0 190px; }
<div id="bd">         
  <div class="main">main</div> 
  <div class="sub">left</div> 
  <div class="extra">right</div>
</div>
```
一些说明

* DOM元素的书写顺序不得更改。
* 主面板部分优先渲染（一般主面板会比侧栏内容重要）。
* 当面板的main内容部分比两边的子面板宽度小的时候，布局就会乱掉。可以通过设置main的min-width属性或使用双飞翼布局避免问题。

二列的实现方法

* 如果是左边带有侧栏的二栏布局，则去掉右侧栏，不要设置主面板的padding-right值，其他操作相同。反之亦然。


### D. float + 负margin(双飞翼布局)
<style>
.d_main-wrap{float: left; width: 100%; }
.d_sub  {float: left; width: 190px; margin-left: -100%; }
.d_extra{float: left; width: 230px; margin-left: -230px; }
.d_main {margin: 0 230px 0 190px; }
</style>
<div class="d_main-wrap">
  <div class="d_main bg_fg ht200">main</div>
</div>
<div class="d_sub bg_sg ht200">left</div> <div class="d_extra bg_sg ht200">right</div>

原理说明：

双飞翼布局和圣杯布局的思想有些相似，都利用了浮动和负边距，但双飞翼布局在圣杯布局上做了改进，在main元素上加了一层div, 并设置margin,由于两侧栏的负边距都是相对于main-wrap而言，main的margin值变化便不会影响两个侧栏，因此省掉了对两侧栏设置相对布局的步骤。

布局步骤:

1. 三者都设置向左浮动。
2. 设置main-wrap宽度为100%，设置两个侧栏的宽度。
3. 设置 负边距，sub设置负左边距为100%，extra设置负左边距为负的自身宽度。
4. 设置main的margin值给左右两个子面板留出空间。

```css
.main-wrap{float: left; width: 100%; }
.sub  {float: left; width: 190px; margin-left: -100%; }
.extra{float: left; width: 230px; margin-left: -230px; }
.main {margin: 0 230px 0 190px; }
<div class="main-wrap">
  <div class="main">main</div>
</div>
<div class="sub">left</div>        
<div class="extra">right</div>
```

一些说明

* 主面板部分优先渲染（一般主面板会比侧栏内容重要）。
* 圣杯采用的是padding，而双飞翼采用的margin，解决了圣杯布局main的最小宽度不能小于左侧栏的缺点。
* 双飞翼布局不用设置相对布局，以及对应的left和right值。
* 通过引入相对布局，可以实现三栏布局的各种组合，例如对右侧栏设置position: relative; left: 190px;,可以实现sub+extra+main的布局。

二列的实现方法

* 如果是左边带有侧栏的二栏布局，则去掉右侧栏，不要设置main-wrap的margin-right值，其他操作相同。反之亦然。

#### 圣杯与双飞翼对比总结:

* 俩种布局方式都是把主列放在文档流最前面，使主列优先加载。
* 两种布局方式在实现上也有相同之处，都是让三列浮动，然后通过负外边距形成三列布局。
* 两种布局方式的不同之处在于如何处理中间主列的位置：圣杯布局是利用父容器的左、右内边距定位；双飞翼布局是把主列嵌套在div后利用主列的左、右外边距定位。

### E. flex布局
Flexbox又叫弹性盒模型。它可以简单使用一个元素居中（包括水平垂直居中），可以让扩大和收缩元素来填充容器的可利用空间，可以改变源码顺序独立布局，以及还有其他的一些功能。
<style>
.e_layout {display: flex; }
.e_main {flex: 1; }
.e_aside {width: 200px; }
</style>
<div class="e_layout">
    <aside class="e_aside bg_sg ht200">侧边栏宽度固定</aside>
    <div class="e_main bg_fg ht200">主内容栏宽度自适应</div>
</div>
<div class="e_layout">
    <div class="e_main bg_fg ht200">主内容栏宽度自适应</div>
    <aside class="e_aside bg_sg ht200">侧边栏宽度固定</aside>
</div>
<div class="e_layout">
    <aside class="e_aside bg_sg ht200">左侧边栏宽度固定</aside>
    <div class="e_main bg_fg ht200">主内容栏宽度自适应</div>
    <aside class="e_aside bg_sg ht200">右侧边栏宽度固定</aside>
</div>
<div class="e_layout">
    <aside class="e_aside bg_sg ht200">第1个侧边栏宽度固定</aside>
    <aside class="e_aside bg_sg ht200">第2个侧边栏宽度固定</aside>
    <div class="e_main bg_fg ht200">主内容栏宽度自适应</div>
</div>
<div class="e_layout">
    <div class="e_main bg_fg ht200">主内容栏宽度自适应</div>
    <aside class="e_aside bg_sg ht200">第1个侧边栏宽度固定</aside>
    <aside class="e_aside bg_sg ht200">第2个侧边栏宽度固定</aside>
</div>

与之前所讲的几种传统布局方案相比，flex布局的代码可谓异常简洁，而且非常通用，利用简单的三行CSS即实现了常见的五种布局。

以下是五种布局的flex布局代码：
```css
.layout{display: flex; }
.main  {flex: 1; }
.aside {width: 200px; }
<div class="layout">
    <aside class="aside">侧边栏宽度固定</aside>
    <div class="main">主内容栏宽度自适应</div>
</div>
<div class="layout">
    <div class="main">主内容栏宽度自适应</div>
    <aside class="aside">侧边栏宽度固定</aside>
</div>
<div class="layout">
    <aside class="aside">左侧边栏宽度固定</aside>
    <div class="main">主内容栏宽度自适应</div>
    <aside class="aside">右侧边栏宽度固定</aside>
</div>
<div class="layout">
    <aside class="aside">第1个侧边栏宽度固定</aside>
    <aside class="aside">第2个侧边栏宽度固定</aside>
    <div class="main">主内容栏宽度自适应</div>
</div>
<div class="layout">
    <div class="main">主内容栏宽度自适应</div>
    <aside class="aside">第1个侧边栏宽度固定</aside>
    <aside class="aside">第2个侧边栏宽度固定</aside>
</div>

```

学习flex布局，参考阮一峰老师的两篇博文

* [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html) 
* [Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)






参考资料:

* [CSS布局十八般武艺都在这里了](https://zhuanlan.zhihu.com/p/25565751)
