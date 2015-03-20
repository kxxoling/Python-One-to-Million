# 单例模式

> 单例模式，也叫单子模式，是一种常用的软件设计模式。在应用这个模式时，
> 单例对象的类必须保证只有一个实例存在。许多时候整个系统只需要拥有一个
> 全局对象，这样有利于我们协调系统整体的行为。
> －－以上来自[维基百科](http://zh.wikipedia.org/wiki/%E5%8D%95%E4%BE%8B%E6%A8%A1%E5%BC%8F)

从定义上来看，这会是一个很有用的避免冲突的设计模式，相当于把所有同样资源的调用
都交给了一个资源代理。那么 Python 中该如何实现这一模式呢？


## 装饰器

所有资源资源调用者都是同一个对象，我首先想到的就是装饰器，可以很方便的
给不同的对象增添相同的功能。

[Python 官方 wiki](https://wiki.python.org/moin/PythonDecoratorLibrary#Singleton)
给出了一个非常优雅的实现：

```python

def singleton(cls):
    instance = cls()
    instance.__call__ = lambda: instance
    return instance

# Sample use

@singleton
class Highlander:
    x = 100
    # Of course you can have any attributes or methods you like.


highlander = Highlander()
another_highlander = Highlander()
assert id(highlander) == id(another_highlander)

```

上面的代码定义了一个 singleton 装饰器，覆盖了类的 `__call__` 方法，
该方法会在类创建的时候创建一个类的实例，并在之后类每次的实例化时总是返回这个实例对象。

当然，如果你希望只在需要的时候创建类的实例对象也有别的方法：

```python

def singleton(cls, *args, **kw):
    instances = {}
    def _singleton():
        if cls not in instances:
            instances[cls] = cls(*args, **kw)
        return instances[cls]
    return _singleton

@singleton
class MyClass(object):
    a = 1
    def __init__(self, x=0):
        self.x = x

one = MyClass()
two = MyClass()

assert id(one) == id(two)

```

上面的代码中实现了这样一个装饰器：装饰器函数创建的时候会创建一个 instances 字典，
该字典用于保存被装饰器修改过的类的实例，在类初始化的时候首先判断是否存在其实例，
如果存在则直接返回，否则创建一个新的实例保存到 instances 字典中，并返回该实例。
（这段代码出自 [cnblogs](http://www.cnblogs.com/goodhacker/p/3366618.html)）


## metaclass

我自己对于不是很喜欢装饰器的实现，因为从语言逻辑上来看，我需要的是一个
有单例特性的类而不是为类添加单例限制。于是我又找到了基于 metaclass 的实现：

```python

class Singleton(type):
    def __init__(cls, name, bases, dict):
        super(Singleton, cls).__init__(name, bases, dict)
        cls._instance = None
    def __call__(cls, *args, **kw):
        if cls._instance is None:
            cls._instance = super(Singleton, cls).__call__(*args, **kw)
        return cls._instance

class MyClass(object):
    __metaclass__ = Singleton

one = MyClass()
two = MyClass()

```

上面的代码在类的第一次实例之后将这个个实例作为其类变量保存，在之后调用类的构造函数的时候
都直接返回这个实例对象。

这个解决方案强化了类与其单例之间的内聚性。
