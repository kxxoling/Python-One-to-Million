# IPython

IPython 是一个增强了交互能力的 Python REPL 环境，IPython v1-3 版本安装同时会安装
IPython notebook，相当于一个网页版的 REPL 环境。
相对于原生的 Python REPL，IPython 主要提供了 Tab 自动补全、shell 命令交互、
快速文档查询等功能。
除了在 shell 中使用外，Django shell 以及 PyCharm IDE 都提供了 IPython 的支持——
安装了 IPython 后会选择其作为默认 python shell。


## 启动 & 退出

启动 IPython：``ipython``

启动 IPython notebook：``ipython notebook``

启动 IPython Qt shell：``ipython qtshell``

退出：双击 Ctrl-D 或者 ``exit`` 或者 ``quit`` 或者 ``exit()``。


## 特性

### 魔法指令（Magic Command）

魔法指令是 IPython 定义的命令，以提供 IPython 环境下与环境交互的能力，通常对应于 shell 命令。
你可以在 IPython 控制台中输入 ``ls`` 或者 ``%ls`` 来查看当前目录文件。

魔法指令通常以 ``%`` 开始，对于非 ``%`` 开始的魔法指令，IPython 会首先判断它是不是
Python 对象，如是对象则优先作为 Python 对象处理。

更全面的 magic 命令列表和功能介绍见[这里](https://damontallen.github.io/IPython-quick-ref-sheets/)。

魔法指令中还包括了很多 Shell 命令，但并不是全部，IPython 也支持直接执行 Shell
命令，只需要以感叹号 ``!`` 起始，例如：``!ls``。


### Tab 补全

原生的 Python shell 可以通过 ``rlcompleter`` 和 ``readline`` 库获得 Tab 补全能力：

```python
import rlcompleter, readline
readline.parse_and_bind('tab: complete')
```

IPython 的支持更强大一些。不过 IPython 的自动补全依旧是循环式的，需要菜单式的可以试试 ptpython。


### Plot

常见用法是配合 mathplotlib，可以调用 mathplotlib GUI 程序展现数据图。


### 自省

IPython 提供了 ``?`` 和 ``??`` 命令，? 操作符会截断长的字符串。相反，?? 不会截断长字符串，
如果有源代码的话还会以语法高亮形式显示它们。

除了简化 Python 的自省，它也提供自身的自省。比如，当你在 IPython shell 输入了大量命令后：

```ipython
In [1]: a = 1

In [2]: b = 2

In [3]: c = 3

In [4]: d = {}

In [5]: e = []

In [6]: for i in range(20):
...: e.append(i)
...: d[i] = b
...:
```

你可以快速查看那些输入的历史记录:

```ipython
In [7]: hist
1: a = 1
2: b = 2
3: c = 3
4: d = {}
5: e = []
6: for i in range(20):
    e.append(i)
    d[i] = b
```

要去掉历史记录中的序号（这里是1至6），使用命令hist -n:

```ipython
In [8]: hist -n
a = 1
b = 2
c = 3
d = {}
e = []
for i in range(20):
    e.append(i)
    d[i] = b
```

这样你就可以方便的将代码复制到一个文本编辑器中。要在历史记录中搜索，
可以先输入一个匹配模型，然后按 Ctrl-P。找到一个匹配后，继续按 Ctrl-P 会向后搜索再上一个匹配，
Ctrl-N 则是向前搜索最近的匹配。


### 编辑

当在 Python 提示符下试验一个想法时，经常需要通过编辑器修改源代码（甚至是反复修改）。
在 IPython 下输入 ``edit`` 就会根据环境变量$EDITOR调用相应的编辑器。如果$EDITOR为空，
则会调用 vi（Unix）或记事本（Windows）。要回到IPython提示符，直接退出编辑器即可。
如果是保存并退出编辑器，输入编辑器的代码会在当前名字空间下被自动执行。
如果你不想这样，使用 ``edit -x``。如果要再次编辑上次最后编辑的代码，使用 ``edit -p``。
在上一个特性里，我提到使用 ``hist -n`` 可以很容易的将代码拷贝到编辑器。
一个更简单的方法是 ``edit`` 加 Python 列表的切片（slice）语法。假定 ``hist`` 输出如下:

```python
In [29]: hist
1 : a = 1
2 : b = 2
3 : c = 3
4 : d = {}
5 : e = []
6 :
for i in range(20):
e.append(i)
d[i] = b

7 : %hist
```

现在要将第4，5，6句代码导出到编辑器，只要输入：

```
edit 4:7
```


### Debugger接口

IPython 的另一特性是它与 Python debugger 的接口。在 IPython shell 下输入 magic 关键字
``pdb`` 就会在产生一个异常时自动开关 debugging 的功能。在自动 ``pdb`` 启用的情况下，
当 Python 遇到一个未处理的异常时 Python debugger 就会自动启动。你在 debugger
中的当前行就是异常发生的那一行。IPython 的作者说有时候当他需要在某行代码处 debug 时，
他会在开始 debug 的地方放一个表达式 1/0。启用 pdb，在 IPython 中运行代码。
当解释器处理到 1/0 那一行时，就会产生一个 ZeroDivisionError 异常，
然后他就在指定的代码处被带到一个调试会话中了。

### 运行

有时候当你在一个交互式shell中时，如果可以运行某个源文件中的内容将会很有用。
运行magic关键字run带一个源文件名就可以在IPython解释器中运行一个文件了
（例如run <源文件> <运行源文件所需参数>）。参数主要有以下这些：

* -n 阻止运行源文件代码时__name__变量被设为”__main__”。这会防止
``if __name__ == "__main__":``
块中的代码被执行
* -i 源文件就在当前IPython的名字空间下运行而不是在一个新的名字空间中。
如果你需要源代码可以使用在交互式session中定义的变量就会很有用。
* -p 使用Python的profiler模块运行并分析源代码。使用该选项代码不会运行在当前名字空间。


### 宏

宏允许用户为一段代码定义一个名字，这样你可以在以后使用这个名字来运行这段代码。
就象在magic关键字edit中提到的，列表切片法也适用于宏定义。假设有一个历史记录如下:

```
In [3]: hist
1: l = []
2: for i in l:
    print i
```

你可以这样来定义一个宏：

```python
In [4]: macro print_l 2
```

Macro print_l created. To execute, type its name (without quotes). Macro contents:

```
for i in l:
    print i
```

运行宏:

```
In [5]: print_l
Out[5]: Executing Macro...
```

在这里，列表l是空的，所以没有东西被输出。但这其实是一个很强大的功能，
我们可以赋予列表l某些实际值，再次运行宏就会看到不同的结果:

```
In [6]: l = range(5)

In [7]: print_l
Out[7]: Executing Macro...
0
1
2
3
4
```

当运行一个宏时就好象你重新输入了一遍包含在宏 ``print_1`` 中的代码。
它还可以使用新定义的变量 ``l``。
由于Python语法中没有宏结构（也许永远也不会有），在一个交互式shell
中它更显得是一个有用的特性。

### 环境（Profiles）

就像早前提到的那样，IPython安装了多个配置文件用于不同的环境。
配置文件的命名规则是ipythonrc-。要使用特定的配置启动IPython，需要这样

```
ipython -p
```

一个创建你自己环境的方法是在$HOME/.ipython目录下创建一个IPython配置文件，名字就叫做ipythonrc- ，这里是你想要的环境的名字。如果你同时进行好几个项目，而这些项目又用到互不相同的特殊的库，这时候每个项目都有自己的环境就很有用了。你可以为每个项目建立一个配置文件，然后在每个配置文件中import该项目中经常用到的模块。


## 使用操作系统的Shell

使用默认的IPython配置文件，有几个Unix Shell命令（当然，是在Unix系统上），cd，pwd和ls都能象在bash下一样工作。运行其它的shell命令需要在命令前加!或!!。使用magic关键字%sc和%sx可以捕捉shell命令的输出。

pysh环境可以被用来替换掉shell。使用-p pysh参数启动的IPython，可以接受并执行用户$PATH中的所有命令，同时还可以使用所有的Python模块，Python关键字和内置函数。例如，我想要创建500个目录，命名规则是从d_0_d到d_500_d（译注：呵呵，作者这里犯了个小小的计算错误，你能看出来吗），我可以使用-p pysh启动IPython，然后就象这样:

```
jjones@cerberus[foo]|2> for i in range(500):
|.> mkdir d_${i}_d
|.>
```

这就会创建500个目录:

```
jjones@cerberus[foo]|8> ls -d d* | wc -l
500
```

注意这里混合了Python的range函数和Unix的mkdir命令。

注意，虽然ipython -p pysh提供了一个强大的shell替代品，但它缺少正确的job控制。在运行某个很耗时的任务时按下Ctrl-z将会停止IPython session而不是那个子进程。


## 问题和方法

虽然作为标准 Python Shell 的替代，IPython 总的来说很完美，但仍然存在一些问题。

第一个问题是关于配色。在我的一个系统上，使用了一个白色背景的 xterm，在使用 ``?`` 和 ``??``
查询一个对象或模块的信息时，虽然会显示对象的定义，但看起来就好象丢失了。
那是因为在构造函数中的的参数默认是白色字体。我的解决办法是在 IPython Shell
中输入 ``colors LightBG`` 设置 Shell 主题。

第二个问题是关于自动缩进和代码粘贴的。autoindent 启用状态，IPython 会对我粘贴的已排好缩进的代码重新缩进。
例如下面的代码:

```
for i in range(10):
    for j in range(10):
        for k in range(10):
            pass
```

会变成:

```
for i in range(10):
        for j in range(10):
                for k in range(10):
                        pass
```

在上面的例子中并不是个问题，因为在它仍然保证了缩进一致。在另一些情况下，可能会成为真正的问题。
可以使用 magic 命令 ``autoindent`` 来开关自动缩进，就像在 Vim 中设置 ``set paste`` 一样。


## 作为库使用

NotFinished


## Vim 模式


## Mac 下的快捷启动

IPython wiki 提供了一段 [AppleScript launcher] 脚本，把它写入 ``~/bin/ipy``
在 Mac Spotlight 中输入 ``ipy`` 并回车就可以在新 iTerm 窗口中打开 IPython Shell。
下面是我稍微修改后的版本：

```applescript
#!/usr/bin/osascript
-- Run ipython on a new iTerm
-- See http://www.iterm2.com/#/section/documentation/scripting

tell application "iTerm"
    activate
    set ipyterm to (make new terminal)
    tell ipyterm
        set ipysession to (make new session)
        tell ipysession
            set name to "IPython"
            exec command "${which ipython}"
        end tell
    end tell
end tell
```


## IPython notebook

### 启动

在终端输入： ``ipython notebook``

IPython notebook 的使用和 Vim 有些类似，分“命令模式”和“编辑模式”，切换方法同样为 ``ESC`` 键。
在命令模式下按 ``h`` 可以呼出帮助菜单，貌似不区分大小写。


[AppleScript launcher]: https://github.com/ipython/ipython/wiki/Cookbook:-Launching-IPython-on-OSX#using-iterm2-and-an-applescript-launcher

