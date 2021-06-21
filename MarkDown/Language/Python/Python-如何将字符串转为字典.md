在工作中遇到一个小问题，需要将一个 `python` 的字符串转为字典，比如字符串：

```python
user_info = '{"name" : "john", "gender" : "male", "age": 28}'
```

我们想把它转为下面的字典：

```python
user_dict = {"name" : "john", "gender" : "male", "age": 28}
```

有以下几种方法：

**1、通过 json 来转换**

```python
>>> import json
>>> user_info= '{"name" : "john", "gender" : "male", "age": 28}'
>>> user_dict = json.loads(user_info)
>>> user_dict
{u'gender': u'male', u'age': 28, u'name': u'john'}
```

但是使用 `json` 进行转换存在一个潜在的问题。

由于 `json` 语法规定 **数组或对象之中的字符串必须使用双引号，不能使用单引号** ([官网](http://json.org/)上有一段描述是 “A string is a sequence of zero or more Unicode characters, wrapped in double quotes, using backslash escapes” )，因此下面的转换是错误的：

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

```python
>>> import json
>>> user_info = "{'name' : 'john', 'gender' : 'male', 'age': 28}"
# 由于字符串使用单引号，会导致运行出错
>>> user_dict = json.loads(user_info)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/__init__.py", line 339, in loads
    return _default_decoder.decode(s)
  File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/decoder.py", line 364, in decode
    obj, end = self.raw_decode(s, idx=_w(s, 0).end())
  File "/usr/local/Cellar/python/2.7.11/Frameworks/Python.framework/Versions/2.7/lib/python2.7/json/decoder.py", line 380, in raw_decode
    obj, end = self.scan_once(s, idx)
ValueError: Expecting property name: line 1 column 2 (char 1)
```

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

**2、通过 eval**

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

```python
>>> user_info = '{"name" : "john", "gender" : "male", "age": 28}'
>>> user_dict = eval(user_info)
>>> user_dict
{'gender': 'male', 'age': 28, 'name': 'john'}
>>> user_info = "{'name' : 'john', 'gender' : 'male', 'age': 28}"
>>> user_dict = eval(user_info)
>>> user_dict
{'gender': 'male', 'age': 28, 'name': 'john'}
```

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

通过 `eval` 进行转换就不存在上面使用 `json` 进行转换的问题。但是，使用 `eval` 却存在`安全性的问题`，比如下面的例子:

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

```python
# 让用户输入 `user_info`
>>> user_info = raw_input('input user info: ')
# 输入 {"name" : "john", "gender" : "male", "age": 28}，没问题
>>> user_dict = eval(user_info)
# 输入 __import__('os').system('dir')，user_dict 会列出当前的目录文件！
# 再输入一些删除命令，则可以把整个目录清空了！
>>> user_dict = eval(user_info)
```

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

**3、通过 literal_eval**

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

```python
>>> import ast
>>> user = '{"name" : "john", "gender" : "male", "age": 28}'
>>> user_dict = ast.literal_eval(user)
>>> user_dict
{'gender': 'male', 'age': 28, 'name': 'john'}
user_info = "{'name' : 'john', 'gender' : 'male', 'age': 28}"
>>> user_dict = ast.literal_eval(user)
>>> user_dict
{'gender': 'male', 'age': 28, 'name': 'john'}
```

[![复制代码](Python-如何将字符串转为字典.assets/copycode-20210527105226081.gif)](javascript:void(0);)

使用 `ast.literal_eval` 进行转换既不存在使用 `json` 进行转换的问题，也不存在使用 `eval` 进行转换的 `安全性问题`，因此推荐使用 `ast.literal_eval`。