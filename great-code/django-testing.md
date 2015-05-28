# 如何测试 Django 应用？

Django 的启动互相之间的依赖严重，很多参数和依赖都需要在运行的时候导入，导致大部分文件都不能单独执行。
不过 Django 的社区非常活跃，对于知名的测试框架都有进行封装，如： `django.test`、`django_nose` 等等，
以配合自身的测试命令使用。


## doctest

在 Flask 中测试一个文件的 doctest 只需要运行：``python filename.py``，然而这在 Django 中行不通。
在 Django 中依赖自身的 test 命令：``python manage.py test[ app_name]``，其中 ``app_name`` 若为空
默认测试所有应用。[在 1.6 及以后版本中](https://docs.djangoproject.com/en/1.6/releases/1.6/#new-test-runner)，
需要首先在 ``settings.py`` 中指定 ``TEST_RUNNER``：

```python
INSTALLED_APPS = (
    ...
    'django_nose',
)
TEST_RUNNER = 'django_nose.NoseTestSuiteRunner'
NOSE_ARGS = ['--with-doctest']
```


## TestCase

Django 的 `TestCase` 类是 `unittest.TestCase` 的子类，使用起来非常相似。


## Fixture

Fixture 是 unittest 提供读取测试数据的一种方式，在 Django 的 TestCase 中也可以直接使用，使用前需要导出数据：

`python manage.py dumpdata --format=yaml --indent=4 > fixtures_dir/filename.yaml`

支持的数据格式包括 YAML、JSON 等等，YAML 可读性较高，不过需要安装额外的依赖。

配合 testserver 命令启动：

```
python manage.py testserver fixtures_dir/filename.yaml
```

在测试用例中指定：

```python
from django.test import TestCase
from django.contrib.auth import authenticate

class LoginTest(TestCase):
    fixtures = ['mysite.yaml']

    def setUp(self):
        # 导入 fixture 中用户数据，省去创建用户的流程，也免去了清除用户数据的流程。

    def test_has_user(self):
        # 如果已导入 fixture 中数据，则可以使用其中的账号登录。
        self.assertIsNotNone(authenticate(username='windrunner', password='password'))
```


## Client

Client 提供了用户代理的模拟，其使用类似于 requests 库，不过使用前需要先初始化：``client = Client()``，
Client 默认会提供 CSRF 认证，如果需要手动验证 CSRF，需要这样初始化：
``csrf_enabled_client = Client(enforce_csrf_checks=True)``。

```python
import unittest
from django.test.client import Client

class PageTest(unittest.TestCase):
    def setUp(self):
        self.client = Client()

    def test_home(self):
        res = self.client.get('/')
        self.assertEqual(200, res.status_code)

    def test_login(self):
        """普通测试。client 实例会自动解决 csrf 问题。"""
        res = self.client.get('/login/')

        self.assertEqual(200, res.status_code)
        self.assertIn('Username', res.content)

        res_post = self.client.post('/login/', {'username': 'windrunner', 'password': 'password', })

        self.assertEqual(200, res_post.status_code)
        self.assertIn('windrunner', res_post.content)

    def test_login_csrf(self):
        """强制 csrf 检查"""
        self.client = Client(enforce_csrf_checks=True)          # 使用检查 CSRF 的 Client 示例代替默认实例
        res = self.client.get('/login/')
        csrf_token = '%s' % res.context['csrf_token'] 			# 获取 csrf_token

        res_fail = self.client.post('/login/', {'user': 'windrunner', 'pass': 'password', })
        self.assertEqual(403, res_fail.status_code) 			# 没有处理 CSRF token 会返回 403 错误代码

        res_csrf = self.client.post('/login/', {'user': 'windrunner', 'pass': 'password', 'csrfmiddlewaretoken': csrf_token, })
        self.assertIn('windrunner', res_csrf.content)

    def test_logout(self):
        res = self.client.post('/logout/')
        self.assertEqual(302, res.status_code)
```


## testserver

testserver 是 Django 提供的启动测试服务器的方法，会创建一个测试数据库来替代默认数据库，
通常会在启动时导入相应 fixture。命令如下：

```
python manage.py testserver --addrport 7000 fixture1 fixture2
```


## Selenium

因为 Selenium 是控制浏览器测试 web 服务，因此并不会受到 Django 的干扰，这里有一段示例代码：

```python
import unittest
from selenium import webdriver
from django.contrib.auth import get_user_model, authenticate

class LoginTest(unittest.TestCase):
    def setUp(self):
        self.browser = webdriver.Firefox()          # 初始化浏览器，也可以选择 Chrome 或者 PhanatomJS

    def tearDown(self):
        self.browser.quit()                         # 测试结束后关闭浏览器

    def _login(self):
        # 这个方法没有以 ``test`` 开始，因此并不会单独被执行。
        self.browser.get('http://localhost:8000/login')         # 发送 GET 请求并打开页面

        # 使用浏览器的选择权选中 HTML 元素，并发送浏览器事件，复杂的元素选择可以借助 XPath
        self.browser.find_element_by_id('username').send_keys('windrunner')
        self.browser.find_element_by_id('password').send_keys('password')
        self.browser.find_element_by_id('submit').click()   # 触发点击事件

    def test_login(self):
        self._login()
        self.assertIn('windrunner', self.browser.page_source)   # 断言登录后的页面内容

    def test_logout(self):
        self._login()
        self.assertIn('windrunner', self.browser.page_source)
        self.browser.get('http://localhost:8000/logout')
        self.assertIn('nobody', self.browser.page_source)
        self.assertNotIn('windrunner', self.browser.page_source)
```

