# Pony ORM

[Pony](https://github.com/ponyorm/pony) 是一个很有意思的 ORM，
它的特别之处在于可以使用 Python 生成器的语法来创建数据库请求，
我们可以用这样的语句来查询数据库：

```python

select(p for p in Product if p.name.startswith('A') and p.cost <= 1000)

```

Pony 还提供了一个在线的数据库结构编辑器：[Pony Editor](https://editor.ponyorm.com/)

演示示例：[University](https://editor.ponyorm.com/user/pony/University)

注意：Pony 采用多重许可证，对于开源项目采用 GPL v3.0 许可证，非商业使用免费，
不同规模的商业使用会有不同的[收费政策](http://ponyorm.com/license-and-pricing.html)。


## 创建数据库



## 定义实体



## 添加与数据库表的映射



## 编写查询语句



## 获取对象



## 更新对象



## 数据库 Session



## 手动编写 SQL 语句



## 事务



## 实现

关于 Pony ORM 的实现，其作者在
[StackOverflow](http://stackoverflow.com/questions/16115713/how-pony-orm-does-its-tricks)
上有做过解释，这里附上中文：
[WindRunner](http://blog.windrunner.info/2015/03/pony.html)

参考链接：[官方文档](http://doc.ponyorm.com/firststeps.html)
