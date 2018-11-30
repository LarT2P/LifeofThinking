---
title: xpath&css选择器
date: 2017-08-24 12:58:21
categories: 
    - 编程与生活
tags:
    - css
    - html
---

# XPATH（参考 [w3school](http://www.w3school.com.cn/xpath/index.asp) 和 [静觅](http://cuiqingcai.com/2621.html) 和 [阮一峰的网络日志](http://www.ruanyifeng.com/blog/2009/07/xpath_path_expressions.html)）

**在 XPath 中，有七种类型的节点：元素、属性、文本、命名空间、处理指令、注释以及文档节点（或称为根节点）。**

节点实际上就是各种html标签，属性，以及被标签包围的文字。

**搜索方法**

*xpath使用路径表达式在xml文档中选取节点。节点通过路径或者step来选取。在形式上，"路径表达式"与传统的文件系统非常类似。*

* 斜杠（/）作为路径内部的分割符。
* 同一个节点有绝对路径和相对路径两种写法。
* 绝对路径（absolute path）必须用"/"起首，后面紧跟根节，比如/step/step/...。
* 相对路径（relative path）则是除了绝对路径以外的其他*法，比如 step/step，也就是不使用"/"起首。
* "."表示当前节点。
* ".."表示当前节点的父节点

下面是详细介绍：

路径表达式 |	结果
----|-----
bookstore |	选取 bookstore 元素的所有子节点。
/bookstore| 选取根元素 bookstore。注释：假如路径起始于正斜杠( / )，则此路径始终代表到某元素的绝对路径！
bookstore/book |	选取属于 bookstore 的子元素的所有 book 元素。
//book |	选取所有 book 子元素，而不管它们在文档中的位置。
bookstore//book |	选择属于 bookstore 元素的后代的所有 book 元素，而不管它们位于 bookstore 之下的什么位置。
//@lang |	选取名为 lang 的所有属性。

*谓语（Predicates）谓语用来查找某个特定的节点或者包含某个指定的值的节点。谓语被嵌在方括号中。*

路径表达式 |	结果
---|---
/bookstore/book[1] |	选取属于 bookstore 子元素的第一个 book 元素。
/bookstore/book[last()] |	选取属于 bookstore 子元素的最后一个 book 元素。
/bookstore/book[last()-1] |	选取属于 bookstore 子元素的倒数第二个 book 元素。
/bookstore/book[position()<3] |	选取最前面的两个属于 bookstore 元素的子元素的 book 元素。
//title[@lang] |	选取所有拥有名为 lang 的属性的 title 元素。
//title[@lang='eng'] |	选取所有 title 元素，且这些元素拥有值为 eng 的 lang 属性。
/bookstore/book[price>35.00] |	选取 bookstore 元素的所有 book 元素，且其中的 price 元素的值须大于 35.00。
/bookstore/book[price>35.00]/title |	选取 bookstore 元素中的 book 元素的所有 title 元素，且其中的 price 元素的值须大于 35.00。

*XPath 通配符可用来选取未知的 XML 元素。*

通配符 |	描述
---|---
\* |	匹配任何元素节点。
@* |	匹配任何属性节点。
node() |	匹配任何类型的节点。

在下面的表格中，我们列出了一些路径表达式，以及这些表达式的结果：

路径表达式 |	结果
---|---
/bookstore/* |	选取 bookstore 元素的所有子元素。
//* |	选取文档中的所有元素。
//title[@*] |	选取所有带有属性的 title 元素。

*通过在路径表达式中使用“|”运算符，可以选取若干个路径。且供选择的路径之间并无关联。互不干扰，必须各自是完整的独立的路径。*

*xpath轴可定义相对于当前节点的节点集。*

轴名称 |	结果
---|---
ancestor |	选取当前节点的所有先辈（父、祖父等）。
ancestor-or-self |	选取当前节点的所有先辈（父、祖父等）以及当前节点本身。
attribute |	选取当前节点的所有属性。
child |	选取当前节点的所有子元素。
descendant |	选取当前节点的所有后代元素（子、孙等）。
descendant-or-self |	选取当前节点的所有后代元素（子、孙等）以及当前节点本身。
following |	选取文档中当前节点的结束标签之后的所有节点。
namespace |	选取当前节点的所有命名空间节点。
parent |	选取当前节点的父节点。
preceding |	选取文档中当前节点的开始标签之前的所有节点。
preceding-sibling |	选取当前节点之前的所有同级节点。
self |	选取当前节点。

步（step）包括：

轴（axis）

    定义所选节点与当前节点之间的树关系

节点测试（node-test）

    识别某个轴内部的节点

零个或者更多谓语（predicate）

    更深入地提炼所选的节点集

**步的语法**：

    轴名称::节点测试[谓语]

实例

例子 |	结果
---|---
child::book |	选取所有属于当前节点的子元素的 book 节点。
attribute::lang |	选取当前节点的 lang 属性。
child::* |	选取当前节点的所有子元素。
attribute::* |	选取当前节点的所有属性。
child::text() |	选取当前节点的所有文本子节点。
child::node() |	选取当前节点的所有子节点。
descendant::book 	|选取当前节点的所有 book 后代。
ancestor::book 	|选择当前节点的所有 book 先辈。
ancestor-or-self::book |	选取当前节点的所有 book 先辈以及当前节点（如果此节点是 book 节点）
child::*/child::price 	|选取当前节点的所有 price 孙节点。

*下面列出了可用在 XPath 表达式中的运算符*：

运算符 |	描述 | 实例 |	返回值
---|---|---|---
\| 	|计算两个节点集 |	//book \| //cd |	返回所有拥有 book 和 cd 元素的节点集
\+ |	加法 |	6 + 4 |	10
\- |	减法 |	6 - 4 |	2
\* |	乘法 |	6 * 4 |	24
div |	除法 |	8 div 4| 	2
mod |	计算除法的余数 |	5 mod 2 	|1
= |	等于 |	price=9.80 	|如果 price 是 9.80，则返回 true。如果 price 是 9.90，则返回 false。
!= 	|不等于 |	price!=9.80 |如果 price 是 9.90，则返回 true。如果 price 是 9.80，则返回 false。
< |	小于 |	price<9.80 	|如果 price 是 9.00，则返回 true。如果 price 是 9.90，则返回 false。
<= |	小于或等于 |	price<=9.80 |	如果 price 是 9.00，则返回 true。如果 price 是 9.90，则返回 false。
\> |	大于 |	price>9.80 |如果 price 是 9.90，则返回 true。如果 price 是 9.80，则返回 false。
\>= |	大于或等于 |	price>=9.80 	|如果 price 是 9.90，则返回 true。如果 price 是 9.70，则返回 false。
or |	或 |	price=9.80 or price=9.70 	|如果 price 是 9.80，则返回 true。如果 price 是 9.50，则返回 false。
and 	|与 |	price>9.00 and price<9.90 	|如果 price 是 9.80，则返回 true。如果 price 是 8.50，则返回 false。


---

这里主要使用是配合`lxml`库，下面是一些例子。

```python
#========
# 文本读取
#========
from lxml import etree
text = '''
<div>
    <ul>
         <li class="item-0"><a href="link1.html">first item</a></li>
         <li class="item-1"><a href="link2.html">second item</a></li>
         <li class="item-inactive"><a href="link3.html">third item</a></li>
         <li class="item-1"><a href="link4.html">fourth item</a></li>
         <li class="item-0"><a href="link5.html">fifth item</a>
     </ul>
 </div>
'''
html = etree.HTML(text)
result = etree.tostring(html)
print(result)
#========
# 文件读取
#========
from lxml import etree
html = etree.parse('hello.html')
result = etree.tostring(html, pretty_print=True)
print(result)
#=============
# 利用xpath解析
#=============
from lxml import etree
html = etree.parse('hello.html')
result_list = html.xpath('//li')
print result_list
```

# CSS选择器（转自[阮一峰的网络日志](http://www.ruanyifeng.com/blog/2009/03/css_selectors.html)）

## 基本选择器

最基本的就是针对标签，类以及id了。

序号 |	选择器	|含义
---|---|---
1.|	* |	通用元素选择器，匹配任何元素
2.|	E	|标签选择器，匹配所有使用E标签的元素
3.|	.info	|class选择器，匹配所有class属性中包含info的元素
4.|	#footer |	id选择器，匹配所有id属性等于footer的元素

```css
* { margin:0; padding:0; }
p { font-size:2em; }
.info { background:#ff0; }
p.info { background:#ff0; }
p.info.error { color:#900; font-weight:bold; }
#info { background:#ff0; }
p#info { background:#ff0; }
```

## 多元素的组合选择器(这个蛮实用的)

序号	|选择器|	含义
---|---|---
5.|	E,F	|多元素选择器，同时匹配所有E元素或F元素，E和F之间用逗号分隔
6.|	E F	|后代元素选择器，匹配所有属于E元素后代的F元素，E和F之间用空格分隔
7.|	E > F	|子元素选择器，匹配所有E元素的子元素F
8.|	E + F	|毗邻元素选择器，匹配所有紧随E元素之后的同级元素F

```css
div p { color:#f00; }
#nav li { display:inline; }
#nav a { font-weight:bold; }
div > strong { color:#f00; }
p + p { color:#f00; }
```

## CSS 2.1 属性选择器

序号|选择器	|含义
---|---|---
9.| 	E[att]	|匹配所有具有att属性的E元素，不考虑它的值。（注意：E在此处可以省略，比如"[cheacked]"。以下同。）
10.|	E[att=val] 	|匹配所有att属性等于"val"的E元素
11.|	E[att~=val] 	|匹配所有att属性具有多个空格分隔的值、其中一个值等于"val"的E元素
12.|	E[att\|=val] 	|匹配所有att属性具有多个连字号分隔（hyphen-separated）的值、其中一个值以"val"开头的E元素，主要用于lang属性，比如"en"、"en-us"、"en-gb"等等

```css
p[title] { color:#f00; }
div[class=error] { color:#f00; }
td[headers~=col1] { color:#f00; }
p[lang|=en] { color:#f00; }
blockquote[class=quote][cite] { color:#f00; }
```

## CSS 2.1中的伪类

序号|	选择器|	含义
---|---|---
13.|	E:first-child 	|匹配父元素的第一个子元素
14.|	E:link 	|匹配所有未被点击的链接
15.|	E:visited 	|匹配所有已被点击的链接
16.|	E:active 	|匹配鼠标已经其上按下、还没有释放的E元素
17.|	E:hover 	|匹配鼠标悬停其上的E元素
18.|	E:focus 	|匹配获得当前焦点的E元素
19.|	E:lang(c) 	|匹配lang属性等于c的E元素

```css
p:first-child { font-style:italic; }
input[type=text]:focus { color:#000; background:#ffe; }
input[type=text]:focus:hover { background:#fff; }
q:lang(sv) { quotes: "\201D" "\201D" "\2019" "\2019"; }
```

## CSS 2.1中的伪元素

序号|	选择器	|含义
---|---|---
20.|	E:first-line	|匹配E元素的第一行
21.| 	E:first-letter	|匹配E元素的第一个字母
22.|	E:before	|在E元素之前插入生成的内容
23.|	E:after	|在E元素之后插入生成的内容

```css
p:first-line { font-weight:bold; color;#600; }

.preamble:first-letter { font-size:1.5em; font-weight:bold; }

.cbb:before { content:""; display:block; height:17px; width:18px; background:url(top.png) no-repeat 0 0; margin:0 0 0 -18px; }

a:link:after { content: " (" attr(href) ") "; }
```

## CSS 3的同级元素通用选择器

序号|	选择器	|含义
---|---|---
24.|	E ~ F |	匹配任何在E元素之后的同级F元素

```css
p ~ ul { background:#ff0; }
```

## CSS 3 属性选择器

序号| 选择器	|含义
---|---|---
25.|	E[att^="val"] 	|属性att的值以"val"开头的元素
26.|	E[att$="val"] 	|属性att的值以"val"结尾的元素
27.|	E[att*="val"] 	|属性att的值包含"val"字符串的元素

```css
div[id^="nav"] { background:#ff0; }
```

## CSS 3中与用户界面有关的伪类

序号|	选择器	|含义
---|---|---
28.|	E:enabled 	|匹配表单中激活的元素
29.|	E:disabled	|匹配表单中禁用的元素
30.|	E:checked	|匹配表单中被选中的radio（单选框）或checkbox（复选框）元素
31.|	E::selection 	|匹配用户当前选中的元素

```css
input[type="text"]:disabled { background:#ddd; }
```

## CSS 3中的结构性伪类(蛮实用的)

序号|	选择器|	含义
---|---|---
32.| 	E:root 	|匹配文档的根元素，对于HTML文档，就是HTML元素
33.|	E:nth-child(n)	|匹配其父元素的第n个子元素，第一个编号为1
34.|	E:nth-last-child(n) 	|匹配其父元素的倒数第n个子元素，第一个编号为1
35.|	E:nth-of-type(n) 	|与:nth-child()作用类似，但是仅匹配使用同种标签的元素
36.| 	E:nth-last-of-type(n)	|与:nth-last-child() 作用类似，但是仅匹配使用同种标签的元素
37.|	E:last-child	|匹配父元素的最后一个子元素，等同于:nth-last-child(1)
38.|	E:first-of-type	|匹配父元素下使用同种标签的第一个子元素，等同于:nth-of-type(1)
39.|	E:last-of-type 	|匹配父元素下使用同种标签的最后一个子元素，等同于:nth-last-of-type(1)
40.|	E:only-child	|匹配父元素下仅有的一个子元素，等同于:first-child:last-child或 :nth-child(1):nth-last-child(1)
41.|	E:only-of-type	|匹配父元素下使用同种标签的唯一一个子元素，等同于:first-of-type:last-of-type或 :nth-of-type(1):nth-last-of-type(1)
42.| 	E:empty	|匹配一个不包含任何子元素的元素，注意，文本节点也被看作子元素

```css
p:nth-child(3) { color:#f00; }
p:nth-child(odd) { color:#f00; }
p:nth-child(even) { color:#f00; }
p:nth-child(3n+0) { color:#f00; }
p:nth-child(3n) { color:#f00; }
tr:nth-child(2n+11) { background:#ff0; }
tr:nth-last-child(2) { background:#ff0; }
p:last-child { background:#ff0; }
p:only-child { background:#ff0; }
p:empty { background:#ff0; }
```

## CSS 3的反选伪类

序号	|选择器|	含义
---|---|---
43.|	E:not(s) 	|匹配不符合当前选择器的任何元素

```css
:not(p) { border:1px solid #ccc; }
```

## CSS 3中的 :target 伪类

序号	|选择器|	含义
---|---|---
44.|	E:target	|匹配文档中特定"id"点击后的效果
