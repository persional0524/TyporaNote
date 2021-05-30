## 反省函数参数

```python
>>> from inspect import signature
>>>
>>> def foo(a:int):
...     pass
>>> sig = signature(foo)
>>> sig
<Signature (a: int)>
```

我们获得了一个Signature 实例



```ruby
>>> sig.parameters
mappingproxy(OrderedDict([('a', <Parameter "a: int">)]))
```

调用parameters就可以获得一个mappingproxy，这里不谈，OrderedDict是collections中的有序字典，我们可以通过items()来迭代



```python
>>> for name,param in sig.parameters.items():
...     name,param
...
('a', <Parameter "a: int">)
```

WOW~  我们获得了参数名称'a'和对应的Parameter 实例(包含对参数的判断方法，以及对参数的操作方法)，很酷~，当然我们可能还会想要对参数a做一些判断，例如是不是位置参数
 我们重新定义下foo



```python
def foo(a:int,b,*,c,d=3):  # * 表示之后的位置参数变为关键词参数
    pass
sig = signature(foo)
```



```ruby
>>> sig.parameters.keys()
odict_keys(['a', 'b', 'c', 'd'])
```

来做一个关键词参数判断，打印所有关键词参数



```python
>>> for name,param in sig.parameters.items():
...     if param.kind == param.KEYWORD_ONLY:  # kind表示当前参数类型，KEYWORD_ONLY表示参数只能为关键词
...         print(name)
...
c
d
```

当然，kind还有其他的类型判断



```undefined
POSITIONAL_ONLY  - 只能是位置参数
POSITIONAL_OR_KEYWORD - 位置参数或者关键词参数
VAR_POSITIONAL - 可选位置参数，对应*args
VAR_KEYWORD - 可选关键词参数，对应**kwargs
```

如果位置参数有默认值的时候，可以通过default获得值，空值参数的default为<class 'inspect._empty'>



```ruby
>>> for name,param in sig.parameters.items():
...     print (param.default)
...
<class 'inspect._empty'>
<class 'inspect._empty'>
<class 'inspect._empty'>
3
```

我们也可以自己创建Parameter实例来动态传入参数



```ruby
>>> from inspect import signature,Parameter
>>> def foo(a=1):
...     print(a)
...
>>> param = Parameter('a',Parameter.KEYWORD_ONLY,default=20)
>>> param = str(param)
>>> foo(param)
a=20
```

