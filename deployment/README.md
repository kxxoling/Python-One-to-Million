# 部署

根据和代码/系统的紧密程度，我们可以简单地把部署分为应用级和系统级。
应用级通常提供把 HTTP 请求递交给应用，而系统级和运维的关系更为紧密。

下面是一个非常常见的 Python 应用部署架构：

* [nginx] ：静态文件服务；SSL 负载转移；反向代理；
* [Memcached] / [Redis] ：缓存；
* [Celery] ：运行后台任务；
* [Redis] / [RabbitMQ] ：任务队列（通常对接 Celery）；
* [uWSGI] / [Gunicorn] ：WSGI 服务器；


## web 应用服务器

### uWSGI

[uWSGI](uwsgi.html) 是一个主要以 C 语言实现的高性能服务器，性能优异、配置灵活，可以使用
C、C++ 甚至 Objective-C 编写插件。

uWSGI 并不仅仅是一个 WSGI 服务器，它也可以用作 Ruby Rack 应用或者 Perl PSGI 应用的后端服务器。


### Gunicorn

[Gunicorn](gunicorn.html) 是一个类 Unix 系统上一个 Python 实现的 WSGI 服务器，性能很高使用也很简单。

作为 WSGI 服务器，Guicorn 拥有很好的性能，但是它并不擅长静态文件处理，因此在实际部署中常常隐藏于
Nginx 之后，由 Nginx 提供静态文件服务，动态请求则通过反向代理发送给 Gunicorn。


### Tornado

### Nginx/Apache


## 服务器部署

Python 应用部署的常用工具有 [Fabric]、[SaltStack]、[Ansible] 和 [Puppet]，前三者都是 Python
应用，Puppet 这是 Ruby 便携的服务器部署工具，除 Fabric 是完全开源外，后三者背后都有商业公司，
提供应用的商业版本或者应用之外的企业服务。

### Fabric

[Fabric](fabric.md) 是一个基于 SSH 的命令式部署工具，通过编写 ``fabfile`` 来扩展和定制 Fabric
的功能。不得不说，对于复杂的部署环境来说，Fabric 的部署方式已经有些落后，但是对于少量、
简单的服务环境，Fabric 使用起来简单、方便。

### Ansible

[Ansible](ansible.md) 同样是基于 SSH 的服务器运维工具。


[Redis]: http://redis.io/
[Memcached]: http://memcached.org/
[Celery]: http://www.celeryproject.org/
[RabbitMQ]: https://www.rabbitmq.com/
[nginx]: https://www.rabbitmq.com/
[Fabric]: http://fabric.readthedocs.org/
[SaltStack]: http://saltstack.com/
[Ansible]: http://www.ansible.com/
[Puppet]: https://puppetlabs.com/

