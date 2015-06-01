# 测试


对于一个开源项目来说，文档和测试都是必不可少的组成部分，没有足够测试和文档覆盖率的
“开源项目”就是一坨垃圾！当然，对于某些能够做到自文档的大神来说，
文档可以是不必要的，但测试依旧是代码质量的保证。

优秀的测试通常遵循一下基本规章：

1. 每个测试单元应该关注于一个功能，并保证其正确性。

2. 测试单元之间应该尽可能独立，也就是说可以独立运行，与顺序无关。

3. 测试的速度应该尽可能快，过慢的测试速度会成为开发的瓶颈。对于耗费时间很长的重型测试，应该将其独立出来。

4. 在集中编程前后都应该完整地运行一遍测试，以保证不会造成意外的破坏。

5. 在编程过程中，如果需要中断工作，那么编写一个不能运行的测试对于恢复工作非常有帮助。

6. debug 的第一步就是写一个针对性的单元测试，虽然这做起来并不一定容易，但却非常有价值。

7. 虽然 PEP8 提倡简短的命名，但在测试函数名称应该长而有意义。比如，编程中你可能使用 ``square()`` 甚至 ``sqr()``
   这样的函数名称，但是在测试中你应该写成：``test_square_of_number_2()``, ``test_square_negative_number()``。

8. 对于新成员来说，阅读测试代码可能是他们了解系统的最快途径之一，热点、难点、边界情况都会一目了然。
   因此，加入新功能的第一步应该是编写一个对应的单元测试。


## 基本概念

### 单元测试

[单元测试](https://zh.wikipedia.org/wiki/%E5%8D%95%E5%85%83%E6%B5%8B%E8%AF%95)是针对程序最小模块单位
进行正确性检验的测试工作。最小单位通常是函数或者方法。理想情况下，每一个单元测试应该独立于其它用例。
单元测试通常由软件开发人员编写，用于确保他们所写的代码符合软件需求和遵循开发目标。

在自动化测试时，为了实现隔离的效果，测试将脱离待测程序单元（或代码主体）本身固有的运行环境之外，
即脱离产品环境或其本身被创建和调用的上下文环境，而在测试框架中运行。
以隔离方式运行有利于充分显露待测试代码与其它程序单元或者产品数据空间的依赖关系。
这些依赖关系在单元测试中可以被消除。隔离模块经常会使用 stubs、mock 或 fake 等测试马甲程序。


### 集成测试

整合测试又称组装测试，即对程序模块采用一次性或增殖方式组装起来，对系统的接口进行正确性检验的测试工作。
整合测试一般在单元测试之后、系统测试之前进行。实践表明，有时模块虽然可以单独工作，
但是并不能保证组装起来也可以同时工作。


### 系统测试

系统测试是将需测试的软件，作为整个基于计算机系统的一个元素，
与计算机硬件、外设、某些支持软件、数据和人员等其他系统元素及环境结合在一起测试。
在实际运行(使用)环境下，对计算机系统进行一系列的组装测试和确认测试。
系统测试的目的在于通过与系统的需求定义作比较，发现软件与系统定义不符合或与之矛盾的地方。


## 基本工具

### doctest

Python 还提供了一个叫做 [doctest](https://docs.python.org/2/library/doctest.html)
的工具，写法如下：

```python
"""
一个最简单的 doctest 写法，我这种缩进是为了照顾 Sphinx 文档自动生成工具::

    >>> factorial(5)
    120
"""

def factorial(n):
    """依旧是 doctest，不过更加复杂::

        >>> [factorial(n) for n in range(6)]
        [1, 1, 2, 6, 24, 120]
        >>> [factorial(long(n)) for n in range(6)]
        [1, 1, 2, 6, 24, 120]
        >>> factorial(30)
        265252859812191058636308480000000L
        >>> factorial(30L)
        265252859812191058636308480000000L
        >>> factorial(-1)
        Traceback (most recent call last):
            ...
        ValueError: n must be >= 0

        Factorials of floats are OK, but the float must be an exact integer:
        >>> factorial(30.1)
        Traceback (most recent call last):
            ...
        ValueError: n must be exact integer
        >>> factorial(30.0)
        265252859812191058636308480000000L

        It must also not be ridiculously large:
        >>> factorial(1e100)
        Traceback (most recent call last):
            ...
        OverflowError: n too large
    """
    import math

    if not n >= 0:
        raise ValueError("n must be >= 0")
    if math.floor(n) != n:
        raise ValueError("n must be exact integer")
    if n+1 == n:  # catch a value like 1e300
        raise OverflowError("n too large")
    result = 1
    factor = 2
    while factor <= n:
        result *= factor
        factor += 1
    return result

if __name__ == "__main__":
    import doctest
    doctest.testmod()
```

如果不在代码中显式 ``import doctest`` 也可以在运行文件的时候输入这样的命令： ``python -m doctest -v filename.py``。

从上面的示例代码中也可以看出，doctest 并便于不提供完整的边界数据测试的支持，因此并不能完全替代单元测试。


### unittest 和 unittest2

Python 自带了 [unittest](https://docs.python.org/2/library/unittest.html) 库，
是 Java JUnit 库的 Python 实现，虽然很好用，但我还是想在这里吐槽一下驼峰式命名的方法。
在 Python 2.7 版本以后，unittest.TestCase 类自带了 ``assertListEquel()`` 等方法，
非常便利，也是我不愿意兼容 Python 2.6 的重要原因。

附即将弃用的方法对照表：

| 方法名                    | 即将弃用的方法名              |
|---------------------------|-------------------------------|
| assertEqual()             | failUnlessEqual, assertEquals |
| assertNotEqual()          | failIfEqual                   |
| assertTrue()              | failUnless, assert_           |
| assertFalse()             | failIf                        |
| assertRaises()            | failUnlessRaises              |
| assertAlmostEqual()       | failUnlessAlmostEqual         |
| assertNotAlmostEqual()    | failIfAlmostEqual             |

[unittest2](http://www.voidspace.org.uk/python/articles/unittest2.shtml)
是 unittest 的增强版本，几乎完全兼容 unittest 的接口，升级时只需要将 ``import unittest``
替换为 ``import unittest2`` 即可，提供的新方法更强大也更严谨。


### py.test


## web 相关

对于 web 功能的测试，最简单的可以使用 ``urllib2.get(url)``，然后测试输出的 HTML 结果是否符合预期。
当然针对每一个功能都这样写未免太过低效，因此知名 web 框架大多有专门的测试库提供测试：

* Django 内置了 [django.test](https://docs.djangoproject.com/en/1.8/topics/testing/overview/)
* Tornado 内置了 [tornado.testing](http://tornado.readthedocs.org/en/latest/testing.html)
* Flask 可以使用 [werkzeug.test](http://werkzeug.pocoo.org/docs/0.10/test/#werkzeug.test.Client)
  和第三方的 [Flask-Testing](https://pythonhosted.org/Flask-Testing/)


### Django

Django 的启动互相之间的依赖严重，大部分文件都不能单独执行，测试时建议使用封装后的工具，
如： `django.test`、`django_nose` 等等。


### Flask

Flask 在写测试的时候需要主要 ``app_context`` 和 ``request_context`` 中的[陷阱](http://flask.pocoo.org/docs/0.10/appcontext/)。


### Tornado

Tornado 的 testing 库很简陋，主要是针对自身异步特性封装了一些工具。


### 浏览器

浏览器端的测试自动化最常用的还是 [Selenium](http://www.seleniumhq.org/)，Python 版本的
[文档](https://selenium-python.readthedocs.org/)并不复杂。示例代码：

```python
import unittest
from selenium import webdriver

class TestOne(unittest.TestCase):

    def setUp(self):
        self.driver = webdriver.Firefox()          # 初始化浏览器，也可以选择 Chrome 或者 PhanatomJS
        self.driver.set_window_size(1280, 550)

    def test_url(self):
        self.driver.get("http://duckduckgo.com/")
        self.driver.find_element_by_id(
            'search_form_input_homepage').send_keys("realpython")
        self.driver.find_element_by_id("search_button_homepage").click()
        self.assertIn(
            "https://duckduckgo.com/?q=realpython", self.driver.current_url
        )

    def tearDown(self):
        self.driver.quit()

if __name__ == '__main__':
    unittest.main()
```

上面的代码会启动浏览器（这里设置的是 Firefox），并触发浏览器事件模拟用户输入。对于没有浏览器的机器，
比如服务器或，可以配置远程 Selenium server 或者使用 Headless 的 PhantomJS 代替。使用 Headless 浏览器
因为减少了打开和关闭浏览器的时间，因此在测试效率上也更高一些。

使用之后感触最深的是错误提示不够丰富，基本上只能断定页面结果并不符合预期，结果反馈跟 `unittest.TestCase` 简直天壤之别。


## 其它工具

### nose


### tox

发布独立库的时候通常要考虑不同版本间兼容性的问题，虽然可以通过 virtualenv 实现环境的模拟，
但毕竟很不方便，[tox](http://tox.readthedocs.org/) 正是解决这一问题的工具。

tox 简化了 virtualenv 的管理，提供了简便的配置。我常用的配置是这样的：

```ini
# 文件 tox.ini 的内容，需要和 setup.py 置于同一目录
[tox]
envlist = py26,py27
[testenv]
deps=      		 # 测试依赖
commands=make test     	# 执行测试的命令
```

### mock

[mock](http://www.voidspace.org.uk/python/mock/) 是一个测试库，提供模拟对象供测试用例使用。
[Python 3 以后](https://docs.python.org/3/library/unittest.mock.html#module-unittest.mock)，
mock 已经加入标准库，调用方法是 ``from unittest import mock``。


## CI

* [travis CI](https://travis-ci.org/)   对于开源项目免费
* [GitLab CI](https://ci.gitlab.com)    免费，提供本地部署支持


## 如何提高测试速度

如果测试很耗费时间，很容易引起开发人员的不满，因而怠于编写测试，所以说提高测试速度对于落实测试来说十分重要。
总结了一些提升测试效率的方法：

1. 合理使用 ``setUpClass`` 和 ``tearDownClass`` 方法。作为类方法，在拥有多个测试方法时也只会在一个测试用例中执行一次。
2. 数据库很慢，避免使用数据库。一定需要的话，请使用内存数据库（比如 SQLite）。
3. 使用 [mock](http://mock.readthedocs.org/en/latest/getting-started.html)，避免使用 model。
4. 如果测试写起来很困难，说明需要重构了。
5. Celery 可以使用如下配置:
   ```
   CELERY_ALWAYS_EAGER = True
   CELERY_EAGER_PROPAGATES_EXCEPTIONS = True
   BROKER_BACKEND = 'memory'
   ```
6. django.test.utils.override_settings
7. 关闭调试和日志
8. 删除不必要的中间件和app

这部分建议对于 Python 项目基本上也适用。


## 参考链接

* [Introduction to Python/Django tests](http://django-testing-docs.readthedocs.org/)
* [DjangoCon 2013 - How to Write Fast and Efficient Unit Tests in Django](http://www.slideshare.net/cordiskinsey/djangocon-2013-how-to-write-fast-and-efficient-unit-tests-in-django)
* [Testing and Django](http://carljm.github.io/django-testing-slides)

