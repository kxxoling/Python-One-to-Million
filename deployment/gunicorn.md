# Gunicorn

[Gunicorn] 全称 Green Unicorn，是 Ruby 服务器 Unicorn 的 Python 版本，虽然完全是由 Python
实现，但是拥有作为 WSGI 服务器性能优秀，是现在最流行的 WSGI 服务之一。


## 安装指南

虽然在很多 Linux 操作系统上都有 Gunicorn 的安装包，但通常还是建议使用 Python 包管理工具
``easy_install`` 或者 ``pip`` 来安装最新版本的 Gunicorn：

```sh
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


[Gunicorn]: http://gunicorn.org/

