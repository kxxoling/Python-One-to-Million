#Misaka 与其它 Python Markdown 库简易对比

@Lepture 在自己的英文博客中对常见的 Python Markdown 库做了一些测试和对比这里简单地做一下总结。
他介绍到的，知名的 Python Markdown 库就有：
* Misaka: Sundown 的 Python 封装（依赖 CPython）
* Hoedown: Hoedown 的 Python 封装 Misaka 的继承者。
* Discount: Discount 的 Python 封装（依赖 CPython）
* cMarkdown: Python Markdown 库，依靠 C 提升性能（依赖 CPython）
* Markdown: 纯粹的 Markdown 实现，老牌 Python Markdown 库。
* Markdown2: 另一个纯粹的 Markdown 库
* mistune：作者 @Lepture

出于性能考虑，其中 Misaka Hoedown CMarkdown mistune 都是对 C 库的封装，
其中 Misaka 和 mistune 都来自 Sundown，Python 的 Hoedown 库封装自 C 的 Heodown，cMarkdown 来自于 upskirt，
而其 C 语言库 Sundown fork 自 upskirt，Sundown 停止更新后，Hoedown 又 fork 了一份，
因此 Heodown 是最具有活力的那一个，也是 Misaka 的作者所推荐的 Markdown 库。

Markdown 和 Markdown2 则是纯粹的 Python 库，虽然 Markdown2 从名字上看起来比 Markdown 要更新鲜，
而且它也宣称自己比 Markdown 快上一倍，但在 @Lepture 的测试中得出了恰恰相反的结论。
除了性能上，Markdown2 也并没有提供更多的功能。

Mistune 是 @Lepture 自己发布的 Markdown 库，提供与 Misaka 相似的接口，但是在纯 Python 环境中就能达到 Markdown 4 倍的性能，
在 CPython 的帮助下更能提升到 5 倍！

在 @Lepture 的测试中 Discount 则安装失败。

性能测试的结果如下：

    Parsing the Markdown Syntax document 1000 times...
    Mistune: 12.7255s
    Mistune (with Cython): 9.74075s
    Misaka: 0.550502s
    Markdown: 46.4342s
    Markdown2: 78.2267s
    cMarkdown: 0.664128s
    Discount is not available

测试用的源代码可以在[这里](https://github.com/lepture/mistune/blob/master/tests/bench.py)获取。
原文：[Markdown Parsers in Python--Lepture](http://lepture.com/en/2014/markdown-parsers-in-python)
