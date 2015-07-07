# 浅谈 web 框架

Python 语言简洁，开发者可以专注于逻辑的编写，因此重新造一遍轮子的代价非常之低，
这恐怕是 Python 社区之所以会出现如此众多 web 框架的主要原因 。

不过硬要评比最流行的 web 框架，当然还是要数 Django、Flask 和 Tornado，
三者在 GitHub 上的 star 数要远远超过其它竞争对手。
而且三者都特点鲜明，深受不同风格的开发者拥趸：Django 大而全，适合快速开发；
Flask 小而美，专注于自己慢慢选配插件； Tornado 性能好，支持异步、高并发。
针对不同的需求，你应该最适合自己和项目的 web 框架。

除了以上三者，其它知名框架还有很多： Twisted、web.py、web2py、bottle、Pylons。。。
飞龙非龙的博客上有[一篇比较简单的比较](http://feilong.me/2011/01/talk-about-python-web-framework)，
可以帮助你认识他们之间设计理念的区别。

个人经验上来看，Flask、Bottle、web.py 这样简单的 web 框架更适合初学者学习，
比较适合一步步进阶。而 Django 在快速开发 demo 上比较有优势（前提是你熟悉它们），
Tornado 适合开发 API 和聊天室这样高并发的应用，但要注意不要混用同步和异步的库，
否则会失去它自身的优势。

