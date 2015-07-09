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


## Django

说到 Python web 框架，绝对绕不过去的就是 Django 了，类似于 Ruby 社区的 RoR，
两者都擅长 10 分钟搭建博客，喜欢自己提供一套轮子。即使如此，仍然免不了使用第三方库，
两者开发进度上的差异往往会带来痛苦的兼容性问题。顺道一提，Django 的长期支持版本间隔为 0.4，
也就是说 Django 1.x 中的长期支持版本包括 1.0，1.4，1.8 。

Django 的快速开发性质使得总有人将它和 WordPress 相提并论，不过 Django 的抽象度明显更高，
是 web 框架基本的。Django 社区也有类似于 WordPress 的产品，而且并不少，因为 Django
本身就是做 CMS 系统出身的。


## Flask

Flask 像是 Django 的精简版本，设计思路上也有很多和 Django 类似的地方，其第三方库充斥着
Django 功能的仿制品，因此使用起来更像是定制版的 Django。

Flask 官方自称是“微框架”，这一点我不太认同，这种说法有误导嫌疑。虽然 Flask 的代码很精简，
但是它极度依赖于 Werkzeug 中间件和 Jinja2 模版库，并不是所有功能都是自身精简的代码所提供，
所以我并不承认它和 Bottle、Pylons 一样能被称为微框架。


## Tornado

Tornado 和上面两者的差异就比较大了，它的特点在于轻量、异步、高性能。轻量是因为它的代码很精简，
主要只提供了网络方面功能的封装（原先还自带了一个异步 MySQL 连接库，后来也剥离出去了）。
高性能主要来自于它的异步实现，相对于同步框架，异步非阻塞能够处理更多的并发连接。
异步自然是 Tornado 最大的特性了。

Tornado 的异步实现称为 epoll，Linux 下依赖 libev，BSD 和 Mac 下可以用 kqueue 代替，
在 Windows 下并没有对应的实现。是的，这里说到了一些系统底层的实现，这是因为 WSGI 规范
并没有提供异步特性的支持，因此为了使用异步特性，Tornado 框架必须运行在支持异步的 web
服务器应用上，Tornado 自带的 server 就是一个很好的选择，其它替代还有 Twisted server。
