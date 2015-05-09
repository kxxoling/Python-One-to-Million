# BPython

相比 IPython，BPython 并不算得上功能强大，它的开发初衷仅在于部分增强原始 REPL 的交互性，
并没有向 IDE 发展的意思，以保持轻量、简单为目的。
根据[官网](http://www.bpython-interpreter.org/about.html)介绍，它有这些特性：


## 行内的语法高亮


## 自动提示


## 函数参数提示


## 时光倒流（Rewind）

这里需要注意一下，BPython 的 rewind 并不是撤销，而是将之前执行过的命令在全新的环境中重新执行到上一步。


## 历史输出

退出 BPython 时会将屏幕中所有的信息输出到 stdout，也就是说你可以在 shell 中看到之前看过的一切。


## 支持 Python 3


## 自定义配置

```
bpython --config=/path/to/a/bpython/config/file
```

