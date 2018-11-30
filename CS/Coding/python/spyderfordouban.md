---
title: 我的第一个豆瓣短评爬虫
date: 2017-08-17 17:19:22
categories: 
    - 编程与生活
tags: 
    - python
---

豆瓣上有着大量的影视剧的评论，所以说，要是想要实现对广大人民群众的观点的分析，对一部片子的理解，综合来看大家的评论是很有必要的。而短评作为短小精干的快速评论入口，是值得一谈的。

所以先要实现对其的数据的爬取。

目前来看，基本内容是可以爬取的。最大的问题在于速度。后续考虑准备运用多线程的方式处理下。以及可以尝试其他提速的方法。

下面是这个程序的构思编写过程。

<!--more-->

# 构思准备

## 爬取的思路，及反省与思考

### 盲目状态

最初，并不知道豆瓣对于未登陆用户的限制，盲目的爬取，看着评论文件，发现行数太少，也就是说评论内容太少，感觉不对劲。

我利用了即时打印写入内容的方式，发现，到了第十页左右的时候，出现无法获得页面内评论内容，思考了下后，试着将页面源代码打印出来，发现到了后面，就出现提示**权限不足**。我一下子知道了，是因为没有登录的原因。

### 登录

之前看过内容，明白这时候应该借助`cookie`的方式了。

但是又要处理验证码。而且，似乎初次登陆的时候并不需要验证码。为了方便，下面直接使用了存在验证码的方式。

由于开始不了解，不知道应该提交哪些信息，多方查找后，终于明白，就是在登录页面登陆后，打开浏览器的*开发者工具*里，查看里面的*网络*，注意关注里面的*方法*一列中的`post`所在行那项。在登陆点击后，随着页面的跳转，会出现一个`post`页面，点击后查看其参数，若是火狐的话有个专门的参数窗口，其中就有要提交的参数了。包括用户信息，还有登录跳转页面（redir）等等。

在最初，我直接将`https://accounts.douban.com/login`选作登录地址，当然也将从其登陆的信息复制了出来，但是发现登录到`redir`还可以，要是用`opener.open()`再登录` 'https://movie.douban.com/subject/26934346/comments?start=' + str(start) + '&limit=20&sort=new_score&status=P'` 这里构造的页面是登不上的。这里我也是测试了好久才发现的问题。具体原因我不清楚。可能是有哪些知识我是遗漏了的。

后来觉察到这一点后，我尝试使用现在的信息登录，如下。

```python
main_url = 'https://accounts.douban.com/login?source=movie'
formdata = {
    "form_email":"你的邮箱",
    "form_password":"你的密码",
    "source":"movie",
    "redir":"https://movie.douban.com/subject/26934346/",
    "login":"登录"
}
user_agent = r'Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.94 Safari/537.36'
headers = {'User-Agnet': user_agent, 'Connection': 'keep-alive'}
```

诶，竟然登上去了！

### 关于验证码的纠结思考

由于豆瓣的验证码是登一次变一次的。所以在发生上面的问题时，在没有找到正确的处理办法之时，我怀疑是因为验证码，在我获取上一次验证码图片，并将验证信息输入到`formdata`这个提交信息的字典中后，再次使用`request.Request(url=main_url, data=logingpostdata, headers=headers)`与`opener.open(req_ligin).read().decode('utf-8')`的时候，会不会验证码发生了变化？而我提交的是刚才的验证码？

又开始查资料，后来终于明白，这里提交的信息中指定了验证码的图片的`captchaID`，这样使得提交信息时候，返回来的验证码图片也就是这个，图片id是唯一的，当你自己修改了提交内容，豆瓣也会使用你提供的这个id来获取服务器里的验证码图片，所以保证了图片的一致。（这是我的理解，觉得有问题，或者更精确的理解的，欢迎留言）

## 使用库的考虑

### BeautifulSoup， re

准备尝试下`beautiful soup`这个库，对于拆解html页面很便利。但是在实践中，还是可能会用到正则表达式`re`模块。可见，正则表达式还是很重要的。掌握基本可以查表使用时必须的。

在代码中可以看出，我对于该库的使用还是有些粗，不巧妙，还有待加强。

不过，今天尝试了下，用CSS选择器还是很方便的。`select()`方法，很方便，可以参考从浏览器开发者工具里选择元素对应的CSS选择器，很直接。

### urllib.request，http.cookiejar

借鉴之前使用`urllib.request`的经验，尝试直接使用`urllib.request.Request(url, headers=headers)`与`urllib.request.urlopen(request, data=None, timeout=3)`发现，豆瓣的短评，最一开始还好，但是在爬取将近十多页的短评时，会报出`Forbidden`的异常，查询后得知，应该是豆瓣对于游客用户的限制，需要登录才可以。参考网上一些其他教程，可以使用设置`cookie`的方法来处理。

使用了`cookiejar.CookieJar()`声明对象，来保存`cookie`到了变量中。利用的`request.HTTPCookieProcessor()`来创建cookie处理器。利用`request.build_opener()`构造了一个 `opener`，后面利用`opener.open()`来打开直接网页或者处理请求。

### socket

类似上一个爬虫里的设置，这里直接使用了全局的超时设定。

```python
import socket
timeout = 3
socket.setdefaulttimeout(timeout)
```

限制三秒，超出就抛出`socket.timeout`异常。捕获后重新连接。

```python
# 超时重连
state = False
while not state:
    try:
        html = opener.open(url).read().decode('utf-8')
        state = True
    except socket.timeout:
        state = False
```

### time

为了防止爬取过快，设置了循环的延时。

```python
for ...:
    ...
    time.sleep(3)
```

# 完整代码

```python
# -*- coding: utf-8 -*-
"""
Created on Thu Aug 17 16:31:35 2017
@note: 为了便于阅读，将模块的引用就近安置了
@author: lart
"""

# 用于生成短评页面网址的函数
def MakeUrl(start):
    """make the next page's url"""
    url = 'https://movie.douban.com/subject/26934346/comments?start=' + str(start) + '&limit=20&sort=new_score&status=P'
    return url


# 登录页面信息
main_url = 'https://accounts.douban.com/login?source=movie'
formdata = {
    "form_email":"你的邮箱",
    "form_password":"你的密码",
    "source":"movie",
    "redir":"https://movie.douban.com/subject/26934346/",
    "login":"登录"
}
user_agent = r'Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.94 Safari/537.36'
headers = {'User-Agnet': user_agent, 'Connection': 'keep-alive'}


# 保存cookies便于后续页面的保持登陆
from urllib import request
from http import cookiejar

cookie = cookiejar.CookieJar()
cookie_support = request.HTTPCookieProcessor(cookie)
opener = request.build_opener(cookie_support)


# 编码信息，生成请求，打开页面获取内容
from urllib import parse

logingpostdata = parse.urlencode(formdata).encode('utf-8')
req_ligin = request.Request(url=main_url, data=logingpostdata, headers=headers)
response_login = opener.open(req_ligin).read().decode('utf-8')


# 获取验证码图片地址
from bs4 import BeautifulSoup

try:
    soup = BeautifulSoup(response_login, "html.parser")
    captchaAddr = soup.find('img', id='captcha_image')['src']

    # 匹配验证码id
    import re

    reCaptchaID = r'<input type="hidden" name="captcha-id" value="(.*?)"/'
    captchaID = re.findall(reCaptchaID, response_login)

    # 下载验证码图片
    request.urlretrieve(captchaAddr, "captcha.jpg")

    # 输入验证码并加入提交信息中，重新编码提交获得页面内容
    captcha = input('please input the captcha:')
    formdata['captcha-solution'] = captcha
    formdata['captcha-id'] = captchaID[0]
    logingpostdata = parse.urlencode(formdata).encode('utf-8')
    req_ligin = request.Request(url=main_url, data=logingpostdata, headers=headers)
    response_login = opener.open(req_ligin).read().decode('utf-8')

finally:
    # 获得页面评论文字
    soup = BeautifulSoup(response_login, "html.parser")
    totalnum = soup.select("div.mod-hd h2 span a")[0].get_text()[3:-2]

    # 计算出页数和最后一页的评论数
    pagenum = int(totalnum) // 20
    commentnum = int(totalnum) % 20
    print(pagenum, commentnum)

    # 设置等待时间，避免爬取太快
    import time
    # 用于在超时的时候抛出异常，便于捕获重连
    import socket

    timeout = 3
    socket.setdefaulttimeout(timeout)

    # 追加写文件的方式打开文件
    with open('秘密森林的短评.txt', 'w+', encoding='utf-8') as file:
        # 循环爬取内容
        for item in range(pagenum):
            print('第' + str(item) + '页')
            start = item * 20
            url = MakeUrl(start)

            # 超时重连
            state = False
            while not state:
                try:
                    html = opener.open(url).read().decode('utf-8')
                    state = True
                except socket.timeout:
                    state = False

            # 获得评论内容
            soup = BeautifulSoup(html, "html.parser")
            comments = soup.select("div.comment > p")
            for text in comments:
                file.write(text.get_text().split()[0] + '\n')
                print(text.get_text())
                limit_num = 0
                if item == pagenum - 1:
                    limit_num =+ 1
                    if limit_num == commentnum:
                        break
            time.sleep(3)

    print('采集写入完毕')
```

# 结果

![](http://upload-images.jianshu.io/upload_images/2817465-4d950a3b3b84b2ec?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可见至少也有9200条评论，不管是否有所遗漏，基数已经基本无差。

# 后续处理

数据已经到手，怎样玩耍就看自己的想法了，近期看到了一篇文章,讲了利用词频制作词云，生成图片，有点意思，决定模仿试试。

[Python 爬虫实践：《战狼2》豆瓣影评分析](http://mp.weixin.qq.com/s/nVofu75tjPfpGavTGLmoTw)

[github:amueller/word_cloud](https://github.com/amueller/word_cloud/)
