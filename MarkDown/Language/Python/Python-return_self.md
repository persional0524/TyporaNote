# python中 return self的作用

```python
class A:
    def __init__(self):
        self.c=0
    def count(self):
        self.c+=1
        print(self.c)
a=A()
a.count()
#1

```

当我们想对**实例对象**的方法进行**连续调用**时

```python
a.count().count()
AttributeError: 'NoneType' object has no attribute 'count'

```

**self其实就是实例对象本身**，那么return self 就是返回实例对象本身,这时候对实例方法进行多次调用就成功了。

```python
class A:
    def __init__(self):
        self.c = 0

    def count(self):
        self.c += 1
        print(self.c)
        return self
a = A()
a.count().count().count()
#1
#2
#3

```

