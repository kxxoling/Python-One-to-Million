# uWSGI

虽然 [uWSGI] 最常见的使用场景是当作 WSGI 服务器，但它其实还提供了更多的其它功能，[这篇博客](https://lincolnloop.com/blog/uwsgi-swiss-army-knife/)
中介绍了 uWSGI 如同瑞士军刀般强大的各项功能。


## 安装指南

官方提供了多种安装方式，比如使用 pip 安装 uWSGI 安装包： ``pip install uwsgi``。

或者使用安装脚本：

```sh
curl http://uwsgi.it/install | bash -s default /tmp/uwsgi
```

或者编译安装最新版本：

```sh
wget http://projects.unbit.it/downloads/uwsgi-latest.tar.gz
tar zxvf uwsgi-latest.tar.gz
cd <dir>
make
```


## 简单使用

以一个简单的 uwsgi 命令为例介绍它的常见用法：

```
uwsgi --http :9090 --wsgi-file foobar.py --master --processes 4 --threads 2 --stats 127.0.0.1:9191
```

* ``--http :9090`` 是指监听端口 9090 的 HTTP 请求。
* ``--wsgi-file foobar.py`` 是指从文件 ``foobar.py`` 中寻找 WSGI 应用对象，默认寻找 ``application`` 对象。
* ``--processes 4 --threads 2`` 指 uWSGI 启动 4 个进程，每个进程启动 2 个线程。
* ``--stats 127.0.0.1:9191`` 提供了 uWSGI 状态监视端口，可以安装 uwsgitop 来查看 uWGI 进程状态。


[uWSGI]: https://uwsgi-docs.readthedocs.org/

