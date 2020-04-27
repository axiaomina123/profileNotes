## Flask-session

①状态保持session

       这个session与所有的框架和语言都无关, 就是用来实现状态保持的

cookie和session都是基础键值对的字符串信息, session是基于cookie实现的

 

②flask上下文的session

       session是请求上下文的一种,封装了用户信息,可以对数据库中缓存的用户信息进行读写操作.

 


③flask扩展包Session

       可以通过flask-session中的Session类对②中的session信息进行一系列操作,例如储存到哪个数据库中redis/memached/filesystem/mongdb/sqlalchemy. 还有过期时间等等

 


④数据库会话对象session

       flask-sqlalchemy扩展包:封装了sql对数据库的基本操作.
    
       db.session.add() /add_all( )
    
       db.session.commit()
    
       db.session.delete()
    
       db.session.rollback()

 


       ①②③的session存在的目的都是为了实现状态保持.
    
       ④只是一个扩展包封装的对象.是为了对数据库进行操作.
使用db.session.commit()提交数据后，使用model.query()查不到新增加的数据

db.session.commit()是提交了数据到数据库，但是没有刷新模型映射中的数据，也就是model.query()中的数据。
而使用db.session.query()则是 从整个服务会话中进行查询，而db.session.commit()提交的数据在这里是有刷新的。

一、问题描述
Flasky查询数据的时候，会需要用到过滤器来根据一定的条件筛选。其中，有两个方法filter和filter_by看起来很像，那么它们的区别是什么呢？

二、对比
官方文档是这样写的：

Query.filter() - filter on SQL expressions.

Query.filter_by() - filter on keyword expressions.

也就是说，它们俩接受的参数类型不一样。

1. filter接受的参数是一个类似于SQL表达式的值

u=User.query.filter(User.username=='john').first()
注意：这里要遵循Python的语法，表示相等的比较操作符是==；而且，要显式地指出username是哪个模型类的字段。

既然参数是一个表达式，它能做的就更多。请看下面的例子：

#查询出了id值大于90的所有用户
users=User.query.filter(User.id>90).all()
 2.filter_by接受的参数是关键字参数

u=User.query.filter_by(username='jessica').first()

相较于filter的表达式参数，filter_by里的关键词参数写起来更加简洁，但是它不能用>或者<这样的比较操作符了