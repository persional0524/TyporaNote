```python
#!/usr/bin/python3
# -*- encoding: utf-8 -*-
"""
@File    :   heapSort.py    
@Contact :   liutao0705@live.com
@License :   (C)Copyright 2021-2022

@Modify Time      @Author    @Version    @Desciption
------------      -------    --------    -----------
2021/4/7 10:00 下午   Lita       1.0         None
"""
import random

"""
优先队列
nlogn

sift -> logn
比快排慢一点

堆，（也叫优先队列）。python 中封装，heapq

扩展：--topk 问题
现在有n个数，设计算法找出前k大的数（k<n）

解决方法：
1、排序后切片
2、lowB三人组思想
3、？

"""

# logn 从上往下走～,向下调整，节点的左右子树都是堆，但自身不是堆
def sift(li, low, high):
    # li表示树，low表示树根， high表示最后一个节点的位置
    tmp = li[low]
    i = low
    j = 2 * i + 1  # 初始j指向空位的左孩子
    # i 指向空位，j指向两个孩子
    while j <= high:  # 循环推出的第二种情况：j>i，说明空位i是叶子节点
        # 如果右孩子存在，且比左孩子大，指向右孩子
        if j + 1 <= high and li[j] < li[j + 1]:
            j = j + 1
        if li[j] > tmp:
            li[i] = li[j]
            i = j
            j = 2 * i + 1
        else:  # 循环推出的第一种情况：j的位置比tmp小，说明两个孩子都比tmp小
            break
        li[i] = tmp


def heap_sort(li):
    n = len(li)
    # 1. 构造堆  range=前包后不包,步长-1，后不包-1，其实是0
    for low in range(n // 2 - 1, -1, -1):
        sift(li, low, n - 1)
    print(li)
    # 2. 挨个出数
    for high in range(n - 1, -1, -1):
        li[0], li[high] = li[high], li[0]  # 退休，棋子
        sift(li, 0, high - 1)


li = list(range(10))
random.shuffle(li)
print(li)
heap_sort(li)
print(li)
```

## 堆的应用

### top k 问题







---

3. 堆排序
   1. 取列表前k个元素建立一个小根堆。堆顶就是目前第k大的树。
   2. 依次向后遍历原列表，对于列表中的元素，如果小于堆顶，则忽略该元素；如果大于堆顶，将堆顶更换为该元素，并且对堆进行一次调整。
   3. 遍历列表所有元素后，倒序弹出堆顶。

