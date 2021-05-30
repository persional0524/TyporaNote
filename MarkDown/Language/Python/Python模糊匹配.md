# 模糊匹配List
```python
# 这个是python的re模块，你在文件最前面加import re;导入这个模块就可以用了
# 模糊匹配List
def fuzzyMatch():
    value = '西西'
    list = ['大海西西的','大家西西','打架','西都好快','西西大化']
    tempList = []
    pattern = '.*' + value + '.*'
    for s in list:
        obj = re.findall(pattern, s)
        if len(obj)>0:
            tempList.extend(obj)
    print(tempList) # 输出: ['大海西西的', '大家西西', '西西大化']
  
if __name__ == '__main__':
	fuzzyMatch()

```

