---
title: python高级技巧之生成器与装饰器
date: 2018-07-22
categories: 
    - 编程与生活
tags:
    - python 
    - 进阶
---

python中有很多高级的技巧，这里记录下生成器与装饰器。

<!-- more -->

> 链接 [Python高级编程技巧](http://python.jobbole.com/61171/)
> 连接 [廖雪峰](https://www.liaoxuefeng.com)

# 推导式(Comprehensions) #

列表推导(list comprehensions)。这是一种将for循环、if表达式以及赋值语句放到单一语句中的一种方法。换句话说，你能够通过一个表达式对一个列表做映射或过滤操作。

一个列表推导式包含以下几个部分：

* 一个输入序列
* 一个表示输入序列成员的变量
* 一个可选的断言表达式
* 一个将输入序列中满足断言表达式的成员变换成输出列表成员的输出表达式

```python
# 原始
num = [1, 4, -5, 10, -7, 2, 3, -1]
filtered_and_squared = []

for number in num:
    if number > 0:
        filtered_and_squared.append(number ** 2)
print filtered_and_squared

# [1, 16, 100, 4, 9]

# lambda函数
num = [1, 4, -5, 10, -7, 2, 3, -1]
filtered_and_squared = map(lambda x: x ** 2, filter(lambda x: x > 0, num))
print filtered_and_squared
 
# [1, 16, 100, 4, 9]
```
上面都不是很好的方案，第一个虽然直观，但是代码较多，还有不必要的`append`，而第二个相当于就是将代码在水平上展开。

考虑列表推导的技巧。

## 列表生成式 ##

常见的形式

```python
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]

>>> [m + n for m in 'ABC' for n in 'XYZ']
['AX', 'AY', 'AZ', 'BX', 'BY', 'BZ', 'CX', 'CY', 'CZ']
# 三层和三层以上的循环就很少用到了。

>>> d = {'x': 'A', 'y': 'B', 'z': 'C' }
>>> [k + '=' + v for k, v in d.items()]
['y=B', 'x=A', 'z=C']
```

重回上面的问题

```python
# 列表推导
num = [1, 4, -5, 10, -7, 2, 3, -1]
filtered_and_squared = [ x**2 for x in num if x > 0]
print filtered_and_squared

# [1, 16, 100, 4, 9]
```

![列表推导](http://jbcdn2.b0.upaiyun.com/2014/02/8db6c2a8d716d7788d0e526c921cc504.jpg)

* 迭代器(iterator)遍历输入序列num的每个成员x
* 断言式判断每个成员是否大于零
* 如果成员大于零，则被交给输出表达式，平方之后成为输出列表的成员。

列表推导式被封装在一个列表中，所以很明显它能够立即生成一个新列表。这里只有一个type函数调用而没有隐式调用lambda函数，列表推导式正是使用了一个常规的迭代器、一个表达式和一个if表达式来控制可选的参数。

### 存在的问题

那就是整个列表必须一次性加载于内存之中，这对上面举的例子而言不是问题，甚至扩大若干倍之后也都不是问题。但是总会达到极限，内存总会被用完。

针对上面的问题，生成器(Generator)能够很好的解决。**生成器表达式不会一次将整个列表加载到内存之中，而是生成一个生成器对象(Generator objector)，所以一次只加载一个列表元素。**

## 生成器

生成器表达式同列表推导式有着几乎相同的语法结构，区别在于生成器表达式是被圆括号包围，而不是方括号

```python
# 初始
num = [1, 4, -5, 10, -7, 2, 3, -1]
filtered_and_squared = ( x**2 for x in num if x > 0 )
print filtered_and_squared
 
# <generator object <genexpr> at 0x00583E18>
 
for item in filtered_and_squared:
    print item
 
# 1, 16, 100 4,9
# 
# 改进
num = [1, 4, -5, 10, -7, 2, 3, -1]
 
def square_generator(optional_parameter):
    return (x ** 2 for x in num if x > optional_parameter)
 
print square_generator(0)
# <generator object <genexpr> at 0x004E6418>
 
# Option I
for k in square_generator(0):
    print k
# 1, 16, 100, 4, 9
 
# Option II
g = list(square_generator(0))
print g
# [1, 16, 100, 4, 9]
```

除非特殊的原因，应该经常在代码中使用生成器表达式。但除非是面对非常大的列表，否则是不会看出明显区别的。

## 多元素处理

```python
alist = ['a1', 'a2', 'a3']
blist = ['1', '2', '3']
 
for a, b in zip(alist, blist):
    print a, b
```

## yield

这就是定义generator的另一种方法。如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator。

fib函数实际上是定义了斐波拉契数列的推算规则，可以从第一个元素开始，推算出后续任意的元素，这种逻辑其实非常类似generator。

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'

>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```

> a, b = b, a + b 表示 (a, b) = (b, a+b)
> 更易理解可见下

```python
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        (a, b) = (b, a+b)
        n = n + 1
    return 'done'

a = list(fib(3))
print a
```

这里，最难理解的就是generator和函数的执行流程不一样。函数是顺序执行，遇到return语句或者最后一行函数语句就返回。**而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。**

```python
def odd():
    print('step 1')
    yield 1
    print('step 2')
    yield(3)
    print('step 3')
    yield(5)

>>> o = odd()
>>> next(o)
step 1
1
>>> next(o)
step 2
3
>>> next(o)
step 3
5
>>> next(o)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```

可以看到，odd不是普通函数，而是generator，在执行过程中，遇到yield就中断，下次又继续执行。执行3次yield后，已经没有yield可以执行了，所以，第4次调用next(o)就报错。

把函数改成generator后，我们基本上从来不会用next()来获取下一个返回值，而是直接使用for循环来迭代：

```python
>>> for n in fib(6):
...     print(n)
```

但是用for循环调用generator时，发现拿不到generator的return语句的返回值。如果想要拿到返回值，必须捕获StopIteration错误，返回值包含在StopIteration的value中。

```python
g = fib(6)
while True:
    try:
        x = next(g)
        print('g:', x)
    except StopIteration as e:
        print('Generator return value:', e.value)
        break

# g: 1
# g: 1
# g: 2
# g: 3
# g: 5
# g: 8
# Generator return value: done
```

> 可以直接作用于for循环的数据类型有以下几种：
> 
> 一类是集合数据类型，如list、tuple、dict、set、str等；
> 
> 一类是generator，包括生成器和带yield的generator function。
> 
> 这些可以直接作用于for循环的对象统称为可迭代对象：Iterable。
> 
> 而生成器不但可以作用于for循环，还可以被next()函数不断调用并返回下一个值，直到最后抛出StopIteration错误表示无法继续返回下一个值了。可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。
> 
> 生成器都是Iterator对象，但list、dict、str虽然是Iterable，却不是Iterator。
> 
> 你可能会问，为什么list、dict、str等数据类型不是Iterator？
> 
> 这是因为Python的Iterator对象表示的是一个数据流，Iterator对象可以被next()函数调用并不断返回下一个数据，直到没有数据时抛出StopIteration错误。可以把这个数据流看做是一个有序序列，但我们却不能提前知道序列的长度，只能不断通过next()函数实现按需计算下一个数据，所以Iterator的计算是惰性的，只有在需要返回下一个数据时它才会计算。
> 
> Iterator甚至可以表示一个无限大的数据流，例如全体自然数。而使用list是永远不可能存储全体自然数的。

# 装饰器

装饰器为我们提供了一个增加已有函数或类的功能的有效方法。

装饰器是一个包装了另一个函数的特殊函数：主函数被调用，并且其 *返回值* 将会被传给装饰器，接下来装饰器将返回一个 *包装了主函数的替代函数*，程序的其他部分看到的将是这个包装函数。

本质上，decorator就是一个返回函数的高阶函数。所以，我们要定义一个能打印日志的decorator，可以定义如下：

```python
def log(func):
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

观察上面的log，因为它是一个decorator，所以接受一个函数作为参数，并返回一个函数。我们要借助Python的@语法，把decorator置于函数的定义处：

```python
@log
def now():
    print('2015-3-25')
```
调用now()函数，不仅会运行now()函数本身，还会在运行now()函数前打印一行日志：

```python
>>> now()
call now():
2015-3-25
```
把@log放到now()函数的定义处，相当于执行了语句：

```python
now = log(now)
```

由于log()是一个decorator，返回一个函数，所以，原来的now()函数仍然存在，只是现在 **同名的now变量指向了新的函数** ，于是 **调用now()将执行新函数**，即在 *log()函数中返回的wrapper()函数*。

wrapper()函数的参数定义是(*args, **kw)，因此，wrapper()函数可以接受任意参数的调用。在wrapper()函数内，首先打印日志，再紧接着调用原始函数(这里是在`return func(*args, **kw)`中实现的)。

要注意，上面的装饰器形式上没有使用参数，但是实际上有一个函数对象参数。如果decorator本身需要传入参数，那就需要编写一个返回decorator的高阶函数，写出来会更复杂。比如，要自定义log的文本：

```python
def log(text):
    def decorator(func):
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```

这个3层嵌套的decorator用法如下：

```python
@log('execute')
def now():
    print('2015-3-25')
```

执行结果如下：

```python
>>> now()
execute now():
2015-3-25
```

和两层嵌套的decorator相比，3层嵌套的效果是这样的：

```python
>>> now = log('execute')(now)
```

我们来剖析上面的语句，首先执行log('execute')，返回的是decorator函数，再调用返回的函数，参数是now函数，返回值最终是wrapper函数。

以上两种decorator的定义都没有问题，但还差最后一步。因为我们讲了函数也是对象，它有__name__等属性，但你去看经过decorator装饰之后的函数，它们的__name__已经从原来的'now'变成了'wrapper'：

```python
>>> now.__name__
'wrapper'
```

因为返回的那个wrapper()函数名字就是'wrapper'，所以，需要把原始函数的__name__等属性复制到wrapper()函数中，否则，有些依赖函数签名的代码执行就会出错。

**不需要编写wrapper.__name__ = func.__name__这样的代码，Python内置的functools.wraps就是干这个事的**，所以，一个完整的decorator的写法如下：

```python
import functools

def log(func):
    @functools.wraps(func)
    def wrapper(*args, **kw):
        print('call %s():' % func.__name__)
        return func(*args, **kw)
    return wrapper
```

或者针对带参数的decorator：

```python
import functools

def log(text):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kw):
            print('%s %s():' % (text, func.__name__))
            return func(*args, **kw)
        return wrapper
    return decorator
```