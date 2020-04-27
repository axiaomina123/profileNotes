# python类方法，静态方法，实例方法总结

### 实例方法

只有实例化对象后才可调用的方法，方法内可调用类属性和方法，也可调用实例方法和属性

如：

```python
class Test():
    def instance_method(self):        # self代表实例对象，表明此方法内部可调用所有实例对象可调用的方法属性，因为此方法在类的定义中，所以也可调用对象不能调用的类的私有属性
        pass
```

其中，instance_method为实例方法，它只能通过实例化类来调用，如：

```python
t = Test()
t.instance_method()
```

### 类方法

类本身可调用实例对象亦可调用，方法内可调用类属性和方法

如：

```python
class Test():
    @classmethod
	def class_method(cls):      #  cls代表类本身，表名此方法内可调用类对象和类属性，因为此方法在类的定义中，所有也可调用类的私有属性
		pass
```

调用时，无需实例化类，类可直接调用：

```python
t = Test()
t.class_method()
Test.class_method()
```

### 静态方法

类本身可调用，实例对象亦可，方法内不允许调用类方法和类属性，也不允许调用实例方法和实例属性，继承时，会被子类的静态方法覆盖

如：

```python
class Test():
    @statismethod
	def static_method():
        pass
```

一般而言，类的静态方法中，主要写一些逻辑代码，与类本身并无关系，只是为了便于维护，为了体现归属关系等，才定义在类中。调用时如：

```
t = Test()
t.static_method()
Test.static_method()
```

