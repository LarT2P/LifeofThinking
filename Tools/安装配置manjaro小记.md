---
title: Manjaro小记
date: 2018-05-24 00:03:55
categories: 
    - 记录与总结
tags: 
    - Linux
---

# 又是一次新的尝试

> 写下这篇文章的时间：2018年5月23日22:46:25

## 使用的工具

- manjaro-kde-17.1.7-stable-x86_64.iso
- rufus-2.18.exe
- AUSU X555L Core i3-5010U CPU @ 2.10GHz 2.10GHz RAM 4.00GB 
- Win8.1 X64
- 一个容量合适的U盘

## 安装

使用 rufus 来制作启动U盘。BIOS设置U盘启动。

由于直接使用ISO模式，我这里出现了非法错误的提示，转而使用DD模式，成功进入安装设置界面。

界面很清晰，关键是驱动设定，选择闭源 —— non-free。

选择中文。

点击 boot 那项启动安装流程。

进入新的图形界面后，开始安装。

流程主要参看 [Manjaro Linux 17.0.2 KDE环境安装、配置记录](https://blog.csdn.net/aaazz47/article/details/77416136?locationNum=6&fps=1)。

问题是，我在手动分区的时候，划分了 /boot/efi（这里文件系统我是用的是fat32，安装后win下会出现一个新的盘符，在磁盘管理中，我将其隐藏了），/，/var，但是安装过程会提示出错。

这里具体的错误已经忘记，但是多番尝试后，修改了挂载点，把 /var 改成了 交换分区。诶，这次竟然装上去了。

## 配置

初次进入系统后，首先要配置好源的问题。

用vi（或者KDE自带编辑器Kate，当然命令行里的Nano也不错）编辑/etc/pacman.conf文件(sudo vi /etc/pacman.conf)，在文件底部添加下面几行:

```
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

再执行来调整源顺序，[使用国内源](https://blog.csdn.net/kxp9545/article/details/76138980)。并更新系统，估计会有一个多G的内容要更新，等着吧。

```bash
sudo pacman-mirrors -c China -g
# 这里我直接使用了KDE的更新管理器，就是托盘里那个小妖精...简单直接
sudo pacman -Syyu
```

关于 pacman 命令可见以下文章：

https://wiki.archlinux.org/index.php/Pacman

https://www.cnblogs.com/shengansong/archive/2011/11/19/2255129.html

之后就是常用软件的安装了。
 
1. 终端使用vim（个人喜好，用了自己的配置文件。需要修改一些路径，字体就不用管，终端使用就这好处。）
2. 图形使用emacs —— spacemacs（使用develop版本，参考配置可见子龙山人的）。
3. 输入法使用fcitx-googlepinyin，我这里安装搜狗总是有问题，直接换了谷歌拼音。

```bash
sudo pacman -S gvim
sudo pacman -S emacs
sudo pacman -S git
# 关于fcitx可见 https://wiki.archlinux.org/index.php/Fcitx
sudo pacman -S fcitx-googlepinyin
sudo pacman -S fcitx-im         # 全部安装
sudo pacman -S fcitx-configtool # 图形化配置工具
sudo pacman -S kcm-fcitx # KDE 的 Fcitx 输入法模块，似乎是可选的

# 设置中文输入法环境变量，相当于开启启动输入法，编辑~/.xprofile文件，增加下面几行(如果文件不存在，则新建)
# 可以通过 
# echo ~
# 或者
# echo $HOME
# 来查看对应的位置

exportGTK_IM_MODULE=fcitx
exportQT_IM_MODULE=fcitx
exportXMODIFIERS="@im=fcitx"
```

如上安装后，gvim和emacs基本没有什么问题，但是输入法有些小问题，多次尝试后，就是输入字符出来的候选菜单里面不能显示文字，当我使用了 `fcitx-ui-light` 这个轻量级UI，发现正常了。（可在配置菜单里的高级中配置）。

## 没完没了

KDE启动内存占用将近五百兆，虽然比win要省一些，但是还是想尝试点新的更给力的环境。于是，我瞄上了 [i3wm](https://i3wm.org/)，一个平铺式窗口管理器。

```bash
sudo pacman -S i3
```

注销用户，登录界面的 `会话` 选择 `i3` 。

进来的时候，发现只有屏幕最下方的显示有变化，变成了一条看似酷炫缺什么也做不了的状态栏。

不要慌，要知道i3主要是使用快捷键。

所以刚进的时候，会问你要不要使用默认配置。还是自己创建配置吧。会问你使用哪个作为主要的功能键，mod键设定为windows键。

这时需要 `win+return` 打开终端。

可以使用这个较为完整的配置：

https://github.com/ID1258/oh-my-i3

注意，里面的安装最后，要运行脚本，这里需要这样执行，详见 https://github.com/ID1258/oh-my-i3/issues/2 ：

```bash
git checkout HEAD^
./install.sh
```
注意安装依赖：

    调用conky作为i3bar输出
    dmenu启动程序
    feh显示壁纸
    xcompmgr透明插件（可选）
    infinality字体渲染美化（可选）

关于配置，网上有很多，但是都需要仔细调教，可以仔细阅读i3官网手册，慢慢摸索。

## 相关参考

[我的vim配置](https://github.com/lartpang/.vim_runtime)
[spacemacs](https://github.com/syl20bnr/spacemacs#windows)
[Manjaro安装与配置](https://blog.csdn.net/lingsuifenfei123/article/details/79155369)