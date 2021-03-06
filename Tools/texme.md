# texme

仓库连接: https://github.com/susam/texme

## 简单使用

可以直接在md格式的html文件中插入一个脚本链接，提供渲染支持。

这里是几个例子，前两个是最简单的。为了避免冲突，左角号写作【，右角号写作】

```html
【!DOCTYPE html】【script src="https://cdn.jsdelivr.net/npm/texme@0.6.0"】【/script】【textarea】

# Euler's Identity

In mathematics, **Euler's identity** is the equality
$$ e^{i \pi} + 1 = 0. $$

## Explanation

Euler's identity is a special case of Euler's formula from complex
analysis, which states that for any real number $ x $,
$$ e^{ix} = \cos x + i \sin x. $$
```

```html
# Euler's Identity

In mathematics, **Euler's identity** is the equality
$$ e^{i \pi} + 1 = 0. $$

## Explanation

Euler's identity is a special case of Euler's formula from complex
analysis, which states that for any real number $ x $,
$$ e^{ix} = \cos x + i \sin x. $$

【script src="https://cdn.jsdelivr.net/npm/texme@0.6.0"】【/script】
```
  
In mathematics, **Euler's identity** is the equality
$$ e^{i \pi} + 1 = 0. $$

Euler's identity is a special case of Euler's formula from complex
analysis, which states that for any real number $ x $,
$$ e^{ix} = \cos x + i \sin x. $$

## 关闭数学公式支持

可以使用Fork项目——mdme来渲染

```html
【!DOCTYPE html】
【script src="https://cdn.jsdelivr.net/npm/mdme"】【/script】【textarea】

# Atomic Theory

**Atomic theory** is a scientific theory of the nature of matter, which
states that matter is composed of discrete units called *atoms*. It
began as a philosophical concept in ancient Greece and entered the
scientific mainstream in the early 19th century when discoveries in the
field of chemistry showed that matter did indeed behave as if it were
made up of atoms.
```

或者使用`texme`的附加支持

可见文档：https://github.com/susam/texme#render-markdown-without-mathjax
