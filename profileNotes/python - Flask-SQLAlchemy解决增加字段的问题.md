## python - Flask-SQLAlchemy解决增加字段的问题（_ _abstract_ _ =True）

#### 第一种方式

```python
 class ModelMixin(object):
      def __repr__(self):
          return unicode(self.__dict__)

      @property
      def public_view(self):
          """return dict without private fields like password"""
          return model_to_dict(self, self.__class__)  
  
 class User(db.Model, ModelMixin):
      """ attributes with _  are not exposed with public_view """
      __tablename__ = "users"
      id = db.Column(db.Integer, primary_key=True)
```

#### 第二种方式

```python
from flask.ext.sqlalchemy import SQLAlchemy
db = SQLAlchemy(app)

class Base(db.Model):
    __abstract__ = True

    created_on = db.Column(db.DateTime, default=db.func.now())
    updated_on = db.Column(db.DateTime, default=db.func.now(), onupdate=db.func.now())


class User(Base):
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key = True)
    email = db.Column(db.String(255), unique = True)
```

# g对象

　　g绑定到了Local对象，是线程隔离的。该对象的作用是绑定数据，绑定的数据可以在全局使用！

