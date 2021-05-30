## configparser简介

1. configparser官方文档：https://docs.python.org/3/library/configparser.html
2. ini文件结构介绍：[configparser官方文档内已包含](https://docs.python.org/3/library/configparser.html#supported-ini-file-structure)
3. Python2：ConfigParser，Python3：configparser

## 测试数据

- 测试文件：test.ini

  ```ini
  [test1]
  a = 1
  b = 2.1
  c : True
  
  [test2]
  d = 11
  e = 22
  f : 33
  
  ```

  

------

## configparser使用

```python
import configparser
import os
# configparser主要使用ConfigParser类来解析ini文件
# 创建一个configparser实例
config = configparser.ConfigParser()
config.read('test.ini')	# 读取test.ini文件

```

### 基础文件读取

| 内置方法         | 含义         |
| ---------------- | ------------ |
| read（filename） | 读取指定文件 |





```python
# -*- coding: utf-8 -*-

"""
@author: rzbber
@software: PyCharm
@file: hah.py
@time: 2019/10/15 15:23
"""
import os
import configparser


def read_email_config():
    file_path = os.path.abspath(os.path.join(os.path.dirname(os.getcwd()), 'config'))
    file_name = 'test.ini'

    config = configparser.ConfigParser()
    config.read(os.path.join(file_path, file_name))
    
    print(config.read(os.path.join(file_path, file_name)))
    print(config.sections())
    print(config.options('test1'))
    print(config.items('test1'))

    print(config.get('test1', 'a'))
    print(config.get('test1', 'b'))
    print(config.get('test1', 'c'))

    print(config.getint('test1', 'a'))	# 使用该种方法获取浮点型、布尔型数据时，会报错
    print(config.getfloat('test1', 'b'))	# 使用该种方法获取整型、布尔型数据时，会报错
    print(config.getboolean('test1', 'c'))	# 使用该种方法获取整型、浮点数据时，会报错


if __name__ == '__main__':
    read_email_config()


```

　

```coffeescript
输出：

['E:\\ac_api_test\\config\\test.ini']
['test1', 'test2']
['a', 'b', 'c']
[('a', '1'), ('b', '2.1'), ('c', 'True')]
1
2.1
True
1
2.1
True

```

