---
title: 纪念我的第一个完整的小说爬虫
date: 2017-08-13 21:42:10
categories: 
    - 编程与生活
tags: 
    - python
---

纪念我的第一个爬虫程序，一共写了三个白天，其中有两个上午没有看，中途遇到了各种奇怪的问题，伴随着他们的解决，对于一些基本的操作也弄清楚了。果然，对于这些东西的最号的学习方式，就是在使用中学习，通过解决问题的方式来搞定这些知识。按需索取，才能更有针对性。

大体记录下整个过程。

<!--more-->

# 准备构思

出于对于python的热爱，想要尝试一些练手的项目，但是不论是看书，还是直接尝试别人的项目，到最后都会沦为不停地复制粘贴...最实际的就是自己来上手亲自写代码。思路都是一样的，但是具体的实现还得靠自己。

以前的复制粘贴给我的帮助也就是告诉了我大致的流程。

## 确定目标网址

目标网址是关键。我梦想中的爬虫是那种偏向于更智能的，直接给他一个想要获取的关键词，一步步的流程直接自己完成，可以自己给定范围，也可以直接爬取整个互联网或者更实际的就是整个百度上的内容，但是，目前就我而言，见到的爬虫，都是给定目标网址，通过目标页面上的内容进一步执行规定的操作，所以现在来看，我们在写爬虫之前，需要确定一个基准页面，这个是需要我们事先制定的。在考虑我们需要程序完成怎样的功能，获取页面文本还是相关链接内容还是其他的目的。

我这个程序想要获取的是《剑来》小说，把各个章节的内容爬去下载存储到文件里。

编程只是实现目的的工具。

所以重点是分析我们的需求。

获取小说目录页面是基本。这里有各个章节的链接，标题等等内容。这是我们需要的。

有了各个章节的链接，就需要进入其中获得各个章节的内容。

所以，我们需要获得页面内容，需要从中获得目标内容。

所以使用 `urllib.request`，`re` 库。

前者用来获得网页内容，后者获得目标信息。

## headers

直接使用`urllib.request`的`urlopen()`，`read()`方法是会报类似以下的错误（这里是网上查找过来的，都是类似的）：

    raise HTTPError(req.get_full_url(), code, msg, hdrs, fp)
    HTTPError: HTTP Error 403: Forbidden

出现`urllib2.HTTPError: HTTP Error 403: Forbidden`错误是由于网站禁止爬虫，可以在请求加上头信息，伪装成浏览器。

    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0'}
    request = url_req.Request(url, headers=headers)
    response = url_req.urlopen(request, data=None, timeout=3)
    html = response.read().decode('GBK')

**注意**：这里存在两个容易出问题的地方。

* 编码：编码问题是使用爬虫中有时候会很头痛的问题，由于网页源代码编码格式不明确，所以这里尝试了许久。

使用`chardet`库的`detect()`方法可以检测字节字符串的编码。所以直接检测这里的html(先不要解码)。输出的是`GB2312`，但是在后面页面的爬取中，会出现提示有的字符的编码异常，所以这里采取了比其范围更广的中文字符集`GBK`，解决了这个问题。

* 设置超时范围：由于频繁的获取网页内容，目标网站有时候会出现没有响应的问题。

（这个问题可以见我在CSDN上的提问：[关于python爬虫程序中途停止的问题](http://bbs.csdn.net/topics/392215006)）

于是我采取了捕获 `urlopen()`的`socket.timeout`异常，并在出现异常的时候再循环访问，直到获得目标页面。

## 获得目标内容

这里使用的是正则表达式。`re`模块。这里的使用并不复杂。

首先需要一个模式字符串。以`re.I`指定忽略大小写，编译后的对象拥有本身匹配的方法，这里使用的是`findall()`，返回一个所有结果组成的列表。可以及时返回输出其内容，进而选择合适的部分进行处理。

[python 正则表达式](http://www.runoob.com/python/python-reg-expressions.html)

通过查看相关的符号，这里使用`(.+?)`来实现匹配非贪婪模式(尽量少的)下任意无限字符，对之使用`()`，进而匹配括号内的模式。

## 文件写入

使用`with open() as file:`，进而可以处理文件。并且可以自动执行打开和关闭文件，更为便捷安全。

    with open(findall_title[0] + '.txt', 'w+', encoding='utf-8') as open_file:

* 这里也要注意编码的问题，指定`utf-8`。会避免一些问题。
* 这里使用`w+`模式，追加写文件。

# 完整代码

```python
# -*- coding: utf-8 -*-
"""
Created on Fri Aug 11 16:31:42 2017
@author: lart
"""

import urllib.request as url_req
import re, socket, time


def r_o_html(url):
    print('r_o_html begin')

    headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0'}

    request = url_req.Request(url, headers=headers)

    NET_STATUS = False
    while not NET_STATUS:
        try:
            response = url_req.urlopen(request, data=None, timeout=3)
            html = response.read().decode('GBK')
            print('NET_STATUS is good')
            print('r_o_html end')
            return html
        except socket.timeout:
            print('NET_STATUS is not good')
            NET_STATUS = False

def re_findall(re_string, operation, html):

    print('re_findall begin')
    pattern = re.compile(re_string, re.I)

    if operation == 'findall':
        result = pattern.findall(html)
    else:
        print('this operation is invalid')
        exit(-1)

    print('re_findall end')
    return result


if __name__ == '__main__':
    url_base = 'http://www.7kankan.la/book/1/'

    html = r_o_html(url_base)

    findall_title = re_findall(r'<title>(.+?)</title>', 'findall', html)

    findall_chapter = re_findall(r'<dd class="col-md-3"><a href=[\',"](.+?)[\',"] title=[\',"](.+?)[\',"]>', 'findall', html)

    with open(findall_title[0] + '.txt', 'w+', encoding='utf-8') as open_file:
        print('article文件打开', findall_chapter)
        for i in range(len(findall_chapter)):
            print('第' + str(i) + '章')

            open_file.write('\n\n\t' + findall_chapter[i][1] + '\n --------------------------------------------------------------------- \n')

            url_chapter = url_base + findall_chapter[i][0]

            html_chapter = r_o_html(url_chapter)

            findall_article = re_findall(r'&nbsp;&nbsp;&nbsp;&nbsp;(.+?)<br />', 'findall', html_chapter)

            findall_article_next = findall_chapter[i][0].replace('.html', '_2.html')

            url_nextchapter = url_base + findall_article_next

            html_nextchapter = r_o_html(url_nextchapter)

            if html_nextchapter:
                findall_article.extend(re_findall(r'&nbsp;&nbsp;&nbsp;&nbsp;(.+?)<br />', 'findall', html_nextchapter))

                for text in findall_article:
                    open_file.write(text + '\n')

            time.sleep(1)

    print('文件写入完毕')

```