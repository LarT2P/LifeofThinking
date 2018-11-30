---
title: Processing权威指南阅读记录
date: 2018-03-29 22:41:22
categories: 
  - 编程与生活
tags: 
  - 学习
---

![Processing权威指南](https://img1.doubanio.com/lpic/s27080847.jpg)

# 我与Processing

假期的时候，偶然间获知了Processing这门语言，在了解了它简单的语法，和通用的编程规则后，我进行了一些尝试。

很有意思的一个编程环境。以简化了的Java来实现图像绘制和设计的工作。其中蕴含着的数学物理知识，让我感受到了不同学科之间关联的一条若有若无的连线。

为了更为熟悉改语言的内容和规则，我特意找了两本书来看。一本书相当基础的[《爱上processing》](https://pan.baidu.com/s/1Rt9Kl6fuAyNWNC4oN2VMZA)(密码: kdg3)，这本书，只要是有点编程基础的同学，基本上是三四个小时就可以看完的。我很快就翻完了。之后便找到了今天的主角——《Processing权威指南》。这本书可以说是非常之厚了。将近七百页，我花了两周才看完，当然，也是“有选择性的通读”。

看完之后，做个记录，毕竟，这本书的内容除了介绍Processing语言本身的技巧规则外，更多的是引导读者去了解、接触到一些新潮艺术家们的艺术创作旅程中。书中有很多访谈，记录许多杰出的艺术创作者们关于灵感与技术等方面的思考，值得一看。

而且这本书，从头到尾提到了很多编程语言的基础概念，也基本上说清楚了一门面向对象的语言的基本概念。甚至可以当做是一个对于Java语言学习的前奏。只不过这个前奏可能更欢快轻松些。

当然，这本书也为读者更进一步的参与到创作中，实现自己的灵感提供了一些技术上的引导，介绍了很多关于2D&3D图像、声音、视频、网络、电子&移动设备、打印格式等其他属于Processing本身具备或者是第三方支持功能领域的知识。为我们更进一步的了解到Processing创作的可能性提供了一个更为全面的认知。

归根到底，创意终究是最重要的。

其他的都只是装饰而已。

但是，如果连起码的装饰都没有的话，那么，绚烂的创意也是一片苍白。

# Processing语言相关

Processing语言实际上是一个Java的子集。所以很多方面都可以看到Java的影子。

比如说下面这个稍微复杂点的例子

```java
import gifAnimation.*;
GifMaker gifExport;     // 声明对象

int [][] grid, futureGrid;

void setup() {
  size(940, 500);
  frameRate(8);
  
  grid = new int [width][height];           // 目前的图像像素数组
  futureGrid = new int [width][height];     // 下一刻状态数组
  
  // 设定初始状态
  float density = 0.3 * width * height;
  for (int i = 0; i < density; i++) {
    grid[int(random(width))][int(random(height))] = 1;
  }
  
  background(0);
  
  // 设定gif导出相关参数
  gifExport = new GifMaker(this, "2D_CA.gif");  
  gifExport.setRepeat(0);          // make it an "endless" animation
  gifExport.setTransparent(0,0,0);  // black is transparent
}

void draw() {
  for (int x = 1; x < width - 1; x++) {
    for (int y = 1; y < height - 1; y++) {
      // “活着的”相邻细胞数目
      int nb = neighbors(x, y);
      
      if ((grid[x][y] == 1) && (nb < 2)) {
        futureGrid[x][y] = 0;
        set(x, y, color(0));
      } else if ((grid[x][y] == 1) && (nb > 3)) {
        futureGrid[x][y] = 0;
        set(x, y, color(0));
      } else if ((grid[x][y] == 0) && (nb == 3)) {
        futureGrid[x][y] = 1;
        set(x, y, color(255));
      } else {
        futureGrid[x][y] = grid[x][y];
      }
    }
  }
  
  int [][] temp = grid;
  grid = futureGrid;
  futureGrid = temp;
  
  gifExport.setDelay(1);
  gifExport.addFrame();
}

// 统计“活着的”相邻细胞数目
int neighbors (int x, int y) {
  int north = (y + height - 1) % height;
  int south = (y + 1) % height;
  int east = (x + 1) % width;
  int west = (x + width - 1) % width;
  return grid[x][north] +
         grid[east][north] +
         grid[east][y] +
         grid[east][south] +
         grid[x][south] +
         grid[west][south] +
         grid[west][y] +
         grid[west][north];
}

void mousePressed() {
    gifExport.finish();          // write file
    noLoop();
}
```

这是一个二维的细胞自动机的例子。只不过在书中原有基础上添加了点生成gif图的部分。利用了第三方库 —— `gifAnimathion`

    import gifAnimation.*;

库的导入，对象的声明

    GifMaker gifExport;

都和Java是一致的。类似于Processing的姊妹项目Arduino的编程，是由两个主要的函数 `void setup(){}`，`void draw(){}` 构成了程序基本的初始化和自动循环。全局变量需要在外部开头声明。

变量声明，流程控制（书中例程多用循环与条件，可见，循环判断才是关键）与Java基本一致。

下面说说Processing的简化后的优势之处。

书中在附录详细比对了设计们常用的几种语言的关于使用时的简介程度，当然，Processing自然是最简单的。毕竟本身就是一门简化后的语言。基本上所有的编写核心都是围绕绘图控制图像来的。

从最基本的绘制直线，圆，矩形，多边形等简单基本图形，到复杂的改变坐标系，以及利用循环条件结构进行稍复杂运动的表现，再到通过模仿物理规律从而展现出更为灵活多变的复杂情形，都是可以通过简单的几句代码来实现的。而且参数的设置也是很有规律的。

比如说这个例子

```java
void setup() {
  size(100, 100);
}

void draw() {
  background(204);
  diagonals(40, 90);
  diagonals(60, 62);
  diagonals(20, 40);
}

void diagonals(int x, int y) {
  line(x, y, x+20, y-40);
  line(x+10, y, x+30, y-40);
  line(x+20, y, x+40, y-40);
}
```

这段代码主要是利用了自定义的函数`diagonals()`实现了划线的功能，可以看到，这里使用的划线函数`line()`,用法是极其简单明晰的。前两点为起点坐标，后两点为终点坐标。两点定义线段，多么直接，明确。其他的类似的基础函数都是差不多的结构与使用方法，所以说，上手的话，看[《爱上processing》](https://pan.baidu.com/s/1Rt9Kl6fuAyNWNC4oN2VMZA)(密码: kdg3)基本就够了。

哦，对，要注意，最基本的是**使用二维直角坐标系，默认原点为窗口左上角，x轴向右，y轴向**下。

# Processing的潜力

相较其他语言，Processing设计出来就是为了提供给艺术家们一门简单直接，又足够强大的工具来进行创造设计。所以说，注定了它会有很多的扩展与延伸。

对的，它有很多功能库，实现了很多方面的功能。官方有一个库列表，在其主页上可以找到。但是使用各种库的时候，要注意版本。可能会有不兼容。

各种各样的库实现了各方面的功能。在开头我提到的2D&3D图像、声音、视频、网络、电子&移动设备等等都有相关的支持。

你甚至可以用它作为Arduino的数据处理前端，因为它可以处理端口的数据。

虽然，它只有一百多个库，但是，少而精才是现状，不用担心社区的力量，只需创意即可。

# Processing的难点

利用工具实现灵感的关键还是在于**用工具的规则去不断的贴合你的想法**。

但是难点就在于如何将脑子里抽象的灵感，或者是动态的复杂的场景用规则化的，流程化的语言构造出来。这些都是在你实际上手的时候所需要关注的。

就像之前我的一个小尝试。

```java
float num_lines;  // 连线的数目
float x_0, y_0;   // 连线的起始点

void setup() {
  size(500, 500);
  smooth();
}

void draw() {
  frameRate(10);    // 降低帧率
  background(255);  // 白色背景
  
  num_lines = random(0, 10);   // 随机连线数目
  
  for(int i = 0; i < 1000; i++) {
    point(random(0, 500), random(0, 500));  // 背景随机点绘制
  }
  
  for(int i = 0; i < num_lines; i++) {
    x_0 = mouseX + random(-50, 50);
    y_0 = mouseY + random(-50, 50);
    
    line(x_0, y_0, mouseX, mouseY);   // 鼠标连线
    
    noFill();                         // 无填充
    ellipse(mouseX, mouseY, x_0, y_0);// 绘制随机高宽的圆
  }
}
```

就是为了实现在背景上周围随机出现点，然后令鼠标附近的几个点随机的与鼠标连线，类似于一些网站页面里利用Js实现的那种花哨的显示效果，可是最后只能做成这个样子。为了更“花哨”，我又加了一堆随机的圆。(*^__^*) 嘻嘻……

# Processing权威指南

前面所说，都是我这段时间来学习的一个小的想法的汇总。

实际上，我感觉自己接触到的只是皮毛，因为语言的使用是要切合实际的应用的，否则也只是空中楼台，毫无用处。

在这本书里，后面介绍了一些比较复杂的设计思路，也就是利用了物理数学知识来实现一些更为美妙规律的视觉效果。

实际上，这本书让我对于这门语言的使用的关键——需要结合更为有力，更为逻辑的知识，来实现更为美妙魔幻的构思——有了更为切实的了解。而其他的，都是手册知识而已，总览全局后，会查手册就好。