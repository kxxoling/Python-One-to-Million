# Django Shell 增强

由于 Django 系统的特殊性，很难在 Shell 中直接导入 Django 应用，而只能使用它自己提供的
shell 命令。不过，并不是说就没有办法使用增强的第三方 Shell 了，[Django-Extensions]
插件提供了切换默认 Shell 的能力。


## Django Extensions

[Django-Extensions] 是一个 Django 第三方插件集，其中囊括了很多实用的 Django 插件，
当然也包括本文的主角 ``shell_plus``。


## shell_plus

装上插件后只需要将 ``django_extensions`` 加入 ``INSTALLED_APPS`` 列表中即可使用
IPython 作为默认 Shell（如果已安装的话）。
需要注意的是，如果在 virtualenv 环境中使用 Django 的话，同样需要在 virtualenv 中安装 IPython。

如果你偏好的是 BPython 的话，[shell_plus] 同样提供了相关命令：``shell_plus``。使用方法：

```shell
./manage.py shell_plus --bpython
```

该命令对 IPython 同样适用：

```shell
./manage.py shell_plus --ipython
```

或者，有需要的话，也可以切换回原生 Python：

```shell
./manage.py shell_plus --plain
```

默认 Shell 的选择顺序是 BPython、IPython、Python。

你同样可以在文件中配置默认 Shell：

```python
SHELL_PLUS = "ipython"
```


## 设置

默认情况下 shell_plus 会自动加载你的全部 Model，其中可能会出现重名或者其它问题，
你可以通过配置文件来修改它的加载机制。

注意：这里的设置只会作用于 shell_pull 而不会影响你的整改系统。

将名为 blog 的 app 中 的 Messages 模块以 blog_messages 名加载
```python
SHELL_PLUS_MODEL_ALIASES = {'blog': {'Messages': 'blog_messages'},}
```

不加载 app 'sites' 中的全部 model，以及 app 'blog' 中的 'pictures' Model。

```python
SHELL_PLUS_DONT_LOAD = ['sites', 'blog.pictures']
```

``SHELL_PLUS_MODEL_ALIASES`` 和 ``SHELL_PLUS_DONT_LOAD`` 可以同时使用。

也可以像这样在运行 manage.py 时再设定不自动加载的模块：

```shell
./manage.py shell_plus --dont-load app1 --dont-load app2.module1
```

命令行和配置文件中的设置会合并后再生效。

## IPython Notebook

shell_plus 同时还支持 [IPython Notebook]：

```python
./manage.py shell_plus --notebook
```

Notebook 相关的设置参数有两个：``NOTEBOOK_ARGUMENTS`` 和 ``IPYTHON_ARGUMENTS``：


``NOTEBOOK_ARGUMENTS`` 用于配置 IPython Notebook：

```shell
ipython notebook -h
```

```pyton
NOTEBOOK_ARGUMENTS = [
    '--ip=x.x.x.x',
    '--port=xx',
]
```

``IPYTHON_ARGUMENTS`` 用于设置 IPython：

```shell
ipython -h
```

使用 IPython Notebook 时同样会自动加载 Django settings 模块和数据库 Model。
其实现依赖于定制的 IPython 插件，Django Extensions 默认会向 IPython Notebook 发送命令
``--ext django_extensions.management.notebook_extension`` 开启该功能，
你可以在 Django 设置中修改 ``IPYTHON_ARGUMENTS`` 参数覆盖该设置：

```python
IPYTHON_ARGUMENTS = [
    '--ext', 'django_extensions.management.notebook_extension',
    '--ext', 'myproject.notebook_extension',
    '--debug',
]
```

开启自动加载可以通过引用 Django Extensions 默认 Notebook，或者复制它的自动加载代码到你自己的扩展中。

IPython Notebook 扩展目前并不支持 ``--dont-load`` 选项。


[Django-Extensions]: http://django-extensions.readthedocs.org/
[shell_plus]: http://django-extensions.readthedocs.org/en/latest/shell_plus.html#interactive-python-shells

