# Python 中的魔法方法

Python 中的对象都有诸如 `__init__()` 这样的方法，它们都有各自特定的用途，却
无法直接被调用。虽然无法被直接调用，实际上你却经常在使用这些方法，比如创建对象实例式
就调用了 `__new__()` 和 `__init__` 方法。那么这些魔法方法还有那些其它的有趣用法呢？


## 操作符重载

Python 中的操作符运算实际上都是在隐式地调用这些魔法方法，如果你重写了对应的魔法方法，
就能修改操作符的运算规则。比如，逻辑或操作符 `|` 对应了魔法方法 `__ror__`，
因此我们可以重载 `__ror__` 来实现类似 Shell 中的管道：

```python
class Pipe(object):
    def __init__(self, func):
        self.func = func
 
    def __ror__(self, other):
        def generator():
            for obj in other:
                if obj is not None:
                    yield self.func(obj)
        return generator()
 
@Pipe
def even_filter(num):
    return num if num % 2 == 0 else None
 
@Pipe
def multiply_by_three(num):
    return num*3
 
@Pipe
def convert_to_string(num):
    return 'The Number: %s' % num
 
@Pipe
def echo(item):
    print item
    return item
 
def force(sqs):
    for item in sqs: pass
 
nums = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
 
force(nums | even_filter | multiply_by_three | convert_to_string | echo)
```

（代码出自[酷壳网友](http://coolshell.cn/articles/10822.html)）

这里有张详细的对照表：

```
Binary Operators
Operator    Method
+   object.__add__(self, other)
-   object.__sub__(self, other)
*   object.__mul__(self, other)
//  object.__floordiv__(self, other)
/   object.__div__(self, other)
%   object.__mod__(self, other)
**  object.__pow__(self, other[, modulo])
<<  object.__lshift__(self, other)
>>  object.__rshift__(self, other)
&   object.__and__(self, other)
^   object.__xor__(self, other)
|   object.__or__(self, other)

Extended Assignments
Operator    Method
+=  object.__iadd__(self, other)
-=  object.__isub__(self, other)
*=  object.__imul__(self, other)
/=  object.__idiv__(self, other)
//= object.__ifloordiv__(self, other)
%=  object.__imod__(self, other)
**= object.__ipow__(self, other[, modulo])
<<= object.__ilshift__(self, other)
>>= object.__irshift__(self, other)
&=  object.__iand__(self, other)
^=  object.__ixor__(self, other)
|=  object.__ior__(self, other)

Unary Operators
Operator    Method
-   object.__neg__(self)
+   object.__pos__(self)
abs()   object.__abs__(self)
~   object.__invert__(self)
complex()   object.__complex__(self)
int()   object.__int__(self)
long()  object.__long__(self)
float() object.__float__(self)
oct()   object.__oct__(self)
hex()   object.__hex__(self

Comparison Operators
Operator    Method
<   object.__lt__(self, other)
<=  object.__le__(self, other)
==  object.__eq__(self, other)
!=  object.__ne__(self, other)
>=  object.__ge__(self, other)
>   object.__gt__(self, other)
```

参考：[Magic Methods and Operator Overloading](http://www.python-course.eu/python3_magic_methods.php)
