# 1. 引入json模块

```python
import json

```

# 2.使用json模块

```python
var = {'a':1,'b':2,'c':3}   #定义一个字典
print (var)
{'a': 1, 'b': 2, 'c': 3}   #未使用json输出效果
var1 = json.dumps(var,indent=4)     # indent  缩进，可自定义，4表示缩进4个空格
print (var1)
{
    "a": 1,
    "b": 2,
    "c": 3
}                        #使用json后的输出效果

```

