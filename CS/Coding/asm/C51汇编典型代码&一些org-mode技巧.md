---
title: C51汇编典型代码&一些org-mode技巧
date: "2018-07-13"
categories: 
    - 编程与生活
tags: 
  - 汇编
  - python
  - org-mode
---

关于51，关于org-mode..

<!-- more -->

# C51汇编典型代码&一些org-mode技巧

[文档存放](https://github.com/lartpang/somethings/tree/master/AT89C51%E7%BB%BC%E5%90%88%E8%AE%BE%E8%AE%A1)

具体内容可见存放的数据。

下面主要介绍关键代码。

## ASM 部分

```asm
;; LCD数据发送=============================================================
OUT_DATA:
    PUSH    01H
    PUSH    02H
    PUSH    03H
    MOV     R2, #32             ;32行,(双屏结构中上半屏)
    MOV     R3, #80H            ;Y地址寄存器
LCD_ADDR_UP:
    MOV     SONG, R3            ;设置绘图区的Y地址坐标
    INC     R3                  ;Y地址加1
    LCALL   SEND_ML
    MOV     SONG, #80H          ;设置绘图区的X地址坐标
    LCALL   SEND_ML
    MOV     R1, #16              ;16*8列
LCD_DISP_UP:
    CLR     A
    MOVC    A, @A+DPTR
    MOV     SONG, A
    LCALL   SEND_SJ
    INC     DPTR
    DJNZ    R1, LCD_DISP_UP
    DJNZ    R2, LCD_ADDR_UP      ;写满全屏的16*8字节X64
    MOV     R2, #32             ;32行,(双屏结构的下半屏)
    MOV     R3, #80H            ;Y地址寄存器
LCD_ADDR_DOWN:
    MOV     SONG, R3            ;设置绘图区的Y地址坐标
    INC     R3                  ;Y地址加1
    LCALL   SEND_ML
    MOV     SONG, #88H          ;设置绘图区的X地址坐标
    LCALL   SEND_ML
    MOV     R1, #16             ;16*8列
LCD_DISP_DOWN:
    CLR A
    MOVC    A, @A+DPTR
    MOV     SONG, A
    LCALL   SEND_SJ
    INC     DPTR
    DJNZ    R1, LCD_DISP_DOWN
    DJNZ    R2, LCD_ADDR_DOWN        ;写满全屏的16*8字节X64
    POP     03H
    POP     02H
    POP     01H
    RET
```

这部分介绍了如何在 LCD12864 上进行绘图操作。

值得纪念的是，这部分代码是我自己摸索，对比着网上找来的资料，不断地调整，最后终于搞定的。

*开心的是，分享给了其他人，表现顽强*

[LCD12864 GDRAM存储结构](https://wx4.sinaimg.cn/mw690/a6bff99cly1fs3wcf3pv9j20bz06s0tp.jpg)

> 目前还没学会如何在 org-mode 中直接插入网络图片，并且可以直接显示。。

## python 部分

```python
import binascii
import serial
from time import sleep
import matplotlib.pyplot as plt
import matplotlib.animation as animation


#初始数据绘图
def update(frame):
    #读入模拟
    read_string = binascii.hexlify(ser.read()).decode('ascii')
    a = (int(read_string[0], 16) * 16 + int(read_string[1], 16))
    print(a)
    sleep(0.1)
    #绘图数据生成
    del(data_read[0])
    data_read.append(a)
    #绘图
    line.set_ydata(data_read)
    #颜色设置
    if abs(a) >= 40:
        plt.setp(line, 'color', 'r', 'linewidth', 2.0)
    else:
        plt.setp(line, 'color', 'b', 'linewidth', 2.0)
    return line

if __name__ == '__main__':
    data_read = list(range(100))
    fig, ax = plt.subplots()
    line, = ax.plot(data_read)
    ax.set_ylim(0, 100)
    plt.grid(True)
    ax.set_ylabel("Temperature: ℃")
    ax.set_xlabel("Relative Time: s")

    ser = serial.Serial(port='COM3', baudrate=1200)
    ani = animation.FuncAnimation(fig, update, frames=None, interval=100)
    plt.show()
ser.close()
```

这段代码中使用了 matplotlib.animation 这个特殊的子模块，用来绘制动态图。

最开始用的是 Tkinter 来配合 matplotlib 显示串口数据的状态，但是不适合大量数据连续的绘制。会出现卡死的状态。

直到最后，还是放弃了自定义界面的想法，干脆只是单纯的做图，于是有了这个版本。

但是还是要记录下之前的努力，下面的是原来的基于 Tkinter 和 matplotlib 的代码。

```python
#  coding:utf-8
"""
实现读取端口数据实现绘图
2018年5月30日23:39:32
"""
import binascii
import time
from tkinter import *

import matplotlib
import serial
from matplotlib.backends.backend_tkagg import FigureCanvasTkAgg
from matplotlib.backend_bases import key_press_handler
from matplotlib.figure import Figure


def drawPic():
    """
    获取GUI界面设置的参数，利用该参数绘制图片
    """
    # 清空图像，以使得前后两次绘制的图像不会重叠
    screen_draw.clf()
    add_plot = screen_draw.add_subplot(111)
    add_plot.axis([0, 100, 00, 100])

    x = list(range(100))
    y = data_read
    add_plot.plot(x, y, 'r', label='Temperature')
    add_plot.set_title('DRAW')
    canvas_draw.draw()


def serial_data():
    """处理串口数据"""
    # 打开端口
    try:
        port = input_port.get()
        baudrate = int(input_baudrate.get())
    except:
        port = 'COM3'
        baudrate = 9600
        input_port.delete(0)
        input_baudrate.delete(0)
        input_port.insert(0, 'COM3')
        input_baudrate.insert(0, '9600')

    ser = serial.Serial(port, baudrate)
    list_index = list(range(50))
    data_read[0: 50] = data_read[50: 100]
    for x in list_index:
        read_string = binascii.hexlify(ser.read()).decode('ascii')
        data_read[x + 50] = int(read_string[0], 16) * \
            16 + int(read_string[1], 16)
        print(data_read[x])
    print("Over")
    ser.close()


def now_draw():
    """实时绘制图像"""
    try:
        port = input_port.get()
        baudrate = int(input_baudrate.get())
        delay = int(input_delay.get())
    except:
        port = 'COM3'
        baudrate = 9600
        delay = 10
        input_port.delete(0)
        input_baudrate.delete(0)
        input_delay.delete(0)
        input_port.insert(0, 'COM3')
        input_baudrate.insert(0, '9600')
        input_delay.insert(0, '10')

    list_index_draw = list(range(100))
    ser = serial.Serial(port, baudrate)

    start = time.clock()

    while True:
        read_string = binascii.hexlify(ser.read()).decode('ascii')
        data_read[99] = int(read_string[0], 16) * 16 + int(read_string[1], 16)
        data_read[0: 99] = data_read[1: 100]

        drawPic()

        # 指定退出死循环方式
        if(data_read[99] == 85):    # 温度采集模块关闭后，串口传回来的值就是 55H = 85
            break
        elif(delay > 0):
            if(time.clock() - start >= delay):
                break

    ser.close()


def on_key_event(event):
    print('推出程序 %s' % event.key)
    root.quit()
    root.destroy()


if __name__ == '__main__':
    matplotlib.use('TkAgg')

    # 用于接受数据的存储
    data_read = list(range(100))
    root = Tk()

    # 在Tk的GUI上放置一个画布，并用.grid()来调整布局
    screen_draw = Figure(figsize=(5, 4), dpi=100)
    canvas_draw = FigureCanvasTkAgg(screen_draw, master=root)
    # 绑定任意键退出
    canvas_draw.mpl_connect('key_press_event', on_key_event)

    canvas_draw.get_tk_widget().grid(row=0, columnspan=3)
    canvas_draw.draw()

    # 放置标签、文本框和按钮等部件，并设置文本框的默认值和按钮的事件函数
    Label(root, text='请输入监测时长(s),0则无限,关闭测温即可停止:').grid(row=1, column=0)
    input_delay = Entry(root)
    input_delay.grid(row=1, column=1)
    input_delay.insert(0, '50')

    Label(root, text='请输入端口号:').grid(row=2, column=0)
    input_port = Entry(root)
    input_port.grid(row=2, column=1)
    input_port.insert(0, 'COM3')

    Label(root, text='请输入波特率:').grid(row=3, column=0)
    input_baudrate = Entry(root)
    input_baudrate.grid(row=3, column=1)
    input_baudrate.insert(0, '9600')

    Button(
        root, text='实时监控', command=now_draw).grid(
            row=1, column=2, columnspan=1)
    Button(
        root, text='开始画图', command=drawPic).grid(
            row=2, column=2, columnspan=1)
    Button(
        root, text='打开端口', command=serial_data).grid(
            row=3, column=2, columnspan=1)

    # 启动事件循环
    root.mainloop()
```

这里倒是学习了下 Tkinter 的相关操作，还是不错的。自定义的能力更强。不过很可惜。

并不是很适合处理动态数据显示。

## ORG-MODE 部分

[org-mode-babel](http://www.3zso.com/archives/orgmode-babel.html)

```org-mode
简单模板对应快捷输入的首字母

输入 < 后面跟一个字母，然后按 TAB 键，就可以生成对应的模板。（eg: <e + TAB ）

s   #+BEGIN_SRC ... #+END_SRC
e   #+BEGIN_EXAMPLE ... #+END_EXAMPLE
q   #+BEGIN_QUOTE ... #+END_QUOTE
v   #+BEGIN_VERSE ... #+END_VERSE
c   #+BEGIN_CENTER ... #+END_CENTER
l   #+BEGIN_LaTeX ... #+END_LaTeX
L   #+LaTeX:
h   #+BEGIN_HTML ... #+END_HTML
H   #+HTML:
a   #+BEGIN_ASCII ... #+END_ASCII
A   #+ASCII:
i   #+INDEX: line
I   #+INCLUDE: line
#+END_EXAMPLE

#+BEGIN_EXAMPLE
（7） 文档元数据

#+TITLE:       the title to be shown (default is the buffer name)
#+AUTHOR:      the author (default taken from user-full-name)
#+DATE:        a date, an Org timestamp1, or a format string for format-time-string
#+EMAIL:       his/her email address (default from user-mail-address)
#+DESCRIPTION: the page description, e.g. for the XHTML meta tag
#+KEYWORDS:    the page keywords, e.g. for the XHTML meta tag
#+LANGUAGE:    language for HTML, e.g. ‘en’ (org-export-default-language)
#+TEXT:        Some descriptive text to be inserted at the beginning.
#+TEXT:        Several lines may be given.
#+OPTIONS:     H:2 num:t toc:t \n:nil @:t ::t |:t ^:t f:t TeX:t ...
#+BIND:        lisp-var lisp-val, e.g.: org-export-latex-low-levels itemize
               You need to confirm using these, or configure org-export-allow-BIND
#+LINK_UP:     the ``up'' link of an exported page
#+LINK_HOME:   the ``home'' link of an exported page
#+LATEX_HEADER: extra line(s) for the LaTeX header, like \usepackage{xyz}
#+EXPORT_SELECT_TAGS:   Tags that select a tree for export
#+EXPORT_EXCLUDE_TAGS:  Tags that exclude a tree from export
#+XSLT:        the XSLT stylesheet used by DocBook exporter to generate FO file

其中#+OPTIONS是复合的选项，包括：

H:         set the number of headline levels for export
num:       turn on/off section-numbers
toc:       turn on/off table of contents, or set level limit (integer)
\n:        turn on/off line-break-preservation (DOES NOT WORK)
@:         turn on/off quoted HTML tags
::         turn on/off fixed-width sections
|:         turn on/off tables
^:         turn on/off TeX-like syntax for sub- and superscripts.  If
           you write "^:{}", a_{b} will be interpreted, but
           the simple a_b will be left as it is.
-:         turn on/off conversion of special strings.
f:         turn on/off footnotes like this[1].
todo:      turn on/off inclusion of TODO keywords into exported text
tasks:     turn on/off inclusion of tasks (TODO items), can be nil to remove
           all tasks, todo to remove DONE tasks, or list of kwds to keep
pri:       turn on/off priority cookies
tags:      turn on/off inclusion of tags, may also be not-in-toc
<:         turn on/off inclusion of any time/date stamps like DEADLINES
*:         turn on/off emphasized text (bold, italic, underlined)
TeX:       turn on/off simple TeX macros in plain text
LaTeX:     configure export of LaTeX fragments.  Default auto
skip:      turn on/off skipping the text before the first heading
author:    turn on/off inclusion of author name/email into exported file
email:     turn on/off inclusion of author email into exported file
creator:   turn on/off inclusion of creator info into exported file
timestamp: turn on/off inclusion creation time into exported file
d:         turn on/off inclusion of drawers
```
