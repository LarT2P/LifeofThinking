---
title: Git使用
date: 2017-08-10 10:20:37
categories: 
    - 编程与生活
tags: 
    - 工具
---

# 初始设置本地Git

首先来设置使用 Git 时的姓名和邮箱地址。名字请用英文输入。

    $ git config --global user.name "Firstname Lastname"
    $ git config --global user.email "your_email@example.com"

这个命令，会在“~/.gitconfig”中以如下形式输出设置文件。

    [user]
      name = Firstname Lastname
      email = your_email@example.com

想更改这些信息时，可以直接编辑这个设置文件。这里设置的姓名和邮箱地址会用在 Git 的 *提交日志*中。由于在 GitHub 上公开仓库时，这里的姓名和邮箱地址也会随着提交日志一同被公开，所以请不要使用不便公开的隐私信息。

将 color.ui 设置为 auto 可以让命令的输出拥有更高的可读性。

    $ git config --global color.ui auto

“~/.gitconfig”中会增加下面一行。

    [color]
      ui = auto

这样一来，各种命令的输出就会变得更容易分辨。

> 注意：下面大多数命令是在Win下 git bash 中使用，当然，linux终端也可以使用。

# 申请GitHub账号及相关设置

## 设置SSH Key

GitHub 上连接已有仓库时的认证，是通过使用了 SSH 的公开密钥认证方式进行的。现在让我们来创建公开密钥认证所需的 SSH Key，并将其添加至 GitHub。

在 Git bash 运行下面的命令创建 SSH Key。

    $ ssh-keygen -t rsa -C "your_email@example.com"
    Generating public/private rsa key pair.
    Enter file in which to save the key
    (/Users/your_user_directory/.ssh/id_rsa): 按回车键
    Enter passphrase (empty for no passphrase): 输入密码
    Enter same passphrase again: 再次输入密码

`your_email@example.com`的部分请改成您在创建账户时用的邮箱地址。密码需要在认证时输入，请选择复杂度高并且容易记忆的组合。输入密码后会出现提示已经创建密匙的提示，并告诉存储位置以及相关信息。其中，*id_rsa 文件是私有密钥，id_rsa.pub 是公开密钥*。

在 GitHub 中添加公开密钥（`Add SSH Key`），今后就可以用私有密钥进行认证了。

在 Title 中输入适当的密钥名称。Key 部分使用 `id_rsa.pub` 文件里的内容。`id_rsa.pub` 的内容可以用如下方法查看。

    $ cat ~/.ssh/id_rsa.pub
    ssh-rsa 公开密钥的内容 your_email@example.com

添加后，账户邮箱会收到邮件提示。可以使用如下命令测试用手中的私人密钥与 GitHub 进行认证和
通信。

    $ ssh -T git@github.com

若是出现
    
    The authenticity of host 'github.com (207.97.227.239)' can't be established.
    RSA key fingerprint is fingerprint值 .
    Are you sure you want to continue connecting (yes/no)? 输入yes

出现如下结果即为成功。

    Hi hirocastest! You've successfully authenticated, but GitHub does not provide shell access.

## 创建新仓库需要注意的细节

* Initialize this repository with a README

在 Initialize this repository with a README 选项上打钩，随后GitHub 会自动初始化仓库并设置 README 文件，让用户可以立刻clone 这个仓库。如果想向 GitHub 添加手中已有的 Git 仓库，建议不要勾选，直接手动 push。

* https://github.com/用户名/Hello-Word

是刚刚创建的仓库的页面。

* .gitignore

正如其名，这个设定会帮我们把不需要在Git仓库中进行版本管理的文件记录在 `.gitignore` 文件中，省去了每次根据框架进行设置的麻烦。

* GitHub Flavored Markdown

在 GitHub 上进行交流时用到的 Issue、评论、Wiki，都可以用 Markdown 语法表述，从而进行标记。准确地说应该是 `GitHub Flavored Markdown（GFM）`语法。该语法虽然是 GitHub 在 Markdown 语法基础上扩充而来的，但一般情况下只要按照原本的 Markdown 语法进行描述就可以。

# 简单试验对已有仓库的操作

## clone

将代码下载到了本地以仓库名作为文件夹名的文件夹中，但是与直接下载不同。

    $ git clone git@github.com:hirocastest/Hello-World.git
    Cloning into 'Hello-World'...
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Receiving objects: 100% (3/3), done.
    $ cd Hello-World

这里会要求输入 GitHub 上设置的公开密钥的密码。认证成功后，仓库便会被 clone 至仓库名后的目录中。将想要公开的代码提交至这个仓库再 push 到 GitHub 的仓库中，代码便会被公开。

如果之前并未设置密码，那么这里也就不用输入。

## 添加自己的文件

本地文件想要加入，需要先加到本地仓库。只有添加到其中，才能实现跟踪管理。

这里我们编写一个 `hello_world.php` 文件，用来输出“Hello World!”。

由于 hello_word.php 还没有添加至 Git 仓库，所以显示为 Untracked
files。

    $ git status
    # On branch master
    # Untracked files:
    # (use "git add <file>..." to include in what will be committed)
    #
    # hello_world.php
    nothing added to commit but untracked files present (use "git add" to track)

添加到本地仓库，这样一来，这个文件就进入了版本管理系统的管理之下。今后的更改管理都交由 Git 进行。通过 git add命令将文件加入暂存区，再通过 `git commit` 命令附加信息并提交。

    $ git add hello_world.php
    $ git commit -m "Add hello world script by php"
    [master d23b909] Add hello world script by php
    1 file changed, 3 insertions(+)
    create mode 100644 hello_world.php

添加成功后，可以通过 `git log` 命令查看提交日志。

    $ git log
    commit d23b909caad5d49a281480e6683ce3855087a5da
    Author: 你的信息
    Date: 你的时间
    Add hello world script by php

> 关于许可：实际使用时，只需将 LICENSE 文件加入仓库，并在README.md 文件中声明使用了何种许可协议即可。使用没有声明许可协议的软件时，以防万一最好直接联系著作者。

## push

之后只要执行 push，GitHub 上的仓库就会被更新。

    $ git push
    Counting objects: 4, done.
    Delta compression using up to 4 threads.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 328 bytes, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To git@github.com:hirocastest/Hello-World.git
    46ff713..d23b909 master -> master

# 本地新仓库推送远程

## init

要使用 Git 进行版本管理，必须先初始化仓库。

在包含所有要上传的文件的目录级别下：

    $ git init

如果初始化成功，执行了 `git init` 命令的目录下就会生成 `.git` 目录。这个 `.git` 目录里存储着管理当前目录内容所需的仓库数据。在 Git 中，我们将这个目录的内容称为“附属于该仓库的工作树”。文件的编辑等操作在工作树中进行，然后记录到仓库中，以此管理文件的历史快照。

## status

工作树和仓库在被操作的过程中，状态会不断发生变化。

    $ git status
    # On branch master
    #
    # Initial commit
    #
    nothing to commit (create/copy files and use "git add" to track)

结果显示了我们当前正处于 `master` 分支下。接着还显示了没有可提交的内容。所谓提交（Commit），是指“记录工作树中所有文件的当前状态”。

## add

要想让文件成为 Git 仓库的管理对象，就需要用 `git add` 命令将其加入暂存区（Stage 或者 Index）中。暂存区是提交之前的一个临时区域。

    $ git add README.md
    $ git status
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    # (use "git rm --cached <file>..." to unstage)
    #
    # new file: README.md
    #

可见，新建的文件被加到了暂存区（注意关注输出内容中括号中的内容，会有一些功能性的建议）

## commit

可以将当前暂存区中的文件实际保存到仓库的历史记录中。通过这些记录，我们就可以在工作树中复原文件。

    $ git commit -m "First commit"
    [master (root-commit) 9f129ba] First commit
    1 file changed, 0 insertions(+), 0 deletions(-)
    create mode 100644 README.md

`-m` 参数后的 "First commit"称作提交信息，是对这个提交的概述。若是不加 `-m`，直接执行 `git commit` 命令。执行后编辑器就会启动，并显示如下结果。

`git commit -am "..."` 命令一次完成 add，commit。

    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    # (use "git rm --cached <file>..." to unstage)
    #
    # new file: README.md
    #

在编辑器中记述提交信息的格式如下。

    * 第一行：用一行文字简述提交的更改内容
    * 第二行：空行
    * 第三行以后：记述更改的原因和详细内容

只要按照上面的格式输入，今后便可以通过确认日志的命令或工具看到这些记录。

在以 #（井号）标为注释的 Changes to be committed（要提交的更改）栏中，可以查看本次提交中包含的文件。将提交信息按格式记述完毕后，请保存并关闭编辑器，以 #（井号）标为注释的行不必删除。随后，刚才记述的提交信息就会被提交。

如果在编辑器启动后想中止提交，请将提交信息留空并直接关闭编辑器，随后提交就会被中止。

提交之后，工作树状态会提示没有更改，因为处于最新状态。

要修改上一条提交信息，可以使用 `git commit --amend` 命令。

## log

可以查看什么人在什么时候进行了提交或合并，以及操作前后有怎样的差别。

针对刚才

    $ git log
    commit 9f129bae19b2c82fb4e98cde5890e52a6c546922(指向这个提交的哈希值)
    Author: ...
    Date: ...
    First commit(该提交的提交信息。)

如果只想让程序显示第一行简述信息，可以在 `git log` 命令后加上 `--pretty=short` 。

    $ git log --pretty=short
    commit 9f129bae19b2c82fb4e98cde5890e52a6c546922
    Author: ...
    First commit

    $ git log -p README.md

在 `git log` 命令后加上 `-p` 参数，文件的前后差
别就会显示在提交信息之后。加上目录名，便会只显示该目录下的日志。如果加的是文件名，就会只显示与该文件相关的日志。

`git log -- graph` ——以图表形式查看分支合并提交等信息。

## diff

可以查看 **工作树**、**暂存区**、**最新提交**之间的差别。显示的内容的内容中，“+”号标出的是新添加的行，被删除的行则用“-”号标出。

要查看与最新提交的差别，请执行以下
命令。

    $ git diff HEAD

这里的 `HEAD` 是指向当前分支中最新一次提交
的指针。

## branch

在进行多个并行作业时，我们会用到分支。在这类并行开发的过程中，往往同时存在多个最新代码状态。`master` 分支是 Git 默认创建的分支，因此基本上所有开发都是以这个分支为中心进行的。不同分支中，可以同时进行完全不同的作业。等该分支的作业完成之后再与 `master` 分支合并。

`git branch` 命令可以将分支名列表显示，同时可以确认当前所在分支。

分支左侧标有“*”（星号），表示这是我们当前所
在的分支。结果中没有显示其他分支名，表示本地仓库中只存在 `master` 一个分支。

如果想以当前的 `master` 分支为基础创建新的分支，我们需要用到 `git checkout -b`命令。

    $ git checkout -b feature-A
    Switched to a new branch 'feature-A'

等同于：

    $ git branch feature-A
    $ git checkout feature-A

创建 `feature-A` 分支，并将当前分支切换为 `feature-A` 分支。这时再来查看分支列表，会显示我们处于 `feature-A` 分支下。

在这个状态下像正常开发那样修改代码、执行 `git add` 命令并进行提交的话，代码就会提交至 `feature-A` 分支。

命令 `git checkout -` 可以切换回上一个分支，当然指定名字也可以。

### 主干分支和特性分支

特性分支顾名思义，是集中实现单一特性（主题），除此之外不进行任何作业的分支。在日常开发中，往往会创建数个特性分支，同时在此之外再保留一个随时可以发布软件的稳定分支。稳定分支的角色通常由 `master` 分支担当。

主干分支是刚才我们讲解的特性分支的原点，同时也是合并的终点。通常人们会用 `master` 分支作为主干分支。主干分支中并没有开发到一半的代码，可以随时供他人查看。有时我们需要让这个主干分支总是配置在正式环境中，有时又需要用标签 Tag 等创建版本信息，同时管理多个版本发布。拥有多个版本发布时，主干分支也有多个。

## merge

接下来，我们假设 `feature-A` 已经实现完毕，想要将它合并到主干分支 `master` 中。为了在历史记录中明确记录下本次分支合并，我们需要创建合并提交。因此，在合并时加上 `--no-ff` 参数，在主干分支中执行

    $ git merge --no-ff feature-A

随后编辑器会启动，用于录入合并提交的信息。默认信息中已经包含了是从 `feature-A` 分支合并过来的相关内容，所以可不必做任何更改。将编辑器中显示的内容保存，关闭编辑器会提示成功合并。

合并时可能发生冲突(Conflict)，不解决，无法合并。按提示解决就好。

## reset 

要让仓库的 `HEAD` 、暂存区、当前工作树回溯到指定状态，需要用到 `git rest --hard` 命令。只要提供 **目标时间点的哈希值**，就可以完全恢复至该时间点的状态。

`git log` 命令只能查看以当前状态为终点的历史日志。所以这里要使用 `git reflog` 命令，查看当前仓库的操作日志。在日志中找出回溯历史之前的哈希值，通过 `git reset --hard` 命令恢复到回溯历史前的状态。

在日志中，我们可以看到 commit、checkout、reset、merge 等 Git 命令的执行记录。只要不进行 Git 的 GC（Garbage Collection，垃圾回收），就可以通过日志随意调取近期的历史状态。

## rebase

在合并特性分支之前，如果发现已提交的内容中有些许拼写错误等，不妨提交一个修改，然后将这个修改包含到前一个提交之中，压缩成一个历史记录。

`git rebase -i HEAD~2` 方式执行 `git rebase` 命令，可以选定当前分支中包含 `HEAD`（最新提交）在内的两个最新历史记录为对象，并在编辑器中打开。

出现类似

    pick 7a34294 Add feature-C
    pick 6fba227 Fix typo

将 `6fba227` 左侧的 `pick` 部分删除，改写为 `fixup`。

    pick 7a34294 Add feature-C
    fixup 6fba227 Fix typo

保存编辑器里的内容，关闭编辑器。提示 `rebase` 成功。这样一来，`Fix typo` 就从历史中被抹去，也就相当于 `Add feature-C`中从来没有出现过拼写错误。这算是一种良性的历史改写。

## remote add

在 GitHub 上新建一个仓库。为防止与其他仓库混淆，仓库名请与本地仓库保持一致。创建时请不要勾选 `Initialize this repository with a README` 选项。因为一旦勾选该选项，GitHub 一侧的仓库就会自动生成 README 文件，从创建之初便与本地仓库失去了整合性。虽然到时也可以强制覆盖，但为防止这一情况发生还是建议不要勾选该选项。

现在我们用 `git remote add` 命令将github新建仓库设置成本地仓库的远程仓库。

    $ git remote add origin 远程仓库路径

按照上述格式执行 `git remote add` 命令之后，Git 会自动将远程仓库的名称设置为 `origin`（标识符）。

## push

当前分支下本地仓库内容推送到远程仓库。

假定在 `master` 分支下工作。

    $ git push -u origin master

当前分支的内容就会被推送给远程仓库 `origin` 的 `master` 分支。

`-u` 参数可以在推送的同时，将 `origin` 仓库的 `master` 分支设置为本地仓库当前分支的 `upstream`（上游）。添加了这个参数，将来运行 `git pull` 命令从远程仓库获取内容时，本地仓库的这个分支就可以直接从 `origin` 的 `master` 分支获取内容，省去了另外添加参数的麻烦。

对于其他分支，我们在本地仓库中创建了 `feature-D` 分支，现在将它 push 给远程仓
库并保持分支名称不变。

    $ git checkout -b feature-D
    $ git push -u origin feature-D

# 获取远程仓库其他分支开发

不要与之前操作的仓库在同一目录下。执行 `git clone` 命令后我们会默认处于 `master` 分支下，同时系统会自动将 `origin` 设置成该远程仓库的标识符。也就是说，当前本地仓库的 `master` 分支与 GitHub 端远程仓库（origin）的 `master` 分支在内容上是完全相同的。

用 `git branch -a`命令查看当前分支的相关信息。添加 `-a` 参数可以同时显示本地仓库和远程仓库的分支信息。

若要获取远程特性分支 `feature-D`，可以使用如下操作。

    $ git checkout -b feature-D origin/feature-D

`-b` 参数的后面是本地仓库中新建分支的名称。为了便于理解，我们仍将其命名为 `feature-D`，让它与远程仓库的对应分支保持同名。新建分支名称后面是获取来源的分支名称。

例子中指定了 `origin/feature-D`，就是说以远程 `origin` 的仓库的 `feature-D` 分支作为来源，在本地仓库中创建 `feature-D` 分支。更改添加提交推送即可。

    $ git commit -am "Add feature-D"
    $ git push

此时，本地分支中并未有内容，需要使用 `pull`

    $ git pull origin feature-D

如果两人同时修改了同一部分的源代码，push 时就很容易发生冲突。所以多名开发者在同一个分支中进行作业时，为减少冲突情况的发生，建议更频繁地进行 push 和 pull 操作。
