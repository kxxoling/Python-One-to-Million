# mock

mock 测试就是在测试过程中，对于某些不容易构建或获取的对象，用虚拟的对象来代替
以便于测试的测试方法。


## 一些基本概念

double 可以理解为置换，它是所有模拟测试对象的统称，我们也可以称它为替身。
一般来说，当你创建任意一种测试置换对象时，它将被用来替代某个指定类的对象。

stub 可以理解为测试桩，它能实现当特定的方法被调用时，返回一个指定的模拟值。
如果你的测试用例需要一个伴生对象来提供一些数据，可以使用 stub 来取代数据源，
在测试设置时可以指定返回每次一致的模拟数据。

spy 可以理解为侦查，它负责汇报情况，持续追踪什么方法被调用了，以及调用过程中传递了哪些参数。
你能用它来实现测试断言，比如一个特定的方法是否被调用或者是否使用正确的参数调用。
当你需要测试两个对象间的某些协议或者关系时会非常有用。

mock 与 spy 类似，但在使用上有些许不同。spy 追踪所有的方法调用，并在事后让你写断言，
而 mock 通常需要你事先设定期望。你告诉它你期望发生什么，然后执行测试代码并验证最后的结果与事先定义的
期望是否一致。

fake 是一个具备完整功能实现和行为的对象，行为上来说它和这个类型的真实对象上一样，
但不同于它所模拟的类，它使测试变得更加容易。一个典型的例子是使用内存中的数据库来生成一个
数据持久化对象，而不是去访问一个真正的生产环境的数据库。

实践中，这些术语常常用起来不同于它们的定义，甚至可以互换，因此不必太过于陷入这些词汇的细节。
这些定义更多的是为了在高层次上区分这些概念， 它也对考虑不同类型测试对象的行为会有帮助。


## 为什么需要 mock

对于为什么需要 mock，或者什么时候需要使用 mock，Tim Mackinnon 提出了一些建议：

* 真实对象具有不可确定的行为（产生不可预测的结果，如股票行情）
* 真实对象很难被创建
* 真实对象的某些行为很难触发（比如网络错误）
* 真实情况令程序的运行速度很慢
* 真实对象有用户界面
* 测试需要询问真实对象它是如何被调用的（比如测试可能需要验证某个回调函数是否被调用了）
* 真实对象实际上并不存在（当需要和其他开发小组，或者新的硬件系统打交道时）


## Mock
``Mock`` 类是 ``ClallableMinxin`` 和 ``NonCallableMock`` 的子类，实际上 ``Mock`` 中并没有其它定义：
``class Mock(CallableMixin, NonCallableMock):pass``。

### 参数

* `spec`：这个参数是用来指定 Mock 实例的行为，那些方法是存在的，那些不存在。
  其值可以是一个类或实例，也可以是一个字符串列表。

* `spec_set`：比起 ``spec`` 更加严格。

* `side_effect`：在 Mock 实例被调用时被调用的方法，对应 ``side_effect``属性，可以用来返回动态值或者
  异常。其参数和 mock 相同，如果返回值不为 ``DEFAULT`` 则用作 Mock 实例的返回值。

  如果 ``side_effect`` 是一个迭代器，则每次调用的时候返回其中下一个元素。如果迭代器中的元素是异常
  则将其抛出而非返回。

* ``return_value``: mock 对象被调用时的返回值，默认是一个新的 ``Mock`` 实例。

* ``wraps``: Item for the mock object to wrap. If ``wraps`` is not None then
  calling the Mock will pass the call through to the wrapped object
  (returning the real result). Attribute access on the mock will return a
  Mock object that wraps the corresponding attribute of the wrapped object
  (so attempting to access an attribute that doesn't exist will raise an
  ``AttributeError``).

  If the mock has an explicity ``return_value`` set then calls are not passed
  to the wrapped object and the ``return_value`` is returned instead.
  如果 Mock 实例存在 ``return_value`` ，不会调用被封装的对象。

* ``name``：Mock 对象在 repr 时的名字，调试时会很有帮助。该参数会传递给子 mock。

Mocks can also be called with arbitrary keyword arguments. These will be
used to set attributes on the mock after it is created.


### 属性

* ``call_args``
* ``call_args_list``
* ``call_count``
* ``called``
* ``return_value``
* ``side_effect``

### 方法

* ``attach_mock`` Attach a mock as an attribute of this one, replacing its name and
  parent. Calls to the attached mock will be recorded in the
  `method_calls` and `mock_calls` attributes of this one.
  * Set attributes on the mock through keyword arguments.
  Attributes plus return values and side effects can be set on child
  mocks using standard dot notation and unpacking a dictionary in the
  method call:
  ```python
  >>> attrs = {'method.return_value': 3, 'other.side_effect': KeyError}
  >>> mock.configure_mock(**attrs)
  ```
* ``mock_add_spec`` Add a spec to a mock. `spec` can either be an object or a
  list of strings. Only attributes on the `spec` can be fetched as
  attributes from the mock.
  If `spec_set` is True then only attributes on the spec can be set.
* ``reset_mock``


## MogicMock

MagicMock 是 Mock 的子类，和 Mock 的不同之处在于 MagicMock 默认已经 mock 了对象的魔术方法（magic method）。
推荐使用 MagicMock。


## patch

``patch( target, new=DEFAULT, spec=None, create=False, spec_set=None, autospec=None, new_callable=None, **kwargs)``
有两种主要用法：

装饰器：

```python
@patch.object(SomeClass, 'attribute', sentinel.attribute)
def test():
    assert SomeClass.attribute == sentinel.attribute
```

和上下文管理器：

```python
with patch('__builtin__.open', mock):
    handle = open('filename', 'r')
```

两种情况下都保证仅仅在函数体内或者 with 表达式中，`target` 被 `new` 所替代。
当函数执行完或者离开 with 环境时，`target` 恢复回去。

`new` 默认是一个 `MagicMock` 对象
If `patch` is used as a decorator and `new` is
omitted, the created mock is passed in as an extra argument to the
decorated function. If `patch` is used as a context manager the created
mock is returned by the context manager.

`target` should be a string in the form `'package.module.ClassName'`. The
`target` is imported and the specified object replaced with the `new`
object, so the `target` must be importable from the environment you are
calling `patch` from. The target is imported when the decorated function
is executed, not at decoration time.

The `spec` and `spec_set` keyword arguments are passed to the `MagicMock`
if patch is creating one for you.

In addition you can pass `spec=True` or `spec_set=True`, which causes
patch to pass in the object being mocked as the spec/spec_set object.

`new_callable` allows you to specify a different class, or callable object,
that will be called to create the `new` object. By default `MagicMock` is
used.

A more powerful form of `spec` is `autospec`. If you set `autospec=True`
then the mock with be created with a spec from the object being replaced.
All attributes of the mock will also have the spec of the corresponding
attribute of the object being replaced. Methods and functions being
mocked will have their arguments checked and will raise a `TypeError` if
they are called with the wrong signature. For mocks replacing a class,
their return value (the 'instance') will have the same spec as the class.

Instead of `autospec=True` you can pass `autospec=some_object` to use an
arbitrary object as the spec instead of the one being replaced.

By default `patch` will fail to replace attributes that don't exist. If
you pass in `create=True`, and the attribute doesn't exist, patch will
create the attribute for you when the patched function is called, and
delete it again afterwards. This is useful for writing tests against
attributes that your production code creates at runtime. It is off by by
default because it can be dangerous. With it switched on you can write
passing tests against APIs that don't actually exist!

Patch can be used as a `TestCase` class decorator. It works by
decorating each test method in the class. This reduces the boilerplate
code when your test methods share a common patchings set. `patch` finds
tests by looking for method names that start with `patch.TEST_PREFIX`.
By default this is `test`, which matches the way `unittest` finds tests.
You can specify an alternative prefix by setting `patch.TEST_PREFIX`.

Patch can be used as a context manager, with the with statement. Here the
patching applies to the indented block after the with statement. If you
use "as" then the patched object will be bound to the name after the
"as"; very useful if `patch` is creating a mock object for you.

`patch` takes arbitrary keyword arguments. These will be passed to
the `Mock` (or `new_callable`) on construction.

`patch.dict(...)`, `patch.multiple(...)` and `patch.object(...)` are
available for alternate use-cases.

### 猴子补丁（monkey patch）

``patch`` 本质上是一个函数，但是在实现的时候通过 MP 添加了很多属性：

```python
patch.object = _patch_object
patch.dict = _patch_dict
patch.multiple = _patch_multiple
patch.stopall = _patch_stopall
patch.TEST_PREFIX = 'test'
```


## 例子

[IPython notebook 在线示例](https://github.com/kxxoling/Python-One-to-Million/blob/ipynb/testing/mock.ipynb)


## 参考文章

* [使用模拟对象（Mock Object）技术进行测试驱动开发](https://www.ibm.com/developerworks/cn/java/j-lo-mockobject/)
* [置换测试: Mock, Stub 和其他](http://objccn.io/issue-15-5/)
* [PyPI - mock](https://pypi.python.org/pypi/mock)
* [mock - getting started](http://www.voidspace.org.uk/python/mock/getting-started.html)

