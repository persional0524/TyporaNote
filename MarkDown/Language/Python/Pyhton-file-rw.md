# [python 文件内容修改替换操作](https://www.cnblogs.com/yujihaia/p/7468235.html)

当我们读取文件中内容后，如果想要修改文件中的某一行或者某一个位置的内容，在python中是没有办法直接实现的，如果想要实现这样的操作只能先把文件所有的内容全部读取出来，然后进行匹配修改后写入到新的文件中。

实例代码如下所示：

 

![复制代码](Pyhton-file-rw.assets/copycode-20210525125215050.gif)

```python
# 打开旧文件
f = open('file_text.txt','r',encoding='utf-8')

# 打开新文件
f_new = open('file_text_bak.txt','w',encoding='utf-8')


# 循环读取旧文件
for line in f:
    # 进行判断
    if "Good day is good day" in line:
        line = line.replace('Good day is good day','hello,yanyan')
    # 如果不符合就正常的将文件中的内容读取并且输出到新文件中
    f_new.write(line)

f.close()
f_new.close()
```

![复制代码](Pyhton-file-rw.assets/copycode-20210525125215050.gif)

 



 

备注：

 

\1. 旧文件的内容

```
hello,world
yanyan is good girl
Good day is good day
```

\2. 新文件在代码执行后的内容

```
hello,world
yanyan is good girl
hello,yanyan
```

\3. 需要注意的是权限的问题，对于旧文件必须要有读取权限，对于新的文件必须要有写入权限





```python
# 以读的方式打开，将文件读取到内存中
with open("C:\\Users\\Administrator\\Desktop\\test.txt", "r", encoding="gbk") as f:
    #存储为一个列表的形式
    lines = f.readlines()
# 写的方式打开文件
with open("C:\\Users\\Administrator\\Desktop\\test.txt", "w", encoding="gbk") as f_w:
    for line in lines:
        if "999999999" in line:
            # 替换
            line = line.replace("999999999", "我的位置是999999")
        f_w.write(line)
```

