---
auther: Lart
date:   2019-01-17
update: 2019-01-18 09:55:36
---

# import os, glob, fnmatch

针对某些操作, 官方推荐这些操作

This module provides a portable way of using operating system dependent functionality.

- If you just want to read or write a file see `open()`
- if you want to manipulate paths, see the `os.path` module
- if you want to read all the lines in all the files on the command line see the `fileinput` module
- for creating temporary files and directories see the `tempfile` module
- for high-level file and directory handling see the `shutil` module.

## 环境变量

- os.environ
- os.getenv(key, default=None)

```python
# 在操作系统中定义的环境变量，全部保存在os.environ这个变量中，可以直接查看：

>>> os.environ
environ({...'LD_LIBRARY_PATH': '/usr/local/cuda-9.0/lib64:/usr/local/cuda-9.0/lib64', ..., 'LC_IDENTIFICATION': 'zh_CN.UTF-8', ...})


# 要获取某个环境变量的值，可以调用如下操作：
>>> os.environ['MANPATH']
'/home/lart/texlive/2018/texmf-dist/doc/man:/usr/local/man:'
>>> os.environ.get('MANPATH')
'/home/lart/texlive/2018/texmf-dist/doc/man:/usr/local/man:'
>>> os.environ.get('MANPATH', 'not found')
'/home/lart/texlive/2018/texmf-dist/doc/man:/usr/local/man:'
>>> os.environ.get('MAINPATH', 'not found')
'not found'
>>> os.environ('MAINPATH')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: '_Environ' object is not callable

>>> os.getenv('MANPATH')
'/home/lart/texlive/2018/texmf-dist/doc/man:/usr/local/man:'
>>> os.getenv('MAINPATH', "not found")
'not found'
# os.getenv最大的差异就在于不存在路径的时候, 不会引发异常
>>> os.getenv('MAINPATH')
```

## 系统指令

- os.system(command)
  - 返回值只会有0(成功), 1, 2
- os.popen(command)
  - 把执行的cmd的输出作为值返回

python调用Shell脚本，有两种方法：`os.system(command)`或`os.popen(command)`, 前者返回值是脚本的退出状态码, 后者的返回值是脚本执行过程中的输出内容. 实际使用时视需求情况而选择.

```python
# 通过 os.popen() 返回的是 file read 的对象，对其进行读取 read() 的操作可以看到执行的输出。
output = os.popen('cat /proc/cpuinfo')
print(output.read())
```

## 系统信息

- os.name
- os.uname()
- sys.platform(`import sys`)
- os.sep
- os.linesep
- os.pathsep
- os.curdir
- os.pardir
- os.cpu.count()

```python
# 获取系统类型
>>> os.name
'posix'
>>> os.uname()
posix.uname_result(sysname='Linux', nodename='lart', release='4.15.0-43-generic', version='#46-Ubuntu SMP Thu Dec 6 14:45:28 UTC 2018', machine='x86_64')
>>> os.uname()[0]
'Linux'
>>> os.uname()[4]
'x86_64'
>>> import sys
>>> sys.platform
'linux'

# 获取当前路径/父路径的标识符
>>> os.curdir
'.'
>>> os.pardir
'..'
# 获取路径分隔符和换行符的表示
>>> os.sep
'/'
>>> os.linesep
'\n'
# 获取当前系统环境变量分隔符
>>> os.pathsep
':'

# 获取cpu核心数
# 此数字不等于当前进程可以使用的CPU数量。可以使用`len(os.sched_getaffinity(0))`获得可用CPU的数量
>>> os.cpu_count()
12
>>> len(os.sched_getaffinity(0))
12
```

## 文件(夹)操作

- os.remove(path, *, dir_fd=None)
  - 移除path对应的 **文件**
  - 若path为文件夹, 会抛出异常`OSError`
- os.rmdir(path, *, dir_fd=None)
  - 移除path对应的 **文件夹**
  - Only works when the directory is empty, otherwise, OSError is raised.
- os.mkdir(path, mode=0o777, *, dir_fd=None)
  - 创建path对应的 **文件夹**
  - If the directory already exists, `FileExistsError` is raised.
- os.removedirs(name)
  - 递归删除文件夹
  - For example, os.removedirs('foo/bar/baz') will first remove the directory 'foo/bar/baz', and then remove 'foo/bar' and 'foo' **if they are empty**. Raises `OSError` if the leaf directory could not be successfully removed
- os.makedirs(name, mode=0o777, exist_ok=False)
  - 递归创建文件夹
  - If `exist_ok` is False (the default), an `OSError` is raised if the target directory already exists.
  - Note `makedirs()` will become confused if the path elements to create include pardir (eg. “..” on UNIX systems).

- os.rename(src, dst, *, src_dir_fd=None, dst_dir_fd=None)
  - 将文件(夹)的名字从`src`改为`dst`
  - 只能对相应的文件进行重命名, 不能重命名文件的上级目录名
  - If dst is a directory, `OSError` will be raised. 如果`src`参数对应文件或目录不存在, 以及如果`dst`参数对应文件或目录已经存在，都会报错
  - If you want cross-platform overwriting of the destination, use `replace()`.
- os.renames(old, new)
  - 递归重命名文件(文件夹)
  - 既可以重命名文件, 也可以重命名文件的上级目录名
  - Note This function can fail with the new directory structure made if you lack permissions needed to remove the leaf directory or file.

## 路径获取

- os.getcwd()
- os.listdir(path='.')
- os.scandir(path='.')
- os.walk(top, topdown=True, onerror=None, followlinks=False)

```python
######################################################################################
# 返回当前进程工作路径
>>> os.getcwd()
'/home/lart/md/python总结'

######################################################################################
# 返回指定path下的文件夹与文件(包含后缀名)的完整名字, 不包含'.', '..'这样的特殊目录
>>> os.listdir('.')
['converter.py', 'face++.py', 'Face++.ipynb', 'person-young-man-beard-emotions-157966.png', '.ipynb_checkpoints', '.idea', 'supervisely.py', 'facepp-python-sdk-master']

######################################################################################
# 使用内容管理器管理os.scandir迭代器, 返回的是一个`os.DirEntry`对象, 相比`os.listdir`可以获得更多的信息
# `os.DirEntry` Object yielded by scandir() to expose the file path and other file attributes of a directory entry.
# 包含如下属性与方法: `name, path, inode(), is_dir(*, follow_symlinks=True), is_file(*, follow_symlinks=True), is_symlink(), stat(*, follow_symlinks=True)`
>>> with os.scandir('.') as it:
...     for entry in it:
...         if not entry.name.startswith('.') and entry.is_file():
...             print(entry.name)
...         if entry.is_dir():
...             print(f"{entry.name}是名字")
...
converter.py
face++.py
Face++.ipynb
person-young-man-beard-emotions-157966.png
.ipynb_checkpoints是名字
.idea是名字
supervisely.py
facepp-python-sdk-master是名字
```

有目录:

```sh
➜  tool_scripts tree
.
├── converter.py
├── Face++.ipynb
├── facepp-python-sdk-master
│   ├── call_four_task.ipynb
│   ├── call.py
│   ├── facepp_custom.py
│   ├── imgResource
│   │   ├── demo.jpeg
│   │   ├── gray_image.png
│   │   ├── merge.jpg
│   │   ├── resultImg.jpg
│   │   ├── resultImg.png
│   │   ├── search.png
│   │   ├── segment.b64
│   │   └── segment.jpg
│   ├── PythonSDK
│   │   ├── compat.py
│   │   ├── facepp.py
│   │   ├── ImagePro.py
│   │   ├── __pycache__
│   │   │   ├── compat.cpython-36.pyc
│   │   │   ├── facepp.cpython-36.pyc
│   │   │   ├── ImagePro.cpython-36.pyc
│   │   │   └── structures.cpython-36.pyc
│   │   └── structures.py
│   ├── Python SDK demo 使用文档.pdf
│   └── README.md
├── face++.py
├── person-young-man-beard-emotions-157966.png
└── supervisely.py

4 directories, 26 files
```

```python
######################################################################################
# os.walk(top, topdown=True, onerror=None, followlinks=False)
# top 是要便利的目录的地址, 为最上层的地址
# topdown 为True(默认为True)，则优先遍历父目录(先把父目录里的所有文件(文件夹)遍历完, 在搜索子文件夹)，否则优先遍历top的子目录 (广度优先与深度优先)
# onerror 需要一个callable对象，当walk需要异常时，会调用
# followlinks如果为True，则会遍历目录下的快捷方式(linux下是`symbolic link`)实际所指的目录(默认False)
#
# os.walk 的返回值是一个生成器(generator),也就是说不断的遍历它，来获得所有的内容。
# 每次遍历的对象都是返回的是一个三元组`(dirpath, dirnames, filenames)`
# dirpath (string)当前正在遍历的这个文件夹的本身的地址
# dirnames (list)**该文件夹中** 所有的 *子文件夹* 的名字(不包括子目录, excluding '.' and '..')
# filenames (list)**该文件夹中** 所有的 *文件* 的名字
# 注意:
#   - 名字列表中不包含完整路径, 要想得到完整的路径, 可以使用`os.path.join(dirpath, name)`.
#   - 当设定`followlinks`为True时, 若是链接指向父文件夹, 会导致无线递归, 因为`.walk()`不会关注搜索过得部分
#   - 如果传递相对路径名，请不要在`walk()`的恢复之间更改当前工作目. `walk()`从不更改当前目录, 并假定其调用者也不会

###############################################################################
# 下面使用了`topdown=True`
>>> for dirpath, dirnames, filenames in os.walk('.'):
...     print(dirpath)
...     print(dirnames)
...     print(filenames)
...
.
['.ipynb_checkpoints', '.idea', 'facepp-python-sdk-master']
['converter.py', 'face++.py', 'Face++.ipynb', 'person-young-man-beard-emotions-157966.png', 'supervisely.py']
./.ipynb_checkpoints
[]
['Face++-checkpoint.ipynb']
./.idea
[]
['misc.xml', 'modules.xml', 'workspace.xml', 'tool_scripts.iml', 'encodings.xml']
./facepp-python-sdk-master
['imgResource', 'PythonSDK', '.ipynb_checkpoints', '.idea']
['call.py', 'facepp_custom.py', 'call_four_task.ipynb', 'Python SDK demo 使用文档.pdf', 'README.md', '.gitignore']
./facepp-python-sdk-master/imgResource
[]
['demo.jpeg', 'resultImg.jpg', 'merge.jpg', 'gray_image.png', 'segment.jpg', 'search.png', 'segment.b64', 'resultImg.png']
./facepp-python-sdk-master/PythonSDK
['__pycache__']
['compat.py', 'facepp.py', 'ImagePro.py', 'structures.py']
./facepp-python-sdk-master/PythonSDK/__pycache__
[]
['compat.cpython-36.pyc', 'facepp.cpython-36.pyc', 'ImagePro.cpython-36.pyc', 'structures.cpython-36.pyc']
./facepp-python-sdk-master/.ipynb_checkpoints
[]
['call_four_task-checkpoint.ipynb']
./facepp-python-sdk-master/.idea
[]
['misc.xml', 'modules.xml', 'facepp-python-sdk-master.iml', 'workspace.xml', 'encodings.xml']

###############################################################################
# 下面使用了`topdown=False`
>>> for dirpath, dirnames, filenames in os.walk('.', topdown=False):
...     print(dirpath)
...     print(dirnames)
...     print(filenames)
...
./.ipynb_checkpoints
[]
['Face++-checkpoint.ipynb']
./.idea
[]
['misc.xml', 'modules.xml', 'workspace.xml', 'tool_scripts.iml', 'encodings.xml']
./facepp-python-sdk-master/imgResource
[]
['demo.jpeg', 'resultImg.jpg', 'merge.jpg', 'gray_image.png', 'segment.jpg', 'search.png', 'segment.b64', 'resultImg.png']
./facepp-python-sdk-master/PythonSDK/__pycache__
[]
['compat.cpython-36.pyc', 'facepp.cpython-36.pyc', 'ImagePro.cpython-36.pyc', 'structures.cpython-36.pyc']
./facepp-python-sdk-master/PythonSDK
['__pycache__']
['compat.py', 'facepp.py', 'ImagePro.py', 'structures.py']
./facepp-python-sdk-master/.ipynb_checkpoints
[]
['call_four_task-checkpoint.ipynb']
./facepp-python-sdk-master/.idea
[]
['misc.xml', 'modules.xml', 'facepp-python-sdk-master.iml', 'workspace.xml', 'encodings.xml']
./facepp-python-sdk-master
['imgResource', 'PythonSDK', '.ipynb_checkpoints', '.idea']
['call.py', 'facepp_custom.py', 'call_four_task.ipynb', 'Python SDK demo 使用文档.pdf', 'README.md', '.gitignore']
.
['.ipynb_checkpoints', '.idea', 'facepp-python-sdk-master']
['converter.py', 'face++.py', 'Face++.ipynb', 'person-young-man-beard-emotions-157966.png', 'supervisely.py']
```

### `os.path`

#### 路径显示

- os.path.abspath(path)
  - 返回path对应的绝对路径

- os.path.commonpath(**paths**)
- os.path.commonprefix(list)

```python
>>> os.path.commonprefix(['/usr/lib', '/usr/local/lib'])
'/usr/l'
>>> os.path.commonpath(['/usr/lib', '/usr/local/lib'])
'/usr'
```

- os.path.dirname(path)
  - 返回path对应的倒数第二级目录

```python
>>> path = '/home/lart/Datasets/tool_scripts'
>>> os.path.dirname(path)
'/home/lart/Datasets'
>>> path = '/home/lart/Datasets/tool_scripts/converter.py'
>>> os.path.dirname(path)
'/home/lart/Datasets/tool_scripts'
```

- os.path.exists(path)

```python
'/home/lart/Datasets'
>>> os.path.exists(path)
True
>>> path = '/home/lart/Datasets/tool_scripts/converter.py'
>>> os.path.exists(path)
True
```

#### 路径判断

- os.path.expanduser(path)
  - 替换path中的`~`为用户目录(HOME)实际值
- os.path.expandvars(path)
  - 替换$name or ${name}为environment variable name对应的实际值

```python
>>> path = '~/Datasets/tool_scripts/converter.py'
>>> os.path.expanduser(path)
'/home/lart/Datasets/tool_scripts/converter.py'
>>> path = '~/Datasets/tool_scripts'
>>> os.path.expanduser(path)
'/home/lart/Datasets/tool_scripts'
```

#### 路径时间

- os.path.getatime(path)
  - 返回最后一次访问path的时间
- os.path.getmtime(path)
  - 返回最后一次修改path的时间
- os.path.getctime(path)
  - 返回path对应的的ctime
  - 在某些系统（如Unix）上是最后一次元数据更改的时间，而在其他系统（如Windows）上则是路径的创建时间

```python
>>> path = '/home/lart/Datasets/tool_scripts'
>>> os.path.getctime(path)
1547694633.120985
>>> os.path.getctime(path + '/converter.py')
1546766886.501085
```

#### 路径体积

- os.path.getsize(path)
  - 返回path对应的体积

```python
>>> path = '/home/lart/Datasets/tool_scripts'
>>> os.path.getsize(path)
4096
>>> os.path.getsize(path + '/converter.py')
857
```

#### 路径规范

- os.path.isabs(path)
  - path是否为绝对路径
- os.path.isfile(path)
- os.path.isdir(path)
- os.path.islink(path)
  - 如名

- os.path.join(path, *paths)
  - 使用系统对应的分隔符拼接路径
  - 对于Windows: Note that since there is a current directory for each drive, `os.path.join("c:", "foo")` represents a path relative to the current directory on drive C: `(c:foo)`, not `c:\foo`.

- os.path.normcase(path)
  - 规范化路径名的大小写。
  - 在Unix和Mac OS X上，这将返回路径不变; 在不区分大小写的文件系统上，它将路径转换为小写。在Windows上，它**还将正斜杠转换为反斜杠**。
- os.path.normpath(path)
  - 通过折叠冗余分隔符和上级引用来规范化路径名，以便`A//B`，`A/B/`，`A/./B`和`A/foo/../B`都变为`A/B`.
  - 此字符串操作可能会更改包含符号链接的路径的含义
  - 在Windows上，它将**正斜杠转换为反斜杠**
  - 要规范化大小写，请使用`normcase()`

- os.path.realpath(path)
  - 返回指定文件名的规范路径，消除路径中遇到的任何符号链接（如果操作系统支持它们）

- os.path.samefile(path1, path2)
  - 如果两个路径名参数都引用相同的文件或目录，则返回True
- os.path.sameopenfile(fp1, fp2)
  - 如果文件描述符fp1和fp2引用同一文件，则返回True

- os.path.split(path)
  - 将路径名路径拆分为一对`(head, tail)`，其中tail是最后一个路径名组件，head是指向该路径的所有内容, tail永远不会有斜线; 如果path以斜线结尾，则tail将为空; 如果路径中没有斜杠，则head将为空
  - 如果`path`为空，则head和tail都为空
  - 尾部斜杠从head剥离，除非它是root(仅一个或多个斜杠)
  - 在所有情况下，`join(head, tail)`返回与path相同位置的路径(但字符串可能不同).
- os.path.splitdrive(path)
  - 将路径名路径拆分为一对`(drive, tail)`，其中drive是挂载点或空字符串. 在不使用驱动器规格的系统上，drive将始终为空字符串
  - 在所有情况下，`drive + tail`将与`path`相同。
- os.path.splitext(path)
  - 将路径名路径拆分为一对`(root, ext)`, 使得`root + ext == path`，ext为空或以`.`开头，最多包含一个句点. `basename`的前导`.`被忽略
  - `splitext('.cshrc')`返回`('.cshrc'，'')`。
- os.path.supports_unicode_filenames
  - 如果可以将任意Unicode字符串用作文件名(在文件系统强加的限制内)，则为True。

## glob

glob模块根据Unix shell使用的规则查找与指定模式匹配的所有路径名，尽管结果以任意顺序返回。

实现了:

- `*` 表示匹配0到多个字符
- `？` 表示匹配一个字符
- 用`[]` 表示匹配括号里表示的字符范围
  - `[exp]` 匹配指定范围内的字符，如：`[1-9]`匹配1至9范围内的字符, 破折號(-)表示一個範圍, 上面的範圍也可直接用[0123456789]來表示
  - `[!exp]` 匹配不在指定范围内的字符
  - 对于文字匹配，将元字符包装在括号中。例如，`'[?]'`匹配字符`'?'`

glob中模式規則不是正則表達式, 而是, 符合標準Uinx路徑擴展規則. 但是**Shell變量名和符號(~)是不被擴充的**, *只有一些特殊的字符: 兩個不同的通配符和字母範圍被支持(见上)*. 对于`~`符和shell变量扩展，请使用`os.path.expanduser()`和`os.path.expandvars(0)`. 模塊規則適合於文檔名的片段(**以/為分隔**, 也就是只能匹配`/`与`/`之间的文本), 但模式中的路徑**可以是相對或者絕對路徑**.

这是通过一致地使用`os.scandir()`和`fnmatch.fnmatch()`函数来完成的，而**不是通过实际调用子shell**。请注意，与`fnmatch.fnmatch()`不同，glob将以点`(.)`开头的文件名视为特殊情况, 通配符不会进行匹配. 可见后面的例子.

- glob.glob(pathname, *, recursive=False)
  - 返回可能为空的(**无序的**, 可以借助`sorted()`排序)匹配路径名字列表, 这个`pathname`必须是包含路径规范的字符串
  - 如果`recursive`为`true`，则模式`**`将**匹配任何文件以及零个或多个目录和子目录**, 如果模式后跟`os.sep`，则只有**目录和子目录匹配**
  - 注意在大型目录树中使用`**`模式可能会消耗过多的时间。
- glob.iglob(pathname, *, recursive=False)
  - 功能同上, 只是返回一个迭代器, 避免了同时的大量存储

```python
>>> import glob
>>> path
'/home/lart/Datasets/tool_scripts'
>>> glob.iglob(path + '/*')
<generator object _iglob at 0x7fdca3773930>
# 这里只返回下一级
>>> glob.glob(path + '/*', recursive=True)
['/home/lart/Datasets/tool_scripts/converter.py', '/home/lart/Datasets/tool_scripts/face++.py', '/home/lart/Datasets/tool_scripts/Face++.ipynb', '/home/lart/Datasets/tool_scripts/person-young-man-beard-emotions-157966.png', '/home/lart/Datasets/tool_scripts/supervisely.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master']
# 这里会遍历所有的子目录和文件
>>> glob.glob(path + '/**', recursive=True)
['/home/lart/Datasets/tool_scripts/', '/home/lart/Datasets/tool_scripts/converter.py', '/home/lart/Datasets/tool_scripts/face++.py', '/home/lart/Datasets/tool_scripts/Face++.ipynb', '/home/lart/Datasets/tool_scripts/person-young-man-beard-emotions-157966.png', '/home/lart/Datasets/tool_scripts/supervisely.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/call.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/demo.jpeg', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/resultImg.jpg', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/merge.jpg', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/gray_image.png', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/segment.jpg', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/search.png', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/segment.b64', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/imgResource/resultImg.png', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/compat.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/facepp.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/__pycache__', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/__pycache__/compat.cpython-36.pyc', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/__pycache__/facepp.cpython-36.pyc', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/__pycache__/ImagePro.cpython-36.pyc', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/__pycache__/structures.cpython-36.pyc', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/ImagePro.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/PythonSDK/structures.py', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/facepp_custom.py','/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/call_four_task.ipynb', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/Python SDK demo 使用文档.pdf', '/home/lart/Datasets/tool_scripts/facepp-python-sdk-master/README.md']


>>> glob.glob('./[0-9].*')
['./1.gif', './2.txt']
>>> glob.glob('*.gif')
['1.gif', 'card.gif']
>>> glob.glob('?.gif')
['1.gif']
>>> glob.glob('**/*.txt', recursive=True)
['2.txt', 'sub/3.txt']
>>> glob.glob('./**/', recursive=True)
['./', './sub/']
```

If the directory contains files starting with `.` they won’t be matched by default. 需要在匹配路径名字上补上符号`.`

For example, consider a directory containing `card.gif` and `.card.gif`:

```python
>>>
>>> import glob
>>> glob.glob('*.gif')
['card.gif']
>>> glob.glob('.c*')
['.card.gif']
```

- glob.escape(pathname)
  - 轉義所有的特殊字符（'？'，'*'和'[']）
  - 如果想匹配任意可能包含特殊字符的字符串，這將非常有用。drive/UNC的sharepoints中的特殊字符不會轉義，例如在Windows系統上`escape('//?/c:/Quo vadis?.txt')`將返回`'//?/c:/Quo vadis[?].txt'`.
  - **每個特殊字符通過構建包含單個條目的字符範圍來轉義**, 例如，`[?]`便是字符`?`的转义

```python
# https://hk.saowen.com/a/83de58e8a7e060d8ace69c912cbd209948d6c4fe533aa2746201d96a1b45a8bf
import glob

specials = '?*['

for char in specials:
    pattern = 'dir/*' + glob.escape(char) + '.txt'
    print('Searching for:  {!r}'.format(pattern))
    for name in sorted(glob.glob(pattern)):
        print(name)
    print()

# 输出
Searching for:  'dir/*[?].txt'
dir/file?.txt

Searching for:  'dir/*[*].txt'
dir/file*.txt

Searching for:  'dir/*[[].txt'
dir/file[.txt
```

```python
# https://www.jianshu.com/p/b1f24d56d73b
>>> glob.escape('./**.?.*.[a-z]')
'./[*][*].[?].[*].[[]a-z]'
```

## fnmatch

此模块提供对Unix shell样式通配符的支持，这些通配符与正则表达式（在re模块中记录）不同。shell样式通配符中使用的特殊字符是：

- `*` 表示匹配0到多个字符
- `？` 表示匹配一个字符
- 用`[]` 表示匹配括号里表示的字符范围
  - `[exp]` 匹配指定范围内的字符，如：`[1-9]`匹配1至9范围内的字符, 破折號(-)表示一個範圍, 上面的範圍也可直接用[0123456789]來表示
  - `[!exp]` 匹配不在指定范围内的字符
  - 对于文字匹配，将元字符包装在括号中。例如，`'[?]'`匹配字符`'?'`

**请注意**:

- 文件名分隔符（Unix上的`/`）对于此模块并不是特殊字符。与`glob`模块略有不同
- 同样，以`.`开头的文件名对于此模块并不是特殊字符，并且可用`*`和`?`匹配

主要的方法:

- fnmatch.fnmatch(filename, pattern)
  - 测试是否filename字符串可以匹配模式pattern, 返回True或False
  - 这两个参数都使用`os.path.normcase()`进行大小写规范化。`fnmatchcase()`可用于执行区分大小写的比较，无论这是否是操作系统的标准。
- fnmatch.fnmatchcase(filename, pattern)
  - 比较区分大小写，不使用`os.path.normcase()`。
- fnmatch.filter(names, pattern)
  - 返回与pattern匹配的名称列表的子集。它与`[n for n in names if fnmatch(n, pattern)]`相同，但实现更有效。
- fnmatch.translate(pattern)
  - 返回**转换为正则表达式的shell样式模式**，以便与`re.match()`一起使用。

对于目录:

```sh
>>> print(os.popen('tree -a -L 2').read())
.
├── converter.py
├── Face++.ipynb
├── facepp-python-sdk-master
│   ├── call_four_task.ipynb
│   ├── call.py
│   ├── facepp_custom.py
│   ├── .gitignore
│   ├── .idea
│   ├── imgResource
│   ├── .ipynb_checkpoints
│   ├── PythonSDK
│   ├── Python SDK demo 使用文档.pdf
│   └── README.md
├── face++.py
├── .idea
│   ├── encodings.xml
│   ├── misc.xml
│   ├── modules.xml
│   ├── tool_scripts.iml
│   └── workspace.xml
├── .ipynb_checkpoints
│   └── Face++-checkpoint.ipynb
├── person-young-man-beard-emotions-157966.png
├── supervisely.py
└── .test
```

从下面的测试可以看出来, 实际上, `glob`应该更为常用一些, `fnmatch`模块不会忽略`.`开头的文件(夹), 这有时候会造成不必要的麻烦, 因大多数时候, 是不会需要搜索这些文件夹的.

```python
>>> import fnmatch
>>> for file in os.listdir('.'):
...     if fnmatch.fnmatch(file, '*'):
...         print(file)
...
converter.py
face++.py
.test
Face++.ipynb
person-young-man-beard-emotions-157966.png
.ipynb_checkpoints
.idea
supervisely.py
facepp-python-sdk-master

>>> glob.glob('./*')
['./converter.py', './face++.py', './Face++.ipynb', './person-young-man-beard-emotions-157966.png', './supervisely.py', './facepp-python-sdk-master']
```

```python
>>> import fnmatch, re
>>>
>>> regex = fnmatch.translate('*.txt')
>>> regex
'(?s:.*\\.txt)\\Z'
>>> reobj = re.compile(regex)
>>> reobj.match('foobar.txt')
<re.Match object; span=(0, 10), match='foobar.txt'>
```

## 参考链接

- Python glob.md: <https://hk.saowen.com/a/83de58e8a7e060d8ace69c912cbd209948d6c4fe533aa2746201d96a1b45a8bf>
- Python 3.7标准库文档: <https://docs.python.org/3/library/index.html>
