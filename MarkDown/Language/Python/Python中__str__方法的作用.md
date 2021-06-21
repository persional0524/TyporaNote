##### 当使用print输出对象的时候，若定义了__str__(self)方法，打印对象时就会从这个方法中打印出return的z字符串数据。__str__方法用例：

```python
class Person:
    """
    定义一个人“类”
    """
    def __init__(self,name,weight):
        self.name = name
        self.weight = weight
    def __str__(self):
        return "我的名字叫%s 体重是%.2fkg" % (self.name,self.weight)
#创建2个对象
xiaoming = Person("小明",56)
xiaomei = Person("小美",44)
print(xiaoming)
print(xiaomei)
输出结果：我的名字叫小明 体重是56.00kg
		 我的名字叫小美 体重是44.00kg

```

##### 若将__str__方法注释之后，print输出对象变量，默认情况下，是输出这个变量引用的对象是由哪一个类创建的对象，以及在内存中的地址，如

```python
class Person:
    """
    定义一个人“类”
    """
    def __init__(self,name,weight):
        self.name = name
        self.weight = weight
    #def __str__(self):
    #   return "我的名字叫%s 体重是%.2fkg" % (self.name,self.weight)

xiaoming = Person("小明",56)
print(xiaoming)
输出结果：<__main__.Person object at 0x00000000026F90F0>

```



never give up! 