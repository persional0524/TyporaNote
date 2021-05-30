### 2340 · 计算小写字母个数（Python 版）

Python

入门

通过率80%

给定一个由大写字母和小写字母组成的字符串 `s`，输出 `s` 中小写字母的个数。

1 \le len(s) \le 401≤*l**e**n*(*s*)≤40

样例

评测机会通过执行命令 `python main.py` 来执行你的代码，并将 `s` 作为标准输入从控制台输入，你的代码需要从标准输入流（控制台）中读入数据 `s`，计算出结果后通过 `print` 语句打印到标准输出流（控制台）中。

样例一

当 `s = 'Apple'` 时，程序执行打印出的结果为：

```
4
```

样例二

当 `s = 'AAA'` 时，程序执行打印出的结果为：

```
0
```



```python
def count_upper_and_lower(s):
    li=list(s)
    j = 0
    for i in li:
        #print(i)
        if i.islower():
            j +=1
    print(j)


s = input()
count_upper_and_lower(s)
```

