---
title: 利用豆瓣短评数据生成词云
date: 2017-08-18 18:08:36
categories: 
    - 编程与生活
tags: 
    - python
---

在之前的文章中，我们获得了豆瓣爬取的短评内容，汇总到了一个文件中，但是，没有被利用起来的数据是没有意义的。

前文提到，有一篇微信推文的关于词云制作的一个实践记录，准备照此试验一下。

<!--more-->

# 思路分析

## 读文件

利用`with open() as...`将文件读进来。这里需要注意文件内容的大小。

## 分词

由于获取的是大量的短评文字，而制作词云需要的是各种词语，有了词，才能谈词云，所以目前第一步需求的就是讲短评内容拆分成一个个的中文词汇。

这里就用到了我所听过的一个库`jieba`，可以将中文语句拆解成一个个的词汇。这里是用的是`lcut()`方法，能将中文字符串拆解成一个列表，每项都是一个词。

## 清洗非中文

但是，我们在分析中，需要的就是中文文字，所以需要将非中文字符彻底清理，这里使用了正则表达式。短小精悍的一个模式`[\u4e00-\u9fa5]+`即可匹配。

使用正则表达式，我的习惯是现在网上的一些在线正则表达式工具上直接测试。其中`oschina`的不错，还给提供了一些例子。

[在线正则表达式测试](http://tool.oschina.net/regex/)

这里是`oschina`的工具网站，做的很好。

[工具分类索引](http://tool.oschina.net/)

## 处理停词

由于这些词汇中，有很多词是没有实际分析价值的，所以我们需要利用一个停词文件来将不必要的词处理掉。

参考文章中，是利用`pandas`库汇总的方法`read_csv()`来处理停词文件。，利用一个`isin()`方法实现了停词。

## 聚合

词分开了，基本也处理干净了。接下来应该考虑制作词云的问题。

我们这里想要重点突出在所有评论中的重要的**核心观点**，为了实现这样的目的，我们使用了分词。

这似乎是一种有些“*断章取义*”的思路。借助词频的分布实现重点突出高词频内容的方式，来展现我们的词云。

所以现在我们需要做的事，就是处理词汇的聚合问题，统计词频而已。

参考文种中利用了类`DataFrame`的分组方法`group()`和聚合方法`agg()`。

关于这里，参考文章中在`agg()`中使用了一个显式的字典（可见文末参考文章），调用了`numpy.size`，但是似乎是这种用法将来会被移除，查了一些文章，说是可以这样用，就是不能自己定制字典了。

```python
FutureWarning: using a dict on a Series for aggregation is deprecated and will be removed in a future version
```

## 词云

这里使用了第三方库`wordcloud`。这个库在安装的时候，直接`pip install wordcloud`时，我出了问题，提示微软开发工具的问题，折腾了半天，最后还是直接在[一个极为丰富的第三方库的集合站点上](http://www.lfd.uci.edu/~gohlke/pythonlibs/)下载使用`pip insatll`了它的`whl`文件。

这下可以正常使用了。

同时，这里为了能够显示处理图片，使用了`matplotlib.pyplot`&`numpy`来进行处理。

### 掩膜设置

由[`wordcloud项目主页README`](https://github.com/amueller/word_cloud) 了解，可以使用二值图像来设定掩膜(mask)。

出于提升数据的表现力，也出于学习的目的，这里使用了直接编写的`rgb2gray()`&`gray2bw()`函数来实现真彩图像转换为二值图像的过程。获得了最终的二值图像掩膜。

这里开始我并不知道需要怎样的图像，看了给的示例代码，用的图片的是二值图像，才明白，白白浪费了好多时间。

而且，我的理解，由彩色转为二值图像，是必要经过灰度图像这个过程的。

关于`matplotlib.pyplot`的使用，网上都说，和`matlab`的语法很类似，以前了解过一点，所以看着例子中的`imshow()`，很自然的就想出了`imread()`，实现了图片的读取。

在查阅文档的过程中发现了一个有意思的地方。

[文档](http://matplotlib.org/api/pyplot_api.html?highlight=imshow#matplotlib.pyplot.imread)

>Return value is a numpy.array. For grayscale images, the return array is MxN. For RGB images, the return value is MxNx3. For RGBA images the return value is MxNx4.
>matplotlib can only read PNGs natively, but if PIL is installed, it will use it to load the image and return an array (if possible) which can be used with imshow(). Note, URL strings may not be compatible with PIL. Check the PIL documentation for more information.

我文中使用的是JPG图像，可见是调用了PIL处理。

而这里对于二值图像的获取，开始经历了一个误区。由于在网上搜索的时候，搜到的大多是利用`PIL`库的`Image`模块的`open()`&`convert()`方法的处理，附加参数`1`，可以实现二值图像的转化，但是在这里使用，后面在使用词云的时候，会提示缺少属性，可见这里不适合这样处理。

### 词云设定

词云支持自定义字体，背景颜色，掩膜设置等等，可以直接在IDE中跳至源文件中查看。都有相关的介绍。

文末代码是一些参数的摘录。

### 词频选择

这里使用了刚才聚合排序好的数据，选择了前1000个词进行展示，并组合成字典，传入了词云的实例对象的方法`fit_words()`生成了词云。

### 词云展示

这里使用了`matplotlib.pyplot`的的几个函数，实现了图像的保存，显示，以及坐标轴的隐藏。

这里倒是有个小异或，有点分不清楚`imshow()`与`show()`了。两者从文档我也没看出个所以然来。不过他们有个最明显的区别就是后者依赖图形窗口，但是前者似乎不需要。

要是有明白的，还请大家留言或者发邮件给我。

# 完整代码

```python
# -*- coding: utf-8 -*-
"""
Created on Thu Aug 17 16:31:35 2017

@note: 为了便于阅读，将模块的引用就近安置了
@author: lart
"""

# 读取事先爬取好的文件，由于文件较小，直接一次性读入。若文件较大，则最好分体积读入。
with open('秘密森林的短评.txt', 'r', encoding='utf-8') as file:
    comments = file.readlines()
    comment = ''.join(comments)


# 摘取中文字符，没有在下载时处理，正好保留原始数据。
import re

pattern = re.compile(r'[\u4e00-\u9fa5]+')
data = pattern.findall(comment)
filted_comment = ''.join(data)

# 分词
import jieba

word = jieba.lcut(filted_comment)


# 整理
import pandas as pd

words_df = pd.DataFrame({'words': word})

#停词相关设置。参数 quoting=3 全不引用
stopwords = pd.read_csv(
        "stopwords.txt",
        index_col=False,
        quoting=3,
        sep="\t",
        names=['stopword'],
        encoding='utf-8'
        )
words_df = words_df[~words_df.words.isin(stopwords.stopword)]

# 聚合
words_stat = words_df.groupby('words')['words'].agg({'size'})
words_stat = words_stat.reset_index().sort_values("size", ascending=False)


# 词云设置
from wordcloud import WordCloud
import matplotlib.pyplot as plt
import numpy as np

def rgb2gray(rgb):
    return np.dot(rgb[...,:3], [0.299, 0.587, 0.114])

def gray2bw(gray):
    for raw in range(len(gray)):
        for col in range(len(gray[raw])):
            gray[raw][col] = (0 if gray[raw][col]>50 else 255)
    return gray

img = plt.imread('4.jpg')
mask = rgb2gray(img)
bw = gray2bw(mask)

wordcloud = WordCloud(
                font_path="YaHei Consolas Hybrid.ttf",
                background_color="white",
                mask=bw,
                max_font_size=80
                )
# word_frequence 为字典类型，可以直接传入wordcloud.fit_words()
word_frequence = {
        x[0]:x[1] for x in words_stat.head(1000).values
        }
wordcloud = wordcloud.fit_words(word_frequence)

# 存储显示
plt.imsave('img.jpg', wordcloud)

plt.subplot(131)
plt.imshow(img)
plt.axis("off")
plt.subplot(132)
plt.imshow(bw)
plt.axis("off")
plt.subplot(133)
plt.imshow(wordcloud, interpolation='bilinear')
plt.axis("off")
```

# 结果文件

使用的掩膜原图片：

秘密森林剧照

![秘密森林剧照](http://upload-images.jianshu.io/upload_images/2817465-4fed401bd994439e?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

输出图片

![输出图片](http://upload-images.jianshu.io/upload_images/2817465-b5570779fa57b746?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

IDE输出结果

![这里写图片描述](http://upload-images.jianshu.io/upload_images/2817465-4eec39379b930ad6?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 停词文件

[stopwords.txt](http://download.csdn.net/download/p_lart/9940528)

---

```markdown
Parameters
----------
font_path : string
    Font path to the font that will be used (OTF or TTF).
    Defaults to DroidSansMono path on a Linux machine. If you are on
    another OS or don't have this font, you need to adjust this path.

width : int (default=400)
    Width of the canvas.

height : int (default=200)
    Height of the canvas.

prefer_horizontal : float (default=0.90)
    The ratio of times to try horizontal fitting as opposed to vertical.
    If prefer_horizontal < 1, the algorithm will try rotating the word
    if it doesn't fit. (There is currently no built-in way to get only
    vertical words.)

mask : nd-array or None (default=None)
    If not None, gives a binary mask on where to draw words. If mask is not
    None, width and height will be ignored and the shape of mask will be
    used instead. All white (#FF or #FFFFFF) entries will be considerd
    "masked out" while other entries will be free to draw on. [This
    changed in the most recent version!]

scale : float (default=1)
    Scaling between computation and drawing. For large word-cloud images,
    using scale instead of larger canvas size is significantly faster, but
    might lead to a coarser fit for the words.

min_font_size : int (default=4)
    Smallest font size to use. Will stop when there is no more room in this
    size.

font_step : int (default=1)
    Step size for the font. font_step > 1 might speed up computation but
    give a worse fit.

max_words : number (default=200)
    The maximum number of words.

stopwords : set of strings or None
    The words that will be eliminated. If None, the build-in STOPWORDS
    list will be used.

background_color : color value (default="black")
    Background color for the word cloud image.

max_font_size : int or None (default=None)
    Maximum font size for the largest word. If None, height of the image is
    used.

mode : string (default="RGB")
    Transparent background will be generated when mode is "RGBA" and
    background_color is None.

relative_scaling : float (default=.5)
    Importance of relative word frequencies for font-size.  With
    relative_scaling=0, only word-ranks are considered.  With
    relative_scaling=1, a word that is twice as frequent will have twice
    the size.  If you want to consider the word frequencies and not only
    their rank, relative_scaling around .5 often looks good.

    .. versionchanged: 2.0
        Default is now 0.5.

color_func : callable, default=None
    Callable with parameters word, font_size, position, orientation,
    font_path, random_state that returns a PIL color for each word.
    Overwrites "colormap".
    See colormap for specifying a matplotlib colormap instead.

regexp : string or None (optional)
    Regular expression to split the input text into tokens in process_text.
    If None is specified, ``r"\w[\w']+"`` is used.

collocations : bool, default=True
    Whether to include collocations (bigrams) of two words.

    .. versionadded: 2.0

colormap : string or matplotlib colormap, default="viridis"
    Matplotlib colormap to randomly draw colors from for each word.
    Ignored if "color_func" is specified.

    .. versionadded: 2.0

normalize_plurals : bool, default=True
    Whether to remove trailing 's' from words. If True and a word
    appears with and without a trailing 's', the one with trailing 's'
    is removed and its counts are added to the version without
    trailing 's' -- unless the word ends with 'ss'.
```

