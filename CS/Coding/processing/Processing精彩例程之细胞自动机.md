---
title: Processing精彩例程之细胞自动机
date: 2018-03-25 22:19:41
categories: 
  - 编程与生活
tags: 
  - Java
---

# 一维细胞自动机

![一维细胞自动机](https://wx3.sinaimg.cn/mw1024/a6bff99cly1fpphnr62iej20rt0dxdgs.jpg)

```java
int [] rules = { 0, 0, 0, 1, 1, 1, 1, 0};   // 生成一维规则
int gen = 1;                                // 回合数，等于当前y轴位置加一
color on = color(255);                      // 设定状态 on 为白色
color off = color(0);                       // 设定状态 off 为黑色

void setup() {                              // 入口函数初始化参数
  size(1001, 501);
  frameRate(8);                             // 设定较低的帧率
  background(0);                            // 黑色背景
  set(width/2, 0, on);                      // 设定初始状态
}

void draw() { 
  for (int i = 1; i < width - 1; i++) {     
    // 生成循环
    int left = get(i - 1, gen - 1);
    int me = get(i, gen - 1);
    int right = get(i + 1, gen - 1);
    // 规则生效
    if (rules(left, me, right) == 1) {
      set(i, gen, on);
    }
  }
  gen++;
  
  if (gen > height - 1) {
    noLoop();
    save("CA.png");
  }
}

int rules (color a, color b, color c) {
  // 规则
  if ((a == on) && (b == on) && (c == on)) {return rules[0];}
  else if ((a == on) && (b == on) && (c == off)) {return rules[1];}
  else if ((a == on) && (b == off) && (c == on)) {return rules[2];}
  else if ((a == on) && (b == off) && (c == off)) {return rules[3];}
  else if ((a == off) && (b == on) && (c == on)) {return rules[4];}
  else if ((a == off) && (b == on) && (c == off)) {return rules[5];}
  else if ((a == off) && (b == off) && (c == on)) {return rules[6];}
  else if ((a == off) && (b == off) && (c == off)) {return rules[7];}

  return 0;
}
```

# 二维细胞自动机

![二维细胞自动机](https://wx2.sinaimg.cn/mw1024/a6bff99cly1fpphnvthkjg20q40dwb2a.gif)

需要gif相关的库——gifAnimation。

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
//int neighbors (int x, int y) {
//  return grid[x][y-1] +
//         grid[x+1][y-1] +
//         grid[x+1][y] +
//         grid[x+1][y+1] +
//         grid[x][y+1] +
//         grid[x-1][y+1] +
//         grid[x-1][y] +
//         grid[x-1][y-1];
//}

// 改进的neighbors()
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