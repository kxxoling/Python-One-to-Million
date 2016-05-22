# Gunicorn

[Gunicorn] 全称 Green Unicorn，是 Ruby 服务器 Unicorn 的 Python 版本，虽然完全是由 Python
实现，但是拥有作为 WSGI 服务器性能优秀，是现在最流行的 WSGI 服务之一。


## 安装指南

虽然在很多 Linux 操作系统上都有 Gunicorn 的安装包，但通常还是建议使用 Python 包管理工具 ``pip`` 来安装最新版本的 Gunicorn：

```sh
[sudo ]easy_install pip
pip install gunicorn
```


## 简单使用

Gunicorn 的使用非常简单。

假设你的 WSGI 应用包叫做 ``myapp``，包中有一个符合 WSGI Application 规范的 ``app``
对象，就可以这样部署启动 Gunicorn 服务器来托管应用：

```sh
gunicorn -w 4 myapp:app
```

上面命令的 ``-w 4`` 是指启动 4 个 worker 进程，用以提高服务器的负载能力。

### 常用参数

* ``-c CONFIG, --config=CONFIG`` - 指定配置文件，格式可以是
  ``$(PATH)``、``file:$(PATH)`` 或者 ``python:$(MODULE_NAME)``。
* ``-b BIND, --bind=BIND`` - 指定 socket 地址，可以是
 ``$(HOST)``、``$(HOST):$(PORT)`` 或者 ``unix:$(PATH)`` 格式。``$(HOST)`` 可以是单纯的 IP 地址。
* ``-w WORKERS, --workers=WORKERS`` - worker 进程数。推荐数量为服务器 CPU 核数的 2-4 倍。
* ``-k WORKERCLASS, --worker-class=WORKERCLASS`` - worker 进程的类型，详细介绍请查看产品文档。你可以在这里设置 ``$(NAME)`` ，其值可以是 ``sync``、``eventlet``、``gevent`` 或者
  ``tornado``、``gthread``
  ``gaiohttp``。默认是 ``sync``。
* ``-n APP_NAME, --name=APP_NAME`` - 如果安装了 setproctitle，你可以使用该参数设置 Gunicorn 的进程名。

## 部署实例

Gunicorn 虽然是一个性能优秀的 WSGI 服务器，但是并不擅长静态资源管理，在生产环境下通常会配合
Nginx 使用。由 Nginx 作为服务器前端，将动态请求通过反向代理传递给 Gunicorn。Nginx
反向代理配置示例：

```nginx
server {
  listen 80;
  server_name example.org;
  access_log  /var/log/nginx/example.log;

  location / {
      proxy_pass http://127.0.0.1:8000;
      proxy_set_header Host $host;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
  }
}
```


## 配置

参考：http://docs.gunicorn.org/en/latest/configure.html



[Gunicorn]: http://gunicorn.org/
[设计理念]: http://docs.gunicorn.org/en/latest/design.html
